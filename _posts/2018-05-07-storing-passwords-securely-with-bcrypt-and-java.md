---
title: Storing Passwords Securely With Bcrypt and Java
tags: [Java, Security]
style: border
color: fill
description: In this post, we go over how to use this password hashing function to ensure your passwords are properly encrypted and GDPR complaint.
---

{%- capture list_items -%}
Introduction
How to Use
Conclusion
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Introduction

Storing passwords are not something easy to come up with when we need to satisfy all the data protection laws, and it is getting even tougher with the rise of the [GDPR](https://eugdpr.org/), the new European regulation in data privacy. Therefore, we have to make sure all our sensitive data is encrypted and, to do that, we can use hashing algorithms.

When we want to hash passwords to store them in a database, Bcrypt is the way to go. There are many libraries for different languages, but this time I will show you how you can do it with the one included in Spring Security.

```xml
<dependency>      
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-crypto</artifactId>
</dependency>
```

**JBcrypt** hashes passwords using a version of _Bruce Schneier's Blowfish_ block cipher with modifications designed to raise the cost of off-line password cracking. The computation cost of the algorithm is parameterized, so it can be increased as computers get faster.

## How to Use

Hash password:

```java
BCrypt.hashpw(plainTextPassword, BCrypt.gensalt())
```

Where `plainTextPassword` is the password we want to hash and `BCrypt.gensalt()` is a salt to autogenerate every time.

In case we want to increment the complexity, an optional parameter (`log_rounds`) needs to be provided to `BCrypt.gensalt()`, which determines the computational complexity of the hashing. `log_rounds` is exponential (_2**log_rounds_) and says how many times to run the internal hash function. The default value is 10, and the valid values are between 4 and 31.

The output strings after running `hashpw()` will look like:

_$bcrypt_id$log_rounds$128_bits_salt184_bits_hash_

`hashpw()` is smart enough to extract the salt from the string, so you do not need to worry about the salt value anymore, only the hashed string.

Check hash password: 

```java
BCrypt.checkpw(unencryptedPassword, hashPassword)
```

This method checks that an unencrypted password matches the one that was previously hashed and ensures that it is stored somewhere.

## Conclusion

Bcrypt is 10,000 times slower than sha1 to run. If we have a machine that's able to run it in 100ms, this is probably fast enough for login, but it might be too slow if we want to execute Bcrypt against a long list of passwords. In consequence, if a hacker wants to run Bcrypt a billion times by using the same computational power, it will take 27,777 hours.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/bcrypt-service" text="Source Code" %}
</p>
