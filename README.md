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

## Karate Framework: Embedded Expressions, Variables & JsonPath in Dynamic JSON Payloads

### Overview

Embedded expressions are one of Karate's most powerful features because they allow test data, variables, and JsonPath results to be injected directly into JSON structures without manual string concatenation.

### Introduction

API automation often requires sending dynamic request payloads.

Examples:

- Customer IDs generated during execution
- Order references returned by previous APIs
- Tokens generated during authentication
- Dynamic product identifiers

Karate solves this elegantly using:

- Variables
- Embedded Expressions
- JsonPath Expressions

---

### What are Embedded Expressions?

### Definition

Embedded expressions allow JavaScript expressions to be evaluated inside JSON or XML using the syntax:

```karate
#(expression)
```

Karate evaluates the expression and replaces it with the resulting value.

### Why it is used

Without embedded expressions:

```karate
* def payload = '{"id":"' + customerId + '"}'
```

With embedded expressions:

```karate
* def payload =
"""
{
  "id": "#(customerId)"
}
"""
```

Cleaner and easier to maintain.

---

### Variables in Karate

### What are Variables?

Variables store reusable data.

### Syntax

```karate
* def customerId = 1001
* def customerName = 'John'
```

### Example

```karate
Feature: Variables Example

Scenario: Create variables

    * def id = 101
    * def name = 'John'

    * print id
    * print name
```

---

### Using Embedded Expressions in JSON

### Example

```karate
* def customerId = 111
* def customerName = 'Alex'

* def requestBody =
"""
{
  "id": #(customerId),
  "name": "#(customerName)"
}
"""
```

Result:

```json
{
  "id": 111,
  "name": "Alex"
}
```

### Important Note

Numbers should not be enclosed in quotes if numerical type is desired.

Correct:

```karate
"id": #(customerId)
```

Incorrect:

```karate
"id": "#(customerId)"
```

---

### Using JsonPath Inside Embedded Expressions

## What is JsonPath?

JsonPath extracts values from JSON documents.

### Why it is Used

To reuse response values in subsequent requests.

### Example Response

```json
{
  "customer": {
    "id": 2001,
    "name": "John"
  }
}
```

Extract value:

```karate
* def customerId = response.customer.id
```

or

```karate
* def customerId = get response.customer.id
```

Use inside payload:

```karate
* def payload =
"""
{
  "customerId": #(customerId)
}
"""
```

---

### Dynamic Payload Creation

### Scenario

Create Customer

```karate
Given url baseUrl + '/customers'
And request
"""
{
   "name": "John"
}
"""
When method post
Then status 201

* def customerId = response.id
```

Create Order

```karate
* def orderRequest =
"""
{
   "customerId": #(customerId),
   "product": "iPhone"
}
"""
```

---

### Embedded Expressions with Objects

```karate
* def address =
{
   city: 'Bangalore',
   country: 'India'
}

* def payload =
"""
{
   "address": #(address)
}
"""
```

---

### Embedded Expressions with Arrays

```karate
* def roles = ['ADMIN', 'USER']

* def requestBody =
"""
{
   "roles": #(roles)
}
"""
```

---

### Conditional Dynamic Values

```karate
* def isPremium = true

* def payload =
"""
{
   "discount": #(isPremium ? 20 : 0)
}
"""
```

---

### Dynamic Dates

```karate
* def currentDate = new Date().toISOString()

* def payload =
"""
{
   "createdDate": "#(currentDate)"
}
"""
```

---

### Using External Test Data

```karate
* def testData = read('customer.json')

* def payload =
"""
{
   "customerId": #(testData.customerId),
   "name": "#(testData.name)"
}
"""
```

---

### Enterprise Real World Scenarios

### Telecom Order Management

```karate
{
   "serviceId": #(response.service.id),
   "productCode": "POSTPAID"
}
```

### Cart Management

```karate
{
   "cartId": #(cartResponse.id),
   "customerId": #(customerId)
}
```

### Authentication Flow

```karate
{
   "token": "#(authToken)"
}
```

### Inventory Management

```karate
{
   "sku": "#(sku)",
   "quantity": #(availableQty)
}
```

---

### Common Mistakes

