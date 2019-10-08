---
layout: default
title: 运行测试
permalink: /running-tests/
redirect_from:
  - /running_tests.html
sitemap:
    priority: 0.7
    lastmod: 2019-04-19T00:00:00-00:00
---

# <i class="fa fa-shield"></i> 运行测试

## 介绍

JHipster附带了一组全面的测试，并且每个生成的应用程序都具有：

*   使用[JUnit 5](https://junit.org/junit5/){:target="_blank"}进行单元测试。
*   使用Spring Test Context框架进行集成测试。
*   用[Jest](https://facebook.github.io/jest/){:target="_blank"}进行UI测试。
*   使用[ArchUnit](https://www.archunit.org/){:target="_blank"}进行架构测试。

可选地，JHipster还可以生成：

*   用[Gatling](http://gatling.io/){:target="_blank"}进行性能测试。
*   行为驱动的[Cucumber](https://cucumber.io/){:target="_blank"}测试。
*   用[Protractor](https://angular.github.io/protractor/#/){:target="_blank"}进行 Angular/React/Vue集成测试

生成这些测试有两个目标：

*   帮助每个JHipster用户遵循最佳实践，因为我们认为测试是每个应用程序中非常有用的一部分
*   验证所生成的内容正确无误。因此，即使您根本不打算使用这些测试，在生成应用程序后仅进行`./mvnw clean verify`和`npm test`也是了解一切正常的一种好方法。如果您认为测试浪费时间，那么您可以自由地忽略那些测试！

所有这些测试都将在标准`src/test`文件夹中生成。

## 集成测试

集成测试是通过Spring Test Context框架完成的，位于`src/test/java`文件夹中。JHipster将启动特定的Spring测试上下文，该上下文将在所有测试中重复使用，如下所示：

*   您的Spring bean应该是无状态的并且是线程安全的，因此可以在不同的测试套件中重复使用。
*   Launching just one Spring context for all tests if a lot faster than launching a new Spring context for each test.如果与为每个测试启动一个新的Spring上下文相比快得多，那么为所有测试仅启动一个Spring上下文。

This Spring test context will use a specific test database to execute its tests:

*   If you use an SQL database, JHipster will launch an in-memory H2 instance in order to use a temporary database for its integration tests. Liquibase will be run automatically, and will generate the database schema.
*   If you use Cassandra, JHipster will launch a containerized version of Cassandra with Docker using [Testcontainers](https://www.testcontainers.org){:target="_blank"}.
*   If you use MongoDB, JHipster will launch an in-memory MongoDB instance using [de.flapdoodle.embed.mongo](https://github.com/flapdoodle-oss/de.flapdoodle.embed.mongo){:target="_blank"}.
*   If you use Elasticsearch, JHipster will launch an in-memory Elasticsearch instance using Spring Data Elasticsearch.
*   If you use Couchbase, JHipster will launch a containerized version of Couchbase with Docker using [Couchbase TestContainers](https://github.com/differentway/testcontainers-java-module-couchbase){:target="_blank"}.

Those tests can be run directly in your IDE, by right-clicking on each test class, or by running `./mvnw clean verify` (or `./gradlew test integrationTest` if you use Gradle).

**Limitations:** if the generated entities have validation enabled, JHipster is not enable to generate the correct values depending on the validation rules. Those rules can be so complex, for example if a Regex pattern is used, that this just not possible. In this case, the tests will fail validation, and the default values used in the test will need to changed manually, so they can pass the validation rules.

## UI tests

UI tests come in two flavors with JHipster: unit tests with Jest, and integration tests with Protractor. Only Jest is provided by default, but if you want to have a good test coverage of your application, we recommend that you use both tools together.

### Jest

UI unit tests are located in the `src/test/javascript/spec` folder. They use [Jest](https://facebook.github.io/jest/){:target="_blank"}.

Those tests will mock up the access to the application's REST endpoints, so you can test your UI layer without having to launch the Java back-end.

*   Those tests can be run using `npm test`.
*   Tip: if you want to focus on a single test change the module description from `describe('...', function() {` to `fdescribe('...', function() {` and Jest will run this test only.

### Protractor

UI integration tests are done with [Protractor](https://angular.github.io/protractor/#/){:target="_blank"}, and are located in the `src/test/javascript/e2e` folder.

Those tests will launch a Web browser and use the application like a real user would do, so you need to have a real application running, with its database set-up.

Those tests can be run using `npm run e2e`.

## Architecture tests

Architecture tests, which enforce certain constrainsts and best practices are done with [ArchUnit](https://www.archunit.org/){:target="_blank"}.
You can easily write your own rules to check custom constraints for your architecture at build time following the [official documentation](https://www.archunit.org/userguide/html/000_Index.html){:target="_blank"}.

## Performance tests

Performance tests are done with [Gatling](http://gatling.io/){:target="_blank"}, and are located in the `src/test/gatling` folder. They are generated for each entity, and allows to test each of them with a lot of concurrent user requests.

To run Gatling tests, you must first install Gatling: please go to the [Gatling download page](https://gatling.io/open-source/){:target="_blank"} and follow the instructions there. Please note we do not allow to run Gatling from Maven or Gradle, as it causes some classpath issues with other plugins (mainly because of the use of Scala).

**Warning!** At the moment, those tests do not take into account the validation rules you may have enforced on your entities. Also tests for creating entities that have a required relationship with another entity will fail out of the box. You will anyway need to change those tests, according to your business rules, so here are few tips to improve your tests:

*   On your running application, go to the `Administration > Logs` screen, and put `org.springframework` in `debug` mode. You will see the validation errors, for example.
*   Use the application normally and open the Chrome `console log`: you will be able to see the REST requests with all their parameters, including the HTTP headers.

For running Gatling tests on a microservice application, you have to:

*   Run a registry
*   Run a gateway
*   Run the microservice application
*   Then, you can run Gatling tests

## Behaviour-driven development (BDD)

Behaviour-driven development (BDD) is available using [Cucumber](https://cucumber.io/){:target="_blank"}, with its [JVM implementation](https://github.com/cucumber/cucumber-jvm){:target="_blank"}.

[Gherkin](https://docs.cucumber.io/gherkin/reference/){:target="_blank"} features will have to be written in your `src/test/features` directory.
