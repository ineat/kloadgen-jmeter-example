# kloadgen-jmeter-example

This repository illustrate how to use KLoadGen with jMeter in order to stress a Kafka ecosystem ;

For further details, see this related blog post.

## Prerequisites

### jMeter, obviously

- Get and install the tool from https://jmeter.apache.org/
- Get and copy the KLoadGen version into jMeter `lib/ext` folder from https://github.com/sngular/kloadgen

> Open jMeter and load the [turn-on-usecase.jmx](./turn-on-usecase.jmx) when you jMeter env is ready

### Kafka and a Schema Registry

If you have no access to a Kafka Broker with a Schema Registry, you can start the following services:

- Kafka by running the awesome [zk-single-kafka-multiple](https://github.com/conduktor/kafka-stack-docker-compose/blob/master/zk-single-kafka-multiple.yml) with `docker compose -f zk-multiple-kafka-single.yml up`
- The APICURIO schema registry by running `docker run -it -p 8080:8080 apicurio/apicurio-registry-mem:latest-snapshot`

> you need to create a topic named `smartylighting.streetlights.1.0.action.test.turn.on`

#### Register the turnOnPayload avro schema

When your registry is started, call the following HTTP request in order to publish the schema into the registry

```
curl --location 'http://localhost:8080/apis/registry/v2/groups/my-group/artifacts' \
--header 'Content-Type: application/json; artifactType=AVRO' \
--header 'X-Registry-ArtifactId: turnOnOffPayload' \
--data '{
  "type": "record",
  "name": "turnOnOffPayload",
  "namespace": "com.ineat.kloadgen.example",
  "fields": [
    {
      "docs": "Whether to turn on or off the light.",
      "name": "command",
      "type": {"name": "CommandOpts", "type": "enum", "symbols": ["on", "off"]}
    },
    {"name": "sentAt",
    "type" : {
        "type" : "long",
        "logicalType" : "timestamp-millis"
      },
      "docs": "Date and time when the message was sent."}

  ]
}
'
```