# Java Maven Application with GitHub Actions Matrix Build

This project is a simple Java application built with Maven. It includes unit tests using JUnit 5 and a fully configured GitHub Actions workflow that runs the build and tests across:

* Multiple operating systems:

  * **Ubuntu (latest)**
  * **Windows (latest)**
* Multiple Java versions:

  * **Java 17**
  * **Java 21**

The workflow uses **matrix strategy**, **include**, **exclude**, **fail-fast**, and **max-parallel** for advanced CI control.

---

## 1. Project Structure

```
.
├── src
│   └── main
│       └── java
│           └── com/example/App.java
│
├── src
│   └── test
│       └── java
│           └── com/example/AppTest.java
│
├── pom.xml
└── .github/
    └── workflows/
         └── matrix-build.yml
```

---

## 2. Application Code

The application contains a simple Java class (`App.java`) that prints a message and includes a method for demonstration purposes.


---

## 3. Test Code

This project uses **JUnit 5** (`junit-jupiter`) for unit testing. The `AppTest.java` file includes a sample test for the `add()` method.


---

## 4. Maven Configuration

The project uses Maven with Java 17 as the base compiler.
JUnit 5 and Surefire plugin are configured inside `pom.xml`.


---

## 5. Running the Project Locally

### Step 1: Install prerequisites

Make sure you have:

* Java 17 or higher
* Maven 3.8+

Check versions:

```bash
java -version
mvn -version
```

### Step 2: Compile and package the project

```bash
mvn clean package
```

### Step 3: Run the application

```bash
java -cp target/demo-app-1.0-SNAPSHOT.jar com.example.App
```

### Step 4: Run all tests

```bash
mvn test
```

---

## 6. GitHub Actions CI Workflow

This project includes a **GitHub Actions workflow** located at:

```
.github/workflows/matrix-build.yml
```

The workflow:

* Runs on **Ubuntu** and **Windows**
* Uses **Java 17** and **Java 21**
* Builds the app with Maven
* Runs tests
* Uses:

  * `fail-fast: false`
  * `max-parallel: 3`
  * `include`
  * `exclude`

---

## 7. Workflow File (matrix-build.yml)

```yaml
name: Java Matrix Build

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    name: Build on ${{ matrix.os }} with Java ${{ matrix.java }}
    runs-on: ${{ matrix.os }}

    strategy:
      fail-fast: false
      max-parallel: 3

      matrix:
        os: [ubuntu-latest, windows-latest]
        java: [17, 21]

        include:
          - os: ubuntu-latest
            java: 21
            extra-flag: true

        exclude:
          - os: windows-latest
            java: 21

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: ${{ matrix.java }}

      - name: Cache Maven packages
        uses: actions/cache@v4
        with:
          path: ~/.m2
          key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
          restore-keys: |
            ${{ runner.os }}-m2-

      - name: Build with Maven
        run: mvn -B package --file pom.xml

      - name: Run Tests
        run: mvn test
```

---

## 8. Understanding the Matrix Build

### Base matrix

| OS             | Java Version  |
| -------------- | ------------- |
| ubuntu-latest  | 17            |
| ubuntu-latest  | 21            |
| windows-latest | 17            |
| windows-latest | 21 (excluded) |

### include section

Used to **add extra parameters** or override combinations.

### exclude section

Used to remove unwanted combinations.
Example: Windows + Java 21 is excluded.

### fail-fast: false

Even if one matrix job fails, the others continue running.

### max-parallel: 3

Controls concurrency to avoid overwhelming GitHub or your free quota.

---



