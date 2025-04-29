# Step 1: Use an official Gradle image to build the application
FROM gradle:8.3.0-jdk17 AS build

# Set the working directory inside the container
WORKDIR /app

# Copy the entire project to the container
COPY . .

# Build the application
RUN gradle clean bootJar

# Step 2: Use a lightweight JDK image for running the app
FROM openjdk:17-jdk-slim

# Set the working directory for the runtime container
WORKDIR /app

COPY grafana-opentelemetry-java.jar grafana-opentelemetry-java.jar 
ENV OTEL_LOGS_EXPORTER="otlp"
ENV OTEL_PROFILING_ENABLED="true"
ENV OTEL_RESOURCE_ATTRIBUTES="service.name=airlines,service.namespace=povsim-application-group,deployment.environment=production"
ENV OTEL_EXPORTER_OTLP_ENDPOINT="http://alloy:4318"
ENV OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"

# Copy the JAR file from the build stage
COPY --from=build /app/build/libs/*.jar app.jar

# Expose the port the application runs on
EXPOSE 8080

# Set the command to run the application
ENTRYPOINT ["java", "-javaagent:grafana-opentelemetry-java.jar", "-jar", "app.jar"]