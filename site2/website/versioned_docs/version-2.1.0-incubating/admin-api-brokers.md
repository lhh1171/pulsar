---
id: admin-api-brokers
title: Managing Brokers
sidebar_label: "Brokers"
original_id: admin-api-brokers
---

Pulsar brokers consist of two components:

1. An HTTP server exposing a {@inject: rest:REST:/} interface administration and [topic](reference-terminology.md#topic) lookup.
2. A dispatcher that handles all Pulsar [message](reference-terminology.md#message) transfers.

[Brokers](reference-terminology.md#broker) can be managed via:

* The [`brokers`](reference-pulsar-admin.md#brokers) command of the [`pulsar-admin`](reference-pulsar-admin) tool
* The `/admin/v2/brokers` endpoint of the admin {@inject: rest:REST:/} API
* The `brokers` method of the {@inject: javadoc:PulsarAdmin:/admin/org/apache/pulsar/client/admin/PulsarAdmin.html} object in the [Java API](client-libraries-java)

In addition to being configurable when you start them up, brokers can also be [dynamically configured](#dynamic-broker-configuration).

> See the [Configuration](reference-configuration.md#broker) page for a full listing of broker-specific configuration parameters.

## Brokers resources

### List active brokers

Fetch all available active brokers that are serving traffic.

#### pulsar-admin

```shell

$ pulsar-admin brokers list use

```

```

broker1.use.org.com:8080

```

###### REST

{@inject: endpoint|GET|/admin/v2/brokers/:cluster|operation/getActiveBrokers}

###### Java

```java

admin.brokers().getActiveBrokers(clusterName)

```

#### list of namespaces owned by a given broker

It finds all namespaces which are owned and served by a given broker.

###### CLI

```shell

$ pulsar-admin brokers namespaces use \
  --url broker1.use.org.com:8080

```

```json

{
  "my-property/use/my-ns/0x00000000_0xffffffff": {
    "broker_assignment": "shared",
    "is_controlled": false,
    "is_active": true
  }
}

```

###### REST

{@inject: endpoint|GET|/admin/v2/brokers/:cluster/:broker/ownedNamespaces|operation/getOwnedNamespaes}

###### Java

```java

admin.brokers().getOwnedNamespaces(cluster,brokerUrl);

```

### Dynamic broker configuration

One way to configure a Pulsar [broker](reference-terminology.md#broker) is to supply a [configuration](reference-configuration.md#broker) when the broker is [started up](reference-cli-tools.md#pulsar-broker).

But since all broker configuration in Pulsar is stored in ZooKeeper, configuration values can also be dynamically updated *while the broker is running*. When you update broker configuration dynamically, ZooKeeper will notify the broker of the change and the broker will then override any existing configuration values.

* The [`brokers`](reference-pulsar-admin.md#brokers) command for the [`pulsar-admin`](reference-pulsar-admin) tool has a variety of subcommands that enable you to manipulate a broker's configuration dynamically, enabling you to [update config values](#update-dynamic-configuration) and more.
* In the Pulsar admin {@inject: rest:REST:/} API, dynamic configuration is managed through the `/admin/v2/brokers/configuration` endpoint.

### Update dynamic configuration

#### pulsar-admin

The [`update-dynamic-config`](reference-pulsar-admin.md#brokers-update-dynamic-config) subcommand will update existing configuration. It takes two arguments: the name of the parameter and the new value. Here's an example for the [`brokerShutdownTimeoutMs`](reference-configuration.md#broker-brokerShutdownTimeoutMs) parameter:

```shell

$ pulsar-admin brokers update-dynamic-config brokerShutdownTimeoutMs 100

```

#### REST API

{@inject: endpoint|POST|/admin/v2/brokers/configuration/:configName/:configValue|operation/updateDynamicConfiguration}

#### Java

```java

admin.brokers().updateDynamicConfiguration(configName, configValue);

```

### List updated values

Fetch a list of all potentially updatable configuration parameters.

#### pulsar-admin

```shell

$ pulsar-admin brokers list-dynamic-config
brokerShutdownTimeoutMs

```

#### REST API

{@inject: endpoint|GET|/admin/v2/brokers/configuration|operation/getDynamicConfigurationName}

#### Java

```java

admin.brokers().getDynamicConfigurationNames();

```

### List all

Fetch a list of all parameters that have been dynamically updated.

#### pulsar-admin

```shell

$ pulsar-admin brokers get-all-dynamic-config
brokerShutdownTimeoutMs:100

```

#### REST API

{@inject: endpoint|GET|/admin/v2/brokers/configuration/values|operation/getAllDynamicConfigurations}

#### Java

```java

admin.brokers().getAllDynamicConfigurations();

```

