FROM registry.access.redhat.com/ubi8/openjdk-11
COPY hello/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT [ "java", "-jar", "app.jar" ]