### Mixing Strings and Numbers

Wrong:

```karate
"id":"#(id)"
```

### Undefined Variables

Wrong:

```karate
"id": #(customerId)
```

if variable not initialized.

### Incorrect JsonPath

```karate
response.customer.id
```

Verify path exists before use.

---

### Best Practices (Enterprise Projects)

1. Keep payload templates separate from feature files.
2. Store reusable payloads under a payloads folder.
3. Use embedded expressions instead of string concatenation.
4. Avoid hardcoded IDs.
5. Reuse response values through variables.
6. Validate extracted JsonPath values before consumption.
7. Use meaningful variable names.
8. Centralize common test data.
9. Use environment-specific configuration files.
10. Avoid deeply nested dynamic expressions.
---

### Interview Questions and Answers

### What is an embedded expression?
An expression evaluated using #(expression) inside JSON or XML.

### Why use embedded expressions?
To create dynamic payloads without string manipulation.

### How do you define a variable?

```karate
* def id = 100
```
### What is JsonPath?
A syntax used to extract data from JSON.

### Difference between variable substitution and embedded expression?
Embedded expressions evaluate JavaScript and objects while substitution primarily injects values.

### Can embedded expressions inject arrays?
Yes.

```karate
"roles": #(roles)
```

### Can you use a response value in another request?
Yes using variables and JsonPath.

### How does Karate preserve JSON types?
Embedded expressions keep numeric, boolean, array and object types intact.

### When should embedded expressions not be used?
Avoid excessive logic inside payloads.

### How can dynamic payloads be shared across features?
Using reusable feature files and common payload templates.

## Scenario Based Questions

### Customer API returns an ID. How would you create an Order API request?

```karate
* def customerId = response.id

* def orderRequest =
"""
{
   "customerId": #(customerId)
}
"""
```

### How would you generate unique emails?

```karate
* def email = 'user' + java.lang.System.currentTimeMillis() + '@test.com'
```

### How do you handle environment-specific data?
Use karate-config.js and environment configurations.

---
### Summary
Embedded Expressions are one of the most important Karate Framework capabilities for building dynamic API requests. They provide a clean, maintainable, and type-safe way of injecting variables, JsonPath results, arrays, objects, and calculated values into JSON payloads. Combined with reusable test data and sound framework design, they enable highly scalable enterprise API automation solutions.

## Karate Framework - JsonPath Expressions (Jayway JsonPath)


### What is JsonPath?
JsonPath is a query language used to extract data from JSON documents.

### Why is it used?
- Simplifies JSON parsing
- Avoids complex loops
- Makes validations concise
- Improves test readability

### Example JSON
```json
{
  "customer": {
    "id": 101,
    "name": "John"
  }
}
```

### Example
```karate
* def name = response.customer.name
* match name == 'John'
```

---

### Karate and Jayway JsonPath

Karate internally uses Jayway JsonPath.

```karate
* def name = karate.jsonPath(response, '$.customer.name')
```

Use it when dynamic path evaluation is needed.

---

### karate.get()

### What is it?
Reads JSON using path notation.

### Example
```karate
* def city = karate.get('response.customer.address.city')
```

### Why use it?
- Cleaner syntax
- Easier to read

---

### karate.jsonPath()

## What is it?
Runs JsonPath expressions on JSON.

```karate
* def id = karate.jsonPath(response, '$.customer.id')
```

---

### Array Access

```json
{
  "users": [
    {"id":1,"name":"John"},
    {"id":2,"name":"Mary"}
  ]
}
```

```karate
* def userName = karate.jsonPath(response,'$.users[0].name')
```

---

### Filter Expressions

```karate
* def premium = karate.jsonPath(response,"$.users[?(@.type=='PREMIUM')]")
```

Real-world usage:
- Get active subscriptions
- Find successful orders
- Validate product types

---

### Nested JSON

```karate
* def zip = karate.jsonPath(response,'$.customer.address.zipcode')
```

---

### Extract Collection Values

```karate
* def ids = karate.jsonPath(response,'$.users[*].id')
```

---

### Assertion Examples

```karate
* match karate.jsonPath(response,'$.customer.id') == 101
```

