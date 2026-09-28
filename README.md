# Karate-Framework-Essentials


## Calling Any Java Class in a Karate Feature File

### Overview

Karate allows you to invoke Java classes and methods directly from a feature file. This is useful when:

- Reusable utility methods are required.
- Complex business logic is easier to implement in Java.
- Data transformation or custom calculations are needed.
- Existing Java libraries need to be reused in test automation.

---

## Why Use Java Classes in Karate?

- Reuse existing Java code
- Perform complex calculations
- Generate dynamic test data
- Work with dates and time
- Integrate with databases
- Implement encryption/decryption
- Keep feature files clean and readable

---

## Loading a Java Class

```karate
* def Calculator = Java.type('utils.Calculator')
```

`Java.type()` loads a Java class into the Karate runtime.

---

## Calling Static Methods

### Java Class

```java
package utils;

public class Calculator {

    public static int add(int a, int b) {
        return a + b;
    }
}
```

### Karate Feature

```karate
* def Calculator = Java.type('utils.Calculator')
* def result = Calculator.add(10, 20)
* print result
```

Output:

```text
30
```

---

## Calling Instance Methods

### Java Class

```java
package utils;

public class Calculator {

    public int multiply(int a, int b) {
        return a * b;
    }
}
```

### Karate Feature

```karate
* def Calculator = Java.type('utils.Calculator')
* def calculator = new Calculator()
* def result = calculator.multiply(10, 5)
```

---

## Passing Parameters

```java
public class UserUtils {

    public static String getFullName(String firstName,
                                     String lastName) {
        return firstName + " " + lastName;
    }
}
```

```karate
* def UserUtils = Java.type('utils.UserUtils')
* def name = UserUtils.getFullName('Satya', 'Nandy')
```

---

## Returning Collections

### Returning a List

```java
public static List<String> countries() {
    return Arrays.asList("India", "Australia", "USA");
}
```

```karate
* def countries = CountryUtil.countries()
```

### Returning a Map

```java
public static Map<String, Object> getUser() {
    Map<String, Object> user = new HashMap<>();
    user.put("id", 101);
    user.put("name", "Satya");
    return user;
}
```

```karate
* def user = UserUtil.getUser()
* match user.id == 101
```

---

## Date Utility Example

```java
public static String today() {
    return LocalDate.now().toString();
}
```

```karate
* def DateUtil = Java.type('utils.DateUtil')
* def today = DateUtil.today()
```

---

## Random Data Generation

```java
public static String randomEmail() {
    return UUID.randomUUID() + "@test.com";
}
```

```karate
* def email = RandomUtil.randomEmail()
```

---

## API Testing Example

```karate
* def RandomUtil = Java.type('utils.RandomUtil')
* def email = RandomUtil.randomEmail()

Given url baseUrl
And request
"""
{
  "name": "Satya",
  "email": "#(email)"
}
"""
When method post
Then status 201
```

---

## Common Use Cases

### Date Operations
- Current date generation
- Date formatting
- Date calculations

### Database Utilities
- Execute SQL queries
- Validate records
- Fetch test data

### Encryption Utilities
- Encrypt sensitive values
- Generate tokens
- Create signatures

### File Utilities
- Read file contents
- Generate reports
- Process CSV/Excel files

---

## Best Practices

1. Keep feature files readable.
2. Put complex logic in Java classes.
3. Prefer static methods for utility classes.
4. Create focused utility classes.
5. Reuse existing framework libraries.
6. Avoid embedding large JavaScript blocks inside Karate features.

---

## Common Mistakes

### Incorrect Package Name

```karate
* def Util = Java.type('util.DateUtil')
```

Correct:

```karate
* def Util = Java.type('utils.DateUtil')
```

### Calling Instance Method as Static

Incorrect:

```karate
* def result = Calculator.multiply(2, 3)
```

Correct:

```karate
* def calculator = new Calculator()
* def result = calculator.multiply(2, 3)
```

---

## Interview Questions

### How do you call a Java class in Karate?

```karate
* def Util = Java.type('utils.Util')
```

### How do you call a static Java method?

```karate
* def value = Util.methodName()
```

### How do you call a non-static method?

```karate
* def util = new Util()
* def value = util.methodName()
```

---

## Key Takeaways

- Use `Java.type()` to load Java classes.
- Static methods can be called directly.
- Instance methods require object creation.
- Java utilities improve reusability and maintainability.
- Existing Java frameworks can be reused inside Karate.
- Ideal for utilities, encryption, database validation, and test-data generation.
