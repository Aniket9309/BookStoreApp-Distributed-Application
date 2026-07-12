# ===== Stage 1: Build =====
FROM maven:3.8-eclipse-temurin-8 AS build

WORKDIR /app

# Poora repo copy karo (parent pom + commons + feign + service chahiye)
COPY . .

# Sirf is service ko build karo, dependencies (commons/feign) bhi saath resolve karega (-am flag)
RUN mvn clean install -pl bookstore-eureka-discovery-service -am -DskipTests

# ===== Stage 2: Runtime =====
FROM eclipse-temurin:8-jre-alpine

VOLUME /tmp

COPY --from=build /app/bookstore-eureka-discovery-service/target/bookstore-eureka-discovery-service-0.0.1-SNAPSHOT.jar eureka-app.jar

ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/eureka-app.jar"]
