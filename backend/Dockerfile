FROM gradle:8.5-jdk21-alpine AS build

WORKDIR /app

# Layer 1: Gradle wrapper (rarely changes)
COPY gradlew .
COPY gradle gradle/

# Layer 2: Build configuration (rarely changes)
COPY settings.gradle.kts .
COPY backend/build.gradle.kts backend/

# Layer 3: Download dependencies (cached unless dependencies change)
RUN ./gradlew :backend:dependencies --no-daemon || true

# Layer 4: Source code (changes frequently)
COPY backend/src backend/src/

# Layer 5: Build (only re-runs if source or dependencies changed)
RUN ./gradlew :backend:bootJar --no-daemon -x test

# Runtime stage (unchanged)
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
RUN addgroup -S spring && adduser -S spring -G spring
COPY --from=build /app/backend/build/libs/*.jar app.jar
RUN chown -R spring:spring /app
USER spring:spring
EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:8080/actuator/health || exit 1
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0"
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]