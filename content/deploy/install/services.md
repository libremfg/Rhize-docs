---
title: Install Rhize services
description: >-
  Instructions to install services in the Rhize Kubernetes cluster.
weight: 100
categories: "how-to"
menu:
  main:
    parent: install
    identifier: install-services
---

The final installation step is to install the Rhize services in your Kubernetes cluster.

## Prerequisites

This topic assumes you have done the following:
- [Set up Kubernetes](/deploy/install/setup-kubernetes) and [Configured Keycloak]({{< relref "keycloak" >}}). All the prerequisites for those topics apply here.
- Configured load balancing for the following DNS records:

    {{< reusable/default-urls >}}

    _Note that `rhize-` is only the recommended prefix of the subdomain. Your organization may use something else._


### Overrides

Each service is installed through a Helm YAML file.
For some of these services, you might need to edit this file to add credential information and modify defaults.

Common values that are changed include:
- URLs and URL links
- The number of replicas running for each pod
- Ingress values for services exposed on the internet

## Get client secrets.

1. Go to Keycloak and get the secrets for each client you've created.
1. Create Kubernetes secrets for each service. You can either create a secret file, or pass raw data from the command line.

   {{< notice "caution" >}}
   How you create Kubernetes secrets **depends on your implementation details and security procedures.**
   For guidance, refer to the official Kubernetes topic, [Managing Secrets using `kubectl`](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-kubectl/).
   {{< /notice >}}

   With raw data, the command might look something like this.

   ```bash
   kubectl create secret generic {{< param application_name >}}-client-secrets \
   -n {{< param application_name >}} --from-literal=dashboard=<USER \
   --from-literal={{< param application_name >}}Agent=123 \
   --from-literal={{< param application_name >}}Audit=123 \
   --from-literal={{< param application_name >}}Baas=KYbMHlRLhXwiDNFuDCl3qtPj1cNdeMSl \
   --from-literal={{< param application_name >}}BPMN=123 \
   --from-literal={{< param application_name >}}Core=123 \
   --from-literal={{< param application_name >}}UI=123 \
   --from-literal=router=123
   ```

   As you install services through Helm, their respective YAML files reference these secrets.

## Add the Rhize Helm Chart Repository

You must add the helm chart repository for Rhize.

1. Add the Helm Chart Repository

    ```bash
    helm repo add libre https://gitlab.com/api/v4/projects/42214456/packages/helm/stable
    ```

## Install and add roles for the DB {#db}

You must install the {{< param db >}} database service first.
You also need to configure the {{< param db >}} service to have roles in Keycloak.

