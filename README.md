# aws-lambda-example

**Repository:** [github.com/suruj404/aws-lambda-example](https://github.com/suruj404/aws-lambda-example)

A serverless Spring Boot 3 REST API packaged for deployment to AWS Lambda, using the [AWS Serverless Java Container](https://github.com/aws/serverless-java-container) to bridge Lambda's `RequestStreamHandler` interface with a standard Spring MVC application.

## Overview

The project exposes a small REST API for managing courses, plus a health-check endpoint, all running inside a single Lambda function behind API Gateway.

- **Runtime:** Java 17, Spring Boot 3.2.6
- **Lambda runtime:** `java21`
- **Entry point:** `org.example.StreamLambdaHandler::handleRequest`
- **Packaging:** Maven assembly (zip) with runtime dependencies in `lib/`, or an optional shaded jar

## Endpoints

| Method | Path            | Description         |
|--------|-----------------|----------------------|
| GET    | `/ping`         | Health check         |
| GET    | `/courses`      | List all courses     |
| GET    | `/courses/{id}` | Get a course by id   |
| POST   | `/courses`      | Create a course      |
| PUT    | `/courses/{id}` | Update a course      |
| DELETE | `/courses/{id}` | Delete a course      |

## Project layout

```
src/main/java/org/example/
├── Application.java              # Spring Boot entry point
├── StreamLambdaHandler.java      # Lambda request stream handler
├── controller/
│   ├── PingController.java       # /ping health check
│   └── CourseController.java     # /courses CRUD endpoints
├── dto/
│   └── Course.java               # Course data model
└── service/
    └── CourseService.java        # In-memory course logic
```

## Build

```bash
mvn clean package
```

This produces a deployment-ready zip under `target/` (via the `assembly-zip` profile, active by default), containing the compiled classes plus a `lib/` folder with runtime dependencies. To build a single shaded jar instead:

```bash
mvn clean package -P shaded-jar
```

## Deploy

The included `template.yml` is an AWS SAM template that provisions the Lambda function and an API Gateway proxy resource (`/{proxy+}`):

```bash
sam deploy --guided
```

## Testing

```bash
mvn test
```

`StreamLambdaHandlerTest` exercises the handler directly against `/ping` and a non-existent route, using `AwsProxyRequestBuilder` from the serverless-java-container test utilities.

## Acknowledgements

Built on top of [aws/serverless-java-container](https://github.com/aws/serverless-java-container) ([issue #134](https://github.com/aws/serverless-java-container/issues/134) is referenced in `application.properties` regarding cold-start config).
