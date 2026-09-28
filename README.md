# Karate-Framework-Essentials

## Karate Framework: Global & Environment Variables using `karate-config.js`

### Overview

In API Automation projects, we often work with multiple environments:

- DEV
- QA
- SIT
- UAT
- STAGE
- PROD

Each environment may have different:

- Base URLs
- Credentials
- API Keys
- Authentication Tokens
- Timeouts
- Database Configurations

Karate provides a special file called:

```text
karate-config.js
```

This file acts as a centralized configuration manager and is executed before every scenario.

Think of it as Karate's equivalent of:

- `application.properties`
- `application.yml`
- Java Properties Files

---

### Why Use karate-config.js?

Without configuration:

```gherkin
Given url 'https://dev-api.company.com'
```

Problems:

- Hardcoded URLs
- Difficult maintenance
- Duplicate configuration
- Environment switching becomes painful

With `karate-config.js`:

```javascript
function fn() {

    var config = {
        baseUrl: 'https://dev-api.company.com'
    };

    return config;
}
```

Usage:

```gherkin
Given url baseUrl
```

### Benefits

- Centralized configuration
- Easy environment switching
- Reusable variables
- Cleaner feature files
- Better maintainability
- Enterprise-friendly approach

---

### Project Structure

```text
src
└── test
    └── java
        ├── features
        ├── runners
        └── karate-config.js
```

Karate automatically loads the file from the classpath.

---

### Basic Syntax

```javascript
function fn() {

    var config = {

        baseUrl: 'https://api.demo.com',
        username: 'admin',
        password: 'admin123'

    };

    return config;
}
```

---

### Accessing Variables in Feature Files

### karate-config.js

```javascript
function fn() {

    var config = {
        baseUrl: 'https://api.demo.com'
    };

    return config;
}
```

### Feature File

```gherkin
Feature: Get Users

Scenario: Get All Users

Given url baseUrl
And path 'users'
When method GET
Then status 200
```

Variables become globally available across all feature files.

---

### Defining Multiple Global Variables

```javascript
function fn() {

    var config = {

        baseUrl: 'https://api.demo.com',
        apiVersion: 'v1',
        username: 'admin',
        password: 'manager',
        timeout: 10000

    };

    return config;
}
```

Usage:

```gherkin
* print baseUrl
* print apiVersion
* print timeout
```

---

### Understanding karate.env

Karate provides a built-in environment variable:

```javascript
karate.env
```

It identifies the current execution environment.

Example:

```javascript
var env = karate.env;
```

Possible values:

```text
dev
qa
sit
uat
prod
```

---

### Setting a Default Environment

```javascript
function fn() {

    var env = karate.env;

    if (!env) {
        env = 'dev';
    }

    karate.log('Current Environment:', env);

    return {};
}
```

Output:

```text
Current Environment: dev
```

---

### Environment-Specific Configuration

```javascript
function fn() {

    var env = karate.env || 'dev';

    var config = {};

    if (env == 'dev') {
        config.baseUrl = 'https://dev-api.company.com';
    }

    if (env == 'qa') {
        config.baseUrl = 'https://qa-api.company.com';
    }

    if (env == 'uat') {
        config.baseUrl = 'https://uat-api.company.com';
    }

    return config;
}
```

---

### Running Tests for Different Environments

### DEV

```bash
mvn test -Dkarate.env=dev
```

### QA

```bash
mvn test -Dkarate.env=qa
```

### UAT

```bash
mvn test -Dkarate.env=uat
```

### PROD

```bash
mvn test -Dkarate.env=prod
```

---

### Real Enterprise Example

```javascript
function fn() {

    var env = karate.env || 'dev';

    var config = {

        connectTimeout: 30000,
        readTimeout: 30000

    };

    if (env == 'dev') {

        config.baseUrl = 'https://dev-api.company.com';
        config.username = 'dev-user';
        config.password = 'dev-pass';

    } else if (env == 'qa') {

        config.baseUrl = 'https://qa-api.company.com';
        config.username = 'qa-user';
        config.password = 'qa-pass';

    } else if (env == 'prod') {

        config.baseUrl = 'https://prod-api.company.com';

    }

    return config;
}
```

---

### Logging Current Environment

```javascript
karate.log('Environment =', env);
```

Output:

```text
Environment = QA
```

Useful for debugging CI/CD runs.

---

### Reading Java System Properties

Pass values from Maven:

```bash
mvn test -Dbuild.number=1234
```

Read inside Karate:

