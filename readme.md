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