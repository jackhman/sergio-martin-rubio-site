---
title: Data Migration with Spring Batch
tags: [Java, Spring Batch, Migration, Database]
style: border
color: primary
description: Spring Batch provides reusable functions that are essential in processing large volumes of records.
---

[Spring Batch](https://docs.spring.io/spring-batch/trunk/reference/html/) is a batch framework designed to process large volumes of data. We can run either chunks or tasklets (single task per step).

This framework is lightweight and very easy to use, so it’s very recommended for task that will run for a long time and we want to forget about it.

## Features

- Stop/start/restart jobs and maintain state between executions.
- Skip and retry records as they are being processed.
- Transaction management. Spring Batch handles transactions for us.
- The ability to notify other systems when errors occur via messaging by integrating Spring Integration.
- Java or XML based configuration.
- All the Spring features like DI, AOP, testability, etc.
- It is open source.
- Many scalable options and allows us to use multithreding.
- Support for big data.

## Use Case
Spring Batch allows us to run databases migration without hassle. For this example we want to migrate our plain text passwords to BCrypt. To achieve that we will have to:

- Read from database.
- Write plain text passwords on a backup table.
- Hash plain text passwords.
- Update passwords on the original table.

## Getting Started

1. **Add dependency** `spring-boot-starter-batch` and create configuration class with readers, writers, steps and job. The configuration class needs to be annotated with `@Configuration` and `@EnableBatchProcessing`.
2. **Create reader**: readers are responsible for getting the data from a source such as a file or a database.

```java
@Bean
public JdbcCursorItemReader<Credentials> reader(DataSource dataSource) {
    return new JdbcCursorItemReaderBuilder<Credentials>()
            .name("credentialsReader")
            .dataSource(dataSource)
            .sql(SQL_SELECT_QUERY)
            .rowMapper(new CredentialsRowMapper())
            .build();
}
```

We set up the data source on the properties file to point to our **MySQL** database. A row mapper is also required, so we can map the database columns with our `Credentials` object.

3. **Create writer**: writers are responsible for saving the modified data into a source.

```java
@Bean
public JdbcBatchItemWriter<Credentials> writer1(DataSource dataSource) {
    return new JdbcBatchItemWriterBuilder<Credentials>()
            .itemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<>())
            .sql(SQL_INSERT_QUERY)
            .dataSource(dataSource)
            .build();
}
@Bean
public JdbcBatchItemWriter<Credentials> writer2(DataSource dataSource) {
    return new JdbcBatchItemWriterBuilder<Credentials>()
            .itemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<>())
            .sql(SQL_UPDATE_QUERY)
            .dataSource(dataSource)
            .build();
}
```

The first writer will be responsible for saving the plain text password on the backup table, and the second one will replace the plain text password with the hashed password. The `itemSqlParameterSourceProvider` allows to use database columns name to match object names.

4. **Create steps**: steps define the flow of the batch.

```java
@Bean
public Step step1(JdbcBatchItemWriter<Credentials> writer1, JdbcCursorItemReader<Credentials> reader) {
    return stepBuilderFactory.get("step1")
            .<Credentials, Credentials>chunk(4)
            .reader(reader)
            .writer(writer1)
            .build();
}
@Bean
public Step step2(JdbcBatchItemWriter<Credentials> writer2, JdbcCursorItemReader<Credentials> reader) {
    return stepBuilderFactory.get("step2")
            .<Credentials, Credentials>chunk(4)
            .reader(reader)
            .processor(processor())
            .writer(writer2)
            .build();
}
```

Both of the steps will run chunks of 4 rows. During the **step1** we will first use the previous reader and the writer to save the plain text password into the db; whereas on the **step2** we will also apply a processor to do the hashing before updating the password.

5. **Create processor**: processors are responsible for making changes on the data.

```java
@Bean
public PasswordProcessor processor() {
    return new PasswordProcessor();
}
```

and this processor is defined on the `PasswordProcessor` class:

```java
@Slf4j
public class PasswordProcessor implements ItemProcessor<Credentials, Credentials> {
    private static final int BCRYPT_ROUNDS = 13;
    @Override
    public Credentials process(Credentials credentials) throws Exception {
        final String hashPassword = hashPassword(credentials.getPassword());
        log.info("hashing password ("  + credentials.getPassword() + ") into (" + hashPassword + ")");
        return new Credentials(credentials.getId(), hashPassword);
    }
    private String hashPassword(String password) {
        return BCrypt.hashpw(password, BCrypt.gensalt(BCRYPT_ROUNDS));
    }
}
```

6. **Create job**: jobs are responsible for running the different steps, as well as listeners and other options for the steps.

```java
@Bean
public Job credentialsJob(JobCompletionNotificationListener listener, Step step1, Step step2) {
    return jobBuilderFactory.get("credentialsJob")
            .incrementer(new RunIdIncrementer())
            .listener(listener)
            .start(step1)
            .next(step2)
            .preventRestart()
            .build();
}
```

The job uses an incrementer to keep track of the current chunk, so we are able to stop and continue the batch. We also use a listener to log some information about the batch state. `PreventRestart` prevents restart of the job even if it fails.

>Note: To continue a job from the latest state we will have to create a few tables or `setspring.batch.initialize-schema=always` on our properties file.

```bash
mysql> show tables;
+------------------------------+
| Tables_in_batch              |
+------------------------------+
| BATCH_JOB_EXECUTION          |
| BATCH_JOB_EXECUTION_CONTEXT  |
| BATCH_JOB_EXECUTION_PARAMS   |
| BATCH_JOB_EXECUTION_SEQ      |
| BATCH_JOB_INSTANCE           |
| BATCH_JOB_SEQ                |
| BATCH_STEP_EXECUTION         |
| BATCH_STEP_EXECUTION_CONTEXT |
| BATCH_STEP_EXECUTION_SEQ     |
| DATABASECHANGELOG            |
| DATABASECHANGELOGLOCK        |
| credentials                  |
| credentials_backup           |
+------------------------------+
13 rows in set (0.00 sec)
```

 7. **Listeners**: responsible for providing information about the batches.

```java
@Slf4j
@Component
@AllArgsConstructor
public class JobCompletionNotificationListener extends JobExecutionListenerSupport {
    private static final String SQL_SELECT_CREDENTIALS = "SELECT * FROM credentials";
    private static final String SQL_SELECT_CREDENTIALS_BACKUP = "SELECT * FROM credentials_backup";
    private final JdbcTemplate jdbcTemplate;
    @Override
    public void afterJob(JobExecution jobExecution) {
        if(jobExecution.getStatus() == BatchStatus.COMPLETED) {
            log.info("HASHING IS DONE!!!");
            jdbcTemplate.query(SQL_SELECT_CREDENTIALS,
                    (rs, row) -> new Credentials(
                            rs.getString(1),
                            rs.getString(2))
            ).forEach(credentials -> log.info("New credentials -> " + credentials));
            jdbcTemplate.query(SQL_SELECT_CREDENTIALS_BACKUP,
                    (rs, row) -> new Credentials(
                            rs.getString(1),
                            rs.getString(2))
            ).forEach(credentials -> log.info("Backup credentials -> " + credentials));
        }
    }
}
```

<p class="text-center">
{% include button.html link="https://github.com/smartinrub/spring-batch-demo" text="Source Code" %}
</p>