```javascript
function fn() {

    var config = {

        buildNumber:
            java.lang.System.getProperty('build.number')

    };

    return config;
}
```

Usage:

```gherkin
* print buildNumber
```

---

### Reading Environment Variables

Useful for secrets.

```javascript
function fn() {

    var config = {

        token:
            java.lang.System.getenv('AUTH_TOKEN')

    };

    return config;
}
```

Linux:

```bash
export AUTH_TOKEN=abcdef123
```

Windows:

```cmd
set AUTH_TOKEN=abcdef123
```

---

### Dynamic Values in Configuration

### UUID

```javascript
function fn() {

    var config = {

        requestId:
            java.util.UUID.randomUUID() + ''

    };

    return config;
}
```

Feature:

```gherkin
* print requestId
```

Output:

```text
af31e488-e5c7-4cd9-8e13-123456789abc
```

---

### Current Timestamp

```javascript
function fn() {

    var config = {

        timestamp:
            new Date().getTime()

    };

    return config;
}
```

---

### Global Request Headers

```javascript
function fn() {

    var config = {

        headers: {

            Accept: 'application/json',
            ContentType: 'application/json'

        }

    };

    return config;
}
```

Usage:

```gherkin
Given headers headers
```

---

### Reusable Authentication Token

```javascript
function fn() {

    var config = {

        token: 'Bearer abcdefghijklmnop'

    };

    return config;
}
```

Usage:

```gherkin
And header Authorization = token
```

---

### Reusable Helper Functions

You can also define JavaScript utility methods.

```javascript
function fn() {

    var config = {};

    config.generateEmail = function() {

        return 'user' +
               new Date().getTime() +
               '@gmail.com';

    };

    return config;
}
```

Feature:

```gherkin
* def email = generateEmail()
* print email
```

Output:

```text
user1693454567@gmail.com
```

---

### Loading Environment Config from JSON

### dev.json

```json
{
  "baseUrl": "https://dev-api.company.com"
}
```

### qa.json

```json
{
  "baseUrl": "https://qa-api.company.com"
}
```

### karate-config.js

```javascript
function fn() {

    var env = karate.env || 'dev';

    var config =
        karate.read('classpath:config/' + env + '.json');

    return config;
}
```

Enterprise projects commonly follow this pattern.

---

### Recommended Enterprise Project Structure

```text
src/test/java

├── features
├── runners
├── testdata
├── utils
├── config
│   ├── dev.json
│   ├── qa.json
│   ├── uat.json
│   └── prod.json
└── karate-config.js
```

---

### Common Mistakes

1. Hardcoding URLs

❌ Bad

```gherkin
Given url 'https://qa-api.company.com'
```

✅ Good

```gherkin
Given url baseUrl
```

---

2. Hardcoding Credentials

❌ Bad

```javascript
username = "admin";
password = "admin123";
```

✅ Good

```javascript
System.getenv('USERNAME')
```

---

3. No Default Environment

❌ Bad

```javascript
var env = karate.env;
```

✅ Good

```javascript
var env = karate.env || 'dev';
```

---

4. Huge Config File

Avoid storing:

- Test Data
- Request Bodies
- Large JSON Objects

inside `karate-config.js`.

Keep it focused on configuration.

---

### Best Practices

1. Use Default Environment

```javascript
var env = karate.env || 'dev';
```

---

2. Keep Secrets Outside Source Control

Use:

```javascript
System.getenv()
```

instead of hardcoding passwords.

---

3. Log Environment During Startup

```javascript
karate.log('Running Against:', env);
```

---

4. Use Separate JSON Config Files

Good for:

- Scalability
- Maintainability
- Team collaboration

---

5. Centralize Common Headers

```javascript
config.headers = {

    Accept: 'application/json',
    ContentType: 'application/json'

};
```

---

6. Keep Feature Files Clean

Avoid:

```gherkin
* def baseUrl = 'https://qa-api.company.com'
```

Use centrally managed configuration.

---

7. Use Dynamic Test Data

Generate:

- UUIDs
- Emails
- Phone Numbers
- Customer IDs

inside utility functions.

---

### Advanced Example

```javascript
function fn() {

    var env = karate.env || 'dev';

    var config = {

        connectTimeout: 30000,
        readTimeout: 30000,
        requestId: java.util.UUID.randomUUID() + ''
    };

    if (env == 'dev') {

        config.baseUrl =
            'https://dev-api.company.com';

    } else if (env == 'qa') {

        config.baseUrl =
            'https://qa-api.company.com';

    }

    config.commonHeaders = {

        Accept: 'application/json',
        ContentType: 'application/json'
    };

    config.generateEmail = function() {

        return 'user' +
               new Date().getTime() +
               '@gmail.com';

    };

    return config;
}
```