```karate
* match karate.jsonPath(response,'$.users[*].name') contains 'John'
```

---

### Real World Example

```json
{
 "cartItems":[
  {"productType":"Mobile"},
  {"productType":"Accessory"}
 ]
}
```

```karate
* def items = karate.jsonPath(response,"$.cartItems[?(@.productType=='Mobile')]")
* match items.length == 1
```

---

### Common JsonPath Expressions

| Expression | Purpose |
|------------|---------|
| $.id | root field |
| $.users[0] | first element |
| $.users[*] | all elements |
| $.users[*].name | all names |
| $..name | recursive search |
| $.users[?(@.active==true)] | filter |

---

### Best Practices

1. Prefer direct property access for simple validations.
2. Use JsonPath only when complexity increases.
3. Avoid deeply nested assertions.
4. Store extracted values in variables.
5. Keep feature files readable.

---

### Interview Questions and Answers

### What is JsonPath?
A query language for extracting values from JSON.

### Why is JsonPath useful?
It simplifies reading and validating JSON payloads.

### Difference between XPath and JsonPath?
XPath is for XML, JsonPath is for JSON.

### What is karate.jsonPath()?
A Karate utility method that evaluates JsonPath expressions.

### What is karate.get()?
A simpler path-based accessor.

### How do you filter JSON arrays?
```karate
$.items[?(@.status=='ACTIVE')]
```

### When should you prefer JsonPath over direct access?
For filtering, dynamic queries, and complex traversal.

### How can JsonPath improve maintainability?
It removes manual iteration logic and keeps tests concise.

### How do you validate nested arrays?
Using wildcard and filter expressions.

### Validate successful orders only.
```karate
* def completed = karate.jsonPath(response,"$.orders[?(@.status=='COMPLETED')]")
* match completed.length > 0
```
### Validate existence of premium customer.
```karate
* def premium = karate.jsonPath(response,"$.customers[?(@.segment=='PREMIUM')]")
* match premium.length == 1
```
### Validate telecom cart line items.
```karate
* def mobiles = karate.jsonPath(response,"$.cartItems[?(@.productType=='Mobile')]")
* match mobiles.length > 0
```

---

### Key Takeaways

- Karate uses Jayway JsonPath.
- karate.get() is simpler for straightforward access.
- karate.jsonPath() handles advanced filtering and extraction.
- JsonPath greatly improves API automation readability.
- Filter expressions are heavily used in enterprise API testing.
- Combining JsonPath and Karate assertions leads to concise and maintainable tests.

## Karate Framework Data-Driven Testing Complete Guide

### Introduction to Data-Driven Testing

### What is it?
Data-driven testing allows the same scenario to be executed multiple times with different data sets.

### Why use it?
- Reduce duplication
- Improve maintainability
- Increase test coverage
- Separate test logic from test data

### Example
```gherkin
Scenario Outline: Verify User
Given url baseUrl
And path 'users', '<id>'
When method get
Then status 200
And match response.id == <id>

Examples:
| id |
| 1  |
| 2  |
| 3  |
```

---

### Scenario Outline

### What is it?
A reusable scenario template executed once per row in the Examples table.

### Why use it?
Avoid creating multiple identical scenarios.

```gherkin
Scenario Outline: Login Test
* print username

Examples:
| username |
| admin |
| user1 |
```

---

### Examples Table

Provides input values for each execution.

```gherkin
Examples:
| username | password |
| admin    | admin123 |
| test     | test123  |
```

---

### Magic Variables

### __row
Returns the current row as JSON.

```gherkin
Scenario Outline:
* print __row

Examples:
| id | name |
| 1  | John |
```

Output:
```json
{ "id":1, "name":"John" }
```

### __num
Current iteration number.

```gherkin
* print __num
```

---

### Auto Variables

Every column becomes a variable automatically.

```gherkin
Examples:
| userId |
| 100    |
```

```gherkin
* print userId
```

---

### Embedded Expressions

Use Karate expressions directly inside Examples.

```gherkin
Examples:
| id | expected |
| 1  | #(1+1)   |
```

---

### Variables Inside Examples

