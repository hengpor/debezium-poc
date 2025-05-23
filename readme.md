# Debezium end-to-end
PoC building a workflow moving data from one table in Postgresql to another Postgresql

1. Setting up source connector using Debezium postgresql
2. Setting up sink connector using Debezium JDBC

## Start services
```commandline
docker-compose up -d
```

## Stop all containers
```commandline
docker-compose down
```
## List kafka topics
```shell
docker exec -it kafka kafka-topics --bootstrap-server kafka:9092 --list
```

## Read kafka event
```shell
docker exec -it kafka kafka-console-consumer \
    --bootstrap-server kafka:9092 \
    --topic local.public.hotel_status \
    --from-beginning
```
## Make sure all plugin are available 
Go to
```shell
http://127.0.0.1:8083/connector-plugins/
```
### Connector configuration schema
```shell
http://127.0.0.1:8083/connector-plugins/io.debezium.connector.postgresql.PostgresConnector/config/
```
## Create source connector
after starting service 
go to localhost:8083 to make sure the connector is up and port is forwarded.
Debezium connect also provide API endpoint where you can create, update and delete connectors.

### To add a new connector
```commandline
curl -XPOST -H "Content-Type: application/json" --data @local.json http://localhost:8083/connectors
```

### To update a connector
```commandline
curl -XPUT http://localhost:8083/connectors/{connector name}/config \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
-d '{
please connector configure here
}'
```

### To delete a connector 
```commandline
curl -i -X DELETE localhost:8083/connectors/{connector name}/
```

## References:
https://github.com/debezium/debezium-examples/tree/main/end-to-end-demo

```shell
CREATE TABLE
  public.exist_hotel_status (
    hotel_status_id serial PRIMARY KEY NOT NULL,
    hotel_id integer NOT NULL,
    status varchar NOT NULL,
    reason character varying NULL,
    additional_info character varying NULL,
    created_at timestamp with time zone DEFAULT NOW(),
    updated_at timestamp with time zone DEFAULT NOW()
  );

INSERT INTO exist_hotel_status (hotel_status_id, hotel_id, status, reason, additional_info)
VALUES
(100, 100, 'inactive', 'testing', 'test new field'),
(101, 101, 'inactive', 'testing', 'test new field')
;
```

```shell
curl -XPOST -H "Content-Type: application/json" --data @sink2.json http://localhost:8083/connectors
curl -XPOST -H "Content-Type: application/json" --data @local.json http://localhost:8083/connectors


curl -XPOST -H "Content-Type: application/json" --data @sink.json http://localhost:8083/connectors
curl -XPOST -H "Content-Type: application/json" --data @sink2.json http://localhost:8083/connectors



curl -XPUT http://localhost:8083/connectors/jdbc-connector/config \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
-d '{
    "connector.class": "io.debezium.connector.jdbc.JdbcSinkConnector",
    "tasks.max": "1",
    "connection.url": "jdbc:postgresql://postgres-dest:5432/postgres",
    "connection.username": "postgres",
    "connection.password": "12345",
    "insert.mode": "upsert",
    "delete.enabled": "true",
    "primary.key.mode": "record_key",
    "pk.mode": "record_key",
    "schema.evolution": "basic",
    "use.time.zone": "UTC",
    "topics": "debezium_cdc_local.public.hotel_status",
    "table.name.format": "new_hotel_status"
}'

curl -i -X DELETE localhost:8083/connectors/jdbc-connector/
curl -i -X DELETE localhost:8083/connectors/jdbc-connector-2/

docker exec -it kafka kafka-console-consumer \
 --bootstrap-server kafka:9092 \
 --topic debezium_cdc_local.public.hotel_status \
 --from-beginning

```