---

### Interview Questions

### Q1. What is karate-config.js?

**Answer:**

`karate-config.js` is a special configuration file executed before every scenario and used to define:

- Global variables
- Environment-specific values
- Utility functions
- Common headers
- Authentication tokens

---

### Q2. When is karate-config.js executed?

**Answer:**

Before every Scenario execution.

---

### Q3. What is the purpose of `karate.env`?

**Answer:**

It identifies the environment in which the test is running.

Example:

```text
dev
qa
uat
prod
```

---

### Q4. How do you switch environments in Karate?

**Answer:**

```bash
mvn test -Dkarate.env=qa
```

---

### Q5. How can you create globally accessible variables?

**Answer:**

Return them inside the config object.

```javascript
var config = {

    baseUrl: 'https://api.company.com'

};

return config;
```

---

### Q6. How do you read system properties?

**Answer:**

```javascript
java.lang.System.getProperty('property.name')
```

---

### Q7. How do you read OS environment variables?

**Answer:**

```javascript
java.lang.System.getenv('TOKEN')
```

---

### Q8. What are the benefits of using karate-config.js?

**Answer:**

- Reusability
- Maintainability
- Clean feature files
- Environment switching
- Secure configuration management

---

### Q9. Can we define functions inside karate-config.js?

**Answer:**

Yes.

Example:

```javascript
config.generateEmail = function() {

    return 'test@gmail.com';

};
```

---

### Q10. Which approach is preferred in enterprise projects?

**Answer:**

Store environment-specific data in:

```text
dev.json
qa.json
uat.json
prod.json
```

and load them dynamically using `karate.env`.

---

### Quick Cheat Sheet

```javascript
function fn() {

    var env = karate.env || 'dev';

    karate.log('Environment:', env);

    var config = {};

    if (env == 'dev') {

        config.baseUrl =
            'https://dev-api.company.com';

    } else if (env == 'qa') {

        config.baseUrl =
            'https://qa-api.company.com';
    }

    config.token =
        java.lang.System.getenv('TOKEN');

    return config;
}
```

Run:

```bash
mvn test -Dkarate.env=qa
```

Use:

```gherkin
Given url baseUrl
And header Authorization = token
When method GET
Then status 200
```

---

### Key Takeaways

- `karate-config.js` is Karate's central configuration file.
- Variables returned from `config` become globally available.
- `karate.env` enables environment-specific execution.
- Avoid hardcoded URLs and credentials.
- Use environment variables for secrets.
- Prefer JSON-based environment configurations in enterprise projects.
- Keep your framework scalable, secure, and maintainable.

## Calling Any Java Class in a Karate Feature File

### Overview

Karate allows you to invoke Java classes and methods directly from a feature file. This is useful when:

- Reusable utility methods are required.
- Complex business logic is easier to implement in Java.
- Data transformation or custom calculations are needed.
- Existing Java libraries need to be reused in test automation.

---

### Why Use Java Classes in Karate?

- Reuse existing Java code
- Perform complex calculations
- Generate dynamic test data
- Work with dates and time
- Integrate with databases
- Implement encryption/decryption
- Keep feature files clean and readable

---

### Loading a Java Class

```karate
* def Calculator = Java.type('utils.Calculator')
```

`Java.type()` loads a Java class into the Karate runtime.

---

### Calling Static Methods

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

### Calling Instance Methods

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

### Passing Parameters

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

### Returning Collections

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

### Date Utility Example

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

### Random Data Generation

```java
public static String randomEmail() {
    return UUID.randomUUID() + "@test.com";
}
```

```karate
* def email = RandomUtil.randomEmail()
```

---

### API Testing Example

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

### Common Use Cases

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

### Best Practices

1. Keep feature files readable.
2. Put complex logic in Java classes.
3. Prefer static methods for utility classes.
4. Create focused utility classes.
5. Reuse existing framework libraries.
6. Avoid embedding large JavaScript blocks inside Karate features.

---

### Common Mistakes

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

### Interview Questions

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

### Key Takeaways

- Use `Java.type()` to load Java classes.
- Static methods can be called directly.
- Instance methods require object creation.
- Java utilities improve reusability and maintainability.
- Existing Java frameworks can be reused inside Karate.
- Ideal for utilities, encryption, database validation, and test-data generation.