```gherkin
* def prefix = 'user'

Scenario Outline:
* print name

Examples:
| name |
| #(prefix + '1') |
| #(prefix + '2') |
```

---

### Exclamation Mark Columns

Karate allows special handling of columns prefixed with !.

```gherkin
Examples:
| id | !payload |
| 1  | {name:'John'} |
```

Useful when passing structured objects.

---

### Data-Driven Testing Using JSON Files

### Why JSON?
- Nested structures
- Large datasets
- Reusable test data

### users.json
```json
[
 {"id":1,"name":"John"},
 {"id":2,"name":"Mary"}
]
```

### Feature File
```gherkin
* def users = read('users.json')

Scenario Outline:
Given url baseUrl
And path 'users', id
When method get
Then status 200
And match response.name == name

Examples:
| karate.setup().users |
```

Alternative:
```gherkin
* def users = read('classpath:data/users.json')
* def user = users[0]
```

---

### Data-Driven Testing Using CSV

### Why CSV?
- Business-friendly
- Excel compatible
- Easy maintenance

### users.csv
```csv
id,name
1,John
2,Mary
```

### Karate Implementation
```gherkin
* def users = read('users.csv')
```

```gherkin
Scenario Outline:
Given path 'users', id
When method get
Then status 200
And match response.name == name

Examples:
| users |
```

---

### Enterprise Use Cases

### Customer Creation Testing
```gherkin
Examples:
| customerId | type |
| 1001 | Retail |
| 1002 | Business |
```

### Payment Validation
```gherkin
Examples:
| amount |
| 10 |
| 100 |
| 1000 |
```

---

### Advanced JSON Driven Pattern

```gherkin
* def testData = read('classpath:data/orders.json')

Scenario Outline:
Given request __row
When method post
Then status 201

Examples:
| testData |
```

---

### Reusable Data Loader

```gherkin
@ignore
Scenario:
* def users = read('users.json')
* karate.set('users', users)
```

---

### Best Practices

1. Keep test data separate from feature files.
2. Use JSON for complex payloads.
3. Use CSV for business-maintained data.
4. Avoid duplicate examples.
5. Use meaningful column names.

---

### Common Pitfalls

### Hardcoded Data
Bad:
```gherkin
* def id = 1
```

Good:
```gherkin
Examples:
| id |
| 1 |
```

### Huge Example Tables
Move large datasets into JSON/CSV.

### Duplicate Scenarios
Prefer Scenario Outline.

---

### Interview Questions and Answers

### What is data-driven testing?
Executing the same test using different datasets.

### What is Scenario Outline?
A Karate structure that repeats execution for each Examples row.

### What are magic variables?
Built-in variables such as __row and __num.

### Difference between Scenario and Scenario Outline?
Scenario runs once; Scenario Outline runs once per example.

### Difference between JSON and CSV driven testing?
JSON supports nested structures. CSV is flat and business-friendly.

### What does __row return?
Current row as JSON object.

### What does __num return?
Current iteration number.

### Why store data externally?
Improves maintainability and reusability.

### How would you execute hundreds of datasets efficiently?
Store data in JSON/CSV and use Scenario Outline.

### How do you test large request payloads?
Generate payloads from JSON templates and override dynamically.

### How do you handle environment-specific test data?
Use karate-config.js together with environment files.

### How would you create reusable datasets?
Create dedicated data provider features and share them across suites.

### A telecom order API needs 500 order combinations. How would you design it?
Use JSON-driven tests with Scenario Outline and reusable payload templates.

### Business users maintain test data in Excel. What approach would you use?
Export Excel to CSV and read via Karate.

### A payload changes frequently. How would you maintain tests?
Move payloads into JSON templates and use overrides.

### A single scenario requires different authentication methods. What would you do?
Pass auth configuration through a JSON dataset and drive execution dynamically.

---

### Summary

Karate provides a powerful mechanism for implementing data-driven testing through:

- Scenario Outline
- Examples Tables
- Magic Variables (__row, __num)
- Auto Variables
- Embedded Expressions
- JSON Data Sources
- CSV Data Sources

For enterprise API automation, JSON-driven testing combined with reusable payload templates is typically the most scalable approach, while CSV remains ideal for business-maintained datasets.

