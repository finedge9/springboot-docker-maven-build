# springboot-docker-maven-build
refered from: https://stackoverflow.com/questions/27767264/how-to-dockerize-maven-project-and-how-many-ways-to-accomplish-it

Working example.

This is not a spring boot tutorial. It's the updated answer to a question on how to run a Maven build within a Docker container.

Question originally posted 4 years ago.
1. Generate an application

Use the spring initializer to generate a demo app

https://start.spring.io/

Extract the zip archive locally

2. Create a Dockerfile

#
# Build stage
#
FROM maven:3.6.0-jdk-11-slim AS build
COPY src /home/app/src
COPY pom.xml /home/app
RUN mvn -f /home/app/pom.xml clean package

#
# Package stage
#
FROM openjdk:11-jre-slim
COPY --from=build /home/app/target/demo-0.0.1-SNAPSHOT.jar /usr/local/lib/demo.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/usr/local/lib/demo.jar"]

Note

    This example uses a multi-stage build. The first stage is used to build the code. The second stage only contains the built jar and a JRE to run it (note how jar is copied between stages).

3. Build the image

docker build -t demo .

4. Run the image

$ docker run --rm -it demo:latest
