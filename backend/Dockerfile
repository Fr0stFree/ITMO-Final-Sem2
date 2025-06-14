# Stage 1: Build the Java application using Maven
FROM maven:3.9-amazoncorretto-17 AS builder

ARG VERSION=${VERSION}

WORKDIR /usr/src/app

COPY ./src ./src

COPY *.xml ./

RUN mvn package -Dversion.application=${VERSION}

# Stage 2: Create a minimal image to run the Java application
FROM amazoncorretto:17-alpine

ARG VERSION=${VERSION}

WORKDIR /app

RUN addgroup --system jaruser \
    && adduser -S -s /bin/false -G jaruser jaruser -D -H \
    && apk add --no-cache dumb-init \
    && apk add --update curl \
    && rm -rf /var/cache/apk/* \
    && mkdir -p /logs/reports \
    && chown -R jaruser:jaruser /logs

COPY --chown=jaruser:jaruser \
     --from=builder /usr/src/app/target/sausage-store-${VERSION}.jar ./sausage-store.jar

EXPOSE 8080

USER jaruser

ENTRYPOINT ["dumb-init", "java", "-jar", "-Dmyserver.bindPort=8080", "./sausage-store.jar"]