If enabling the Audit Trail, also the include the configuration in [Enable change data capture](#enable-change-data-capture).


1. Use Helm to install the database:

    ```bash
    helm install -f baas.yaml {{< param application_name >}}-baas {{< param application_name >}}/baas -n {{< param application_name >}}
    ```

    To confirm it works, run the following command:

    ```bash
    kubectl get pods
    ```

    All statuses should be `RUNNING`.


1. Return to the Keycloak UI and add all `{{< param application_name >}}` roles to the admin group.

1. Proxy the `http:8080` port on `{{< param application_name >}}-baas-dgraph-alpha`.

1. Get a token using the credentials. With `curl`, it looks like this:

    ```bash
    curl --location --request POST 'https://<customer>-
    auth.{{< param application_name >}}/realms/{{< param application_name >}}/protocol/openid-connect/token' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --data-urlencode 'grant_type=password' \
    --data-urlencode 'username=system@{{< param application_name >}}.com' \
    --data-urlencode 'password=<PASSWORD>' \
    --data-urlencode 'client_id={{< param application_name >}}Baas' \
    --data-urlencode 'client_secret=<CLIENT_SECRET>'
    ```

1. Post the schema:

    ```bash
    curl --location --request POST 'http://localhost:<FORWARDED_PORT>/admin/schema' \
    --header 'Authorization: Bearer <TOKEN>' \
    --data grant_type=urn:ietf:params:oauth:grant-type:uma-ticket" \
    --header 'Content-Type: application/octet-stream' \
    --data-binary '<SCHEMA_FILE>’
    ```

    This creates more roles.

1. Go to Keycloak UI and add all new {{< param db >}} roles to the `ADMIN` group.

If the install is successful, the Keycloak UI is available on its
[default port]({{< ref "default-ports" >}}).


## Install services

Each of the following procedures installs a service through Helm.

The syntax to install a Rhize service must have arguments for the following:
- The chart YAML file
- The packaged chart
- The path to the unpackaged chart or directory

Additionally, use the `-n` flag to ensure that the install is scoped to the correct namespace:


```
helm install <service_name> \
  -f <service-overide-file>.yaml \
  <path/to/directory> \
  -n <ns>
```

For the full configuration options,
read the official [Helm `install` reference](https://helm.sh/docs/helm/helm_install/).

<!-- vale off -->
### NATS {#nats}
<!-- vale on -->


[NATS](https://nats.io) is the message broker that powers Rhize's event-driven architecture.

Install NATS with these steps:

1. Modify the NATS Helm file with your code editor. Edit any necessary overrides.
1. Install with Helm:

    ```
    helm install nats -f nats.yaml {{< param application_name >}}/nats -n {{< param application_name >}}
    ```


### Tempo

Rhize uses [Tempo](https://grafana.com/oss/tempo/) to trace BPMN processes.

Install Tempo with these steps:

1. If it doesn't exist, add the Tempo repository:

    ```bash
    helm repo add grafana https://grafana.github.io/helm-charts
    ```

1. Modify the Helm file as needed.
1. Install with Helm:

    ```bash
    helm install tempo -f tempo.yaml grafana/tempo -n {{< param application_name >}}
    ```

### Core

The {{< param brand_name >}} Core service is the custom edge agent that monitors data sources, like OPC-UA servers, and publishes and subscribes topics to NATS.

> **Requirements**: Core requires the [{{< param db >}}](#db) and [NATS](#nats) services.

Install the Core agent with these steps:

1. In the `core.yaml` Helm file, edit the `clientSecret` and `password` with settings from the Keycloak client.
1. Override any other values, as needed.
1. Install with Helm:

    ```bash
    helm install core -f core.yaml {{< param application_name >}}/core -n {{< param application_name >}}
    ```

### BPMN

The BPMN service is the custom engine Rhize uses to process low-code workflows modeled in the BPMN UI.

> **Requirements**: The BPMN service requires the [{{< param db >}}](#db), [NATS](#nats), and [Tempo](#tempo) services.

Install the BPMN engine with these steps:

1. Open `bpmn.yaml` Update the `clientSecret` and `password` for your BPMN Keycloak credentials.
1. Modify any other values, as needed.
1. Install with Helm:

   ```bash
   helm install bpmn -f bpmn.yaml {{< param application_name >}}/bpmn -n {{< param application_name >}}
   ```

### Router

Rhize uses the [Apollo router](https://www.apollographql.com/docs/router) to unite queries for different services in a single endpoint.

> **Requirements:** Router requires the [GraphDB](#db), [BPMN](#bpmn), and [Core](#core) services.

Install the router with these steps:

1. Modify the router Helm YAML file as needed.
1. Install with Helm:

    ```bash
    helm install router -f router.yaml {{< param application_name >}}/router -n {{< param application_name >}}
    ```

If the install is successful, the Router explorer is available on its
[default port]({{< ref "default-ports" >}}).

### Grafana

Rhize uses [Grafana](https://grafana.com) for its dashboard to monitor real time data.

Install Grafana with these steps:

1. Modify the Grafana Helm YAML file as needed.

1. Add the Helm repository
    ```bash
    helm repo add grafana https://grafana.github.io/helm-charts
    ```

1. Install with Helm:

    ```bash
    helm install grafana -f grafana.yaml grafana/grafana -n {{< param application_name >}}
    ```

If the install is successful, the Grafana service is available on its
[default port]({{< ref "default-ports" >}}).

### Agent

The Rhize agent bridges your plant processes with the Rhize data hub.
It collects data emitted from the plant and publishes it to the NATS message broker.

> **Requirements:** Agent requires the [Graph DB](#db), [Nats](#nats), and [Tempo](#tempo) services.

Install the agent with these steps:

1. Modify the Agent Helm file as needed.
2. Install with Helm:

    ```bash
    helm install agent -f agent.yaml libre/agent -n {{< param application_name >}}
    ```

## Install UI

The UI is the graphical frontend to [handle events]({{< relref "/how-to/bpmn" >}}) and [define work masters]({{< relref "/how-to/model" >}}).

> **Requirements:** The UI requires the [GraphDB](#db), [BPMN](#bpmn), [Core](#core), and [Router](#router) services.

After installing all other services, install the UI with these steps:

1. Forward the port from the Router API.
1. Open the UI Helm file. Update the `envVars` object with settings from the UI Keycloak client.
1. Modify any other values, as needed.
1. Install with Helm:

    ```bash
    helm install ui -f ui-overrides.yaml {{< param application_name >}}/admin-ui -n {{< param application_name >}}
    ```

If the install is successful, the UI is available on its
[default port]({{< ref "default-ports" >}}).

## Optional: Audit Trail service


The Rhize [Audit]({{< relref "/how-to/audit" >}}) service provides an audit trail for database changes to install. The Audit service uses PostgreSQL for storage.

Install Audit Service with these steps:

1. Modify the Audit trail Helm YAML file. It is *recommended* to change the PostgreSQL username and password values.

2. Install with Helm:

    ```bash
    helm install audit -f audit.yaml libre/audit -n {{< param application_name >}}
    ```

### Enable change data dapture

The Audit trail requires [change data capture (CDC)]({{< ref "track-changes" >}}) to function. To enable CDC in {{< param application_name >}} BAAS, include the following values for the Helm chart overrides:

```yaml
alpha:
  # Change Data Capture (CDC)
  cdc:
    # Enable
    enabled: true
    # If configured for security, configure in NATS url. For example `nats://username:password@nats:4222`
    nats: nats://nats:4222
    # Adjust based on high-availability requirements and cluster size.
    replicas: 1
```

### Enable Audit subgraph

To use the Audit trail in the UI, you must add the Audit trail subgraph into the router. To enable router to use and compose the subgraph:

1. Update the Router Helm chart overrides, `router.yaml`, to include:

```yaml
# Add Audit to the router subgraph url override
router:
  configuration:
    override_subgraph_url:
      AUDIT: http://audit:8084/query

# If supergraph compose is enabled
supergraphCompose:
  supergraphConfig:
    subgraphs:
    AUDIT:
      routing_url: http://audit:8084/query
      schema:
        subgraph_url: http://audit:8084/query
```

2. Update the Router deployment

```shell
$ helm upgrade --install router -f router.yaml {{< param application_name >}}/router -n {{< param application_name >}}
```

## Optional: Calendar Service (Draft)

The {{< param brand_name >}} calendar service monitors work calendar definitions and creates work calendar entries in real time, both in the [Graph DB](#db) and Timeseries DB.

> **Requirements:** The calendar service requires the [GraphDB](#db), [Keycloak](#keycloak), and [NATS](#nats) services.

> **Note:** It is a prerequisite to have a Timeseries DB installed such as [InfluxDB](https://influxdata.com/), [QuestDB](https://questdb.io) or [TimescaleDB](https://www.timescale.com/). The following instructions are specific to QuestDB.

Install the calendar service with these steps:

1. Create tables in timeseries

```sql
CREATE TABLE IF NOT EXISTS PSDT_POT(
  EquipmentId SYMBOL,
  EquipmentVersion STRING,
  WorkCalendarId STRING,
  WorkCalendarIid STRING,
  WorkCalendarDefinitionId STRING,
  WorkCalendarDefinitionEntryId STRING,
  WorkCalendarDefinitionEntryIid STRING,
  WorkCalendarEntryId STRING,
  WorkCalendarEntryIid SYMBOL,
  HierarchyScopeId STRING,
  EntryType STRING,
  ISO22400CalendarState STRING,
  isDeleted boolean,
  updatedAt TIMESTAMP,
  time TIMESTAMP,
  lockerCount INT,
  lockers STRING
) TIMESTAMP(time) PARTITION BY month
DEDUP UPSERT KEYS(time, EquipmentId, WorkCalendarEntryIid);

CREATE TABLE IF NOT EXISTS PDOT_PBT(
  EquipmentId SYMBOL,
  EquipmentVersion STRING,
  WorkCalendarId STRING,
  WorkCalendarIid STRING,
  WorkCalendarDefinitionId STRING,
  WorkCalendarDefinitionEntryId STRING,
  WorkCalendarDefinitionEntryIid STRING,
  WorkCalendarEntryId STRING,
  WorkCalendarEntryIid SYMBOL,
  HierarchyScopeId STRING,
  EntryType STRING,
  ISO22400CalendarState STRING,
  isDeleted boolean,
  updatedAt TIMESTAMP,
  time TIMESTAMP,
  lockerCount INT,
  lockers STRING
) TIMESTAMP(time) PARTITION BY month
DEDUP UPSERT KEYS(time, EquipmentId, WorkCalendarEntryIid);

CREATE TABLE IF NOT EXISTS Calendar_AdHoc(
  EquipmentId SYMBOL,
  EquipmentVersion STRING,
  WorkCalendarId STRING,
  WorkCalendarIid STRING,
  WorkCalendarDefinitionId STRING,
  WorkCalendarDefinitionEntryId STRING,
  WorkCalendarDefinitionEntryIid STRING,
  WorkCalendarEntryId STRING,
  WorkCalendarEntryIid SYMBOL,
  HierarchyScopeId STRING,
  EntryType STRING,
  ISO22400CalendarState STRING,
  isDeleted boolean,
  updatedAt TIMESTAMP,
  time TIMESTAMP,
  lockerCount INT,
  lockers STRING
) TIMESTAMP(time) PARTITION BY month
DEDUP UPSERT KEYS(time, EquipmentId, WorkCalendarEntryIid);
```

2. Modify the calendar YAML file as needed.

3. Deploy with helm 

```bash
helm install calendar-service -f calendar-service.yaml {{< param application_name >}}/calendar-service -n {{< param application_name >}}
```

## Optional: change service configuration

The services installed in the previous step have many parameters that you can configure for your performance and deployment requirements.
Review the full list in the [Service configuration]({{< relref "/reference/service-config" >}}) reference.

## Troubleshoot

For general Kubernetes issues, the [Kubernetes dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) is great for troubleshooting, and you can configure it to be accessible through the browser.

For particular problems, try these commands:

- **Is my service running?**

   To check deployment status, use this command:

   ```bash
    kubectl get deployments
    ```

    Look for the pod name and its status.

- **Access service through browser**

    Some services are accessible through the browser.
    To access them, visit local host on the service's [default port]({{< ref "default-ports" >}}).

- **I installed a service too early**.
    If you installed a service too early, use Helm to uninstall:

    ```bash
    helm uninstall {{< param db >}}
    ```

    Then perform the steps you need and reinstall when ready.
