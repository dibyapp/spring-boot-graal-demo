# Spring Boot GraalVM Native Image Demo with JPA and MySQL

A small **Spring Boot 3.1.3 / Java 17** learning project for tracing a REST endpoint through Spring Data JPA to MySQL, then exploring GraalVM Native Image. The source exposes `GET /getRecords` and contains a `TestUser` entity and JPA repository.

This is a historical experiment, not a production API or a benchmark. It currently has no reproducible native-build certification, authentication layer, or measured startup/memory comparison.

## Source map

| File | What to inspect |
| --- | --- |
| [DemoApplication.java](src/main/java/com/neonci/DemoApplication.java) | Spring Boot entry point |
| [GrallController.java](src/main/java/com/neonci/controller/GrallController.java) | `/getRecords` mapping |
| [TestUser.java](src/main/java/com/neonci/entity/TestUser.java) | `testuser` table and entity fields |
| [TestUserRepository.java](src/main/java/com/neonci/repository/TestUserRepository.java) | JPA repository |
| [pom.xml](pom.xml) | Boot 3.1.3 parent, Java 17, native and Boot plugins |

## Start with the JVM

```sh
git clone https://github.com/dibyapp/spring-boot-graal-demo.git
cd spring-boot-graal-demo
mvn -DskipTests package
```

Install Java 17 and Maven. Prepare a disposable MySQL schema and set the three variables in [DATABASE.md](docs/DATABASE.md) before running `mvn spring-boot:run`. The database account needs the permissions required by this demo's Hibernate `ddl-auto: update` configuration. Use synthetic records only.

After successful startup, inspect `http://localhost:8080/getRecords` locally. An empty synthetic table may return `[]`. The endpoint returns entity objects directly, including a password property in the current model; it is not safe to expose this API or populate it with real users. A DTO and password handling are required before reuse.

The entity's ID is `int`, while the repository declares `Long`; reconcile that mismatch before extending ID-based operations. Packaging is not a successful database or native-runtime test. The existing context-loading test requires configured infrastructure.

## Explore native compilation

The POM includes `native-maven-plugin` and `spring-boot-maven-plugin`. Boot's parent supplies native-profile conventions. Use a compatible GraalVM Java 17 toolchain, Native Image, and the native C/C++ build prerequisites for your platform. Follow the [Boot 3.1.3 native-image guide](https://docs.spring.io/spring-boot/docs/3.1.3/reference/html/native-image.html) for the profile and AOT sequence.

The conventional command to investigate is `mvn -Pnative -DskipTests native:compile`. It is a build recipe, not a claim of a tested native binary. Prove JVM startup and the database query first, then compare the native run using the same synthetic data and configuration.

## Troubleshooting and useful next contributions

- Unresolved datasource placeholders: set all three process environment variables.
- Database connection / schema errors: check your local MySQL service, demo schema, grants, and driver compatibility.
- Native toolchain errors: confirm `java -version`, `native-image --version`, and the compiler toolchain match this historical Boot version.
- Useful next work: a safe response DTO, ID-type alignment, a disposable database fixture, a verified AOT build, and a repeatable JVM/native comparison with raw measurements.

For a new application, generate a project using currently supported versions at [Spring Initializr](https://start.spring.io). Upgrading this historical demo needs code and runtime validation.

## What has been checked?

The JVM source compilation only check passed locally. [Validation scope](docs/VALIDATION.md) · [CI](https://github.com/dibyapp/spring-boot-graal-demo/actions/workflows/starter-check.yml). This is a limited check, not certification of the whole repository.

## Continue learning

| What you want to understand | Repository |
| --- | --- |
| IoC, dependency injection, bean lifecycle, AOP | [Spring Core examples](https://github.com/dibyapp/spring-core-all-concepts-tutorial) |
| Controllers, JSP forms, validation, CRUD, PRG | [Spring MVC examples](https://github.com/dibyapp/spring-mvc-all-concepts-tutorial) |
| Requests, sessions, cookies, dispatching, JDBC | [Java Servlet examples](https://github.com/dibyapp/java-servlets-all-concepts) |
| Spring Boot 3.1 JPA/MySQL native-image demo | [Spring Boot GraalVM demo](https://github.com/dibyapp/spring-boot-graal-demo) |
| Explicit native Maven profile experiment | [GraalVM application](https://github.com/dibyapp/graalvm-app) |
| Historical Boot 3.0 AOT/Kubernetes experiment | [Spring native study project](https://github.com/dibyapp/spring-native) |

## Contribute or report a problem

Found a broken example? Include its folder, Java/tool versions, a minimal reproduction, and a sanitized error. See [CONTRIBUTING.md](CONTRIBUTING.md). A small fix with a repeatable before/after result is useful; unverified performance claims are not.

Maintained by [dibyapp](https://github.com/dibyapp). [MIT](LICENSE) applies to the author's original contributions; inherited material and bundled third-party files retain their own terms. Read [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md). If an example helped you understand a concept, a star or a link to that specific example helps other learners find it.
