ARG DEBEZIUM_VERSION=3.0

FROM quay.io/debezium/connect:3.1.1.Final

ARG POSTGRES_VERSION=42.2.8
ARG KAFKA_JDBC_VERSION=5.3.1

# Fetch and deploy PostgreSQL JDBC driver
RUN cd /kafka/libs && \
    curl -sO https://repo1.maven.org/maven2/org/postgresql/postgresql/$POSTGRES_VERSION/postgresql-$POSTGRES_VERSION.jar

# Fetch and deploy Kafka Connect JDBC
ENV KAFKA_CONNECT_JDBC_DIR=$KAFKA_CONNECT_PLUGINS_DIR/kafka-connect-jdbc
RUN mkdir $KAFKA_CONNECT_JDBC_DIR

RUN cd $KAFKA_CONNECT_JDBC_DIR && \
    curl -s0 https://mvnrepository.com/artifact/io.debezium/debezium-connector-jdbc/3.1.1.Final.ar
