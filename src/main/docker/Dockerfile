# syntax=docker.io/docker/dockerfile:1.7-labs
FROM maven:3.9.11-amazoncorretto-21-debian-trixie@sha256:ad74ccc1ac9fc6841347f5be09838d33ec239bf2344b1d95cf6d70c6bba335f3 AS bygging
RUN apt-get update;apt-get install -y unzip=6.0-29 zlib1g
RUN mkdir -p /app/jre
COPY . /app
WORKDIR /app
RUN mvn clean package -DskipTests;unzip /app/target/app-jlink.zip -d /app/jre
ARG MODULE_MAIN
RUN echo "-m \"$MODULE_MAIN\"\n--module-path /app/app.jar:/app/dependency\n" > arguments

FROM gcr.io/distroless/base@sha256:4f6e739881403e7d50f52a4e574c4e3c88266031fd555303ee2f1ba262523d6a
LABEL maintainer="Team Arbeidsforhold"
ENV LANG='nb_NO.UTF-8' LANGUAGE='nb_NO:nb' LC_ALL='nb:NO.UTF-8' TZ="Europe/Oslo" LD_LIBRARY_PATH="/usr/lib"
COPY --from=bygging /usr/lib/x86_64-linux-gnu/libz.so.* /usr/lib/
COPY --from=bygging /app/jre/lib/* /usr/lib
COPY --from=bygging --exclude=/app/jre/lib /app/jre /app/jre
COPY --from=bygging /app/target/dependency /app/target/dependency
COPY --from=bygging /app/target/app.jar /app/app.jar
COPY --from=bygging /app/arguments /app/arguments

ENTRYPOINT ["/app/jre/bin/java", "@/app/arguments"]
