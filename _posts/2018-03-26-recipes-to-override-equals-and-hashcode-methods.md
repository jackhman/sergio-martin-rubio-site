---
title: Recipes to @Override “equals” and “hashCode” methods
tags: [Java]
style: fill
color: primary
description: Do you want to know more about Java equals and hashCode methods? Then, have a look at this recipes!
---

## equals

- Use “==” operator to compare object references. If so, return true. This is for performance optimisation since you can check this before the comparison.
- Use “instanceof” to check that both objects have same type, otherwise return false.
- Cast the argument to the class type.
- Check desired fields in the class to return true when you consider that two objects are equals.
- To compare primitive fields whose type is not “double” or “float” use “==” operator in the comparison.
- To compare objects use equals method.
- To compare “float” and “double” use Float.compare(float, float) and Double.compare(double, double) respectively.
- To improve performance, first compare fields that are more likely to differ or less expensive to compare.
- Add test unless you autogenerate or use annotations.

```java
@Override 
public boolean equals(Object o) {

    if (o == this) return true;

    if (!(o instanceof MyObject)) return false;

    MyObject mo = (MyObject)o;

    return mo.mostImportantField == mostImportantField 
        && mo.sencondField == sencondField
        && mo.lessImportantField == lessImportantField;
}
```

##hashCode

- Declare an int variable named result and initialise it with the first field.
- For primitive fields use e.g. Integer.hashCode(field)
- If the field is an object call hashCode recursively.
- If the field is an array use Arrays.hashCode.
- Combine all hash codes into the result and return result.
- Multiply result every time by 31 (odd prime which can be replaced by a shift and a subtraction for better performance).

```java
@Override
public int hashCode() {
    int result = Integer.hashCode(firstField);
    result += 31 * result + sencondField.hashCode(); 
    result += 31 * result + Integer.hashCode(thirdField); 
    return result;
}
```
 

## Additional considerations:

- Always override HashCode when overriding equals.
- lways use Object type as an argument in the equals method.
- Use @Override annotation.
- If there is no need to create a custom equals or hashCode method you only need to use @Autovalue or @EqualsAndHashCode from Google and Lombok libraries respectively.
- Alternatively, you can use autogeneration provided by your IDE.
