FROM eclipse-temurin:17-jre

ENV OTEL_AGENT_VERSION=1.10.0 \
    OTEL_AGENT_JAR_FILE=opentelemetry-javaagent.jar \
    APP_HOME=/app \
    MIN_HEAP_SIZE="40M" MAX_HEAP_SIZE="512M" THREADSTACK_SIZE="228k" \
    JAVA_GC_ARGS="-XX:MinHeapFreeRatio=20 -XX:MaxHeapFreeRatio=40 -XX:+UseSerialGC -XX:GCTimeRatio=4 -XX:AdaptiveSizePolicyWeight=90" \
    JAVA_OPTS="-server -Duser.timezone=America/Chicago -Djava.security.egd=file:/dev/./urandom -XX:CompressedClassSpaceSize=64m -XX:MaxMetaspaceSize=256m" \
    JAVA_DIAG_ARGS="" \
    JAVA_OPTS_APPEND=""

RUN apt-get update && \
	apt-get install -q=2 -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confnew" dumb-init

# Adds a non privileged user 'app' with group 'app' that our application can be run as
RUN adduser app --home ${APP_HOME} --gecos ",,," --disabled-password --system --group

# Set user and working directory
WORKDIR ${APP_HOME}
USER app

COPY ./target/${OTEL_JAR_FILE} ${APP_HOME}/

# Set entrypoint into the container and the startup command
ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD java ${JAVA_OPTS} \
	${JAVA_GC_ARGS} \
	${JAVA_DIAG_ARGS} \
	${JAVA_OPTS_APPEND} \
	-Dspring.profiles.active=${SPRING_PROFILES_ACTIVE} \
	-server -jar ./${APP_NAME}.jar
