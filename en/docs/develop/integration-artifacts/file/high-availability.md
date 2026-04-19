---
title: High Availability and Coordination
description: Coordinate multiple FTP/SFTP listener instances so that only one actively polls while others act as warm standby nodes, preventing duplicate file processing.
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# High Availability and Coordination

When you deploy several copies of the same FTP/SFTP integration — for example, one per pod in a Kubernetes cluster — every copy would normally connect to the same remote directory and pick up every file. That causes duplicate processing, race conditions, and inconsistent downstream state.

Turning on **Coordination** fixes this. One copy is elected as the active node and polls the server; the others stay as warm standbys and take over automatically if the active one goes down. You only need one extra thing — a shared database where the nodes track their leader election.

## How it works

| Step | What happens |
|---|---|
| **1. Leader election** | On startup, every copy in the same `coordinationGroup` registers with the shared database. One copy is elected active. |
| **2. Heartbeat** | The active node writes a heartbeat to the database at a regular interval. |
| **3. Liveness check** | Standby nodes periodically check the active node's heartbeat. If the heartbeat goes stale, the active node is considered dead. |
| **4. Failover** | A standby is promoted to active and starts polling immediately — no manual intervention. |
| **5. Polling behaviour** | Only the active node polls the FTP server. Standby nodes skip polling entirely, consuming no FTP server resources. |

The pattern is **active-passive**: at most one node polls at a time. Per-file locking across multiple active pollers isn't supported.

## Enabling coordination

Coordination is a regular listener field — you set it on the **Ftp Listener Configuration** view the same way you set Host, Port, or Polling Interval. It is **not** tucked under Advanced Configurations.

<Tabs>
<TabItem value="ui" label="Visual Designer" default>

1. Open the listener by clicking its name under **Listeners** in the sidebar, or under **Attached Listeners** in the **FTP Integration Configuration** panel.
2. Scroll to the **Coordination** field and click **Record** to open the builder.
3. Fill in the three required fields:

   | Field | What to enter |
   |---|---|
   | **Member Id** | A unique name for this copy of the integration. Every pod/instance must have a different value. Typically sourced from a `configurable` so each deployment can set its own. |
   | **Coordination Group** | A shared name that all copies of the same listener use. Copies with matching group names coordinate; copies with different group names are independent. |
   | **Database Config** | Connection details (host, port, user, password, database) for the shared MySQL or PostgreSQL database used to track leader election. |

4. Save the listener. Deploy each copy with a different **Member Id**.

</TabItem>
<TabItem value="code" label="Ballerina Code">

Add a `coordination` record to the `ftp:Listener` configuration. Source `memberId` from a `configurable` so each deployment sets its own value; keep `coordinationGroup` identical across copies you want to coordinate.

```ballerina
import ballerina/ftp;

configurable string memberId = ?;

listener ftp:Listener ftpListener = new ({
    host: "ftp.example.com",
    port: 21,
    auth: {credentials: {username: "user", password: "pass"}},
    pollingInterval: 30,
    coordination: {
        databaseConfig: {
            host: "mysql.example.com",
            user: "coordinator",
            password: "secret",
            database: "ftp_coordination"
        },
        memberId: memberId,
        coordinationGroup: "incoming-files-group"
    }
});

service on ftpListener {
    remote function onFileText(string content, ftp:FileInfo fileInfo) returns error? {
        // Only one copy in the cluster executes this handler per file.
    }
}
```

`ftp:CoordinationConfig` fields:

| Field | Type | Default | Description |
|---|---|---|---|
| `databaseConfig` | `task:DatabaseConfig` | — | Connection details for the coordination database. Accepts `MysqlConfig` or `PostgresqlConfig`. Required. |
| `memberId` | `string` | — | Unique identifier for this node. Must be distinct across all nodes in the coordination group. Required. |
| `coordinationGroup` | `string` | — | Name of the coordination group. All listeners with the same group coordinate together. Required. |
| `livenessCheckInterval` | `int` | `30` | Seconds between heartbeat checks by standby nodes. |
| `heartbeatFrequency` | `int` | `1` | Seconds between heartbeat writes by the active node. |

</TabItem>
</Tabs>

### Picking a unique Member Id per deployment

Every running copy must carry a distinct `memberId`. Two common patterns:

- **Kubernetes** — use the pod name via the downward API, exposed as an environment variable:

  ```yaml
  env:
    - name: BAL_CONFIG_VAR_MEMBER_ID
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
  ```

- **Static deployments** — set a distinct value in each node's `Config.toml`:

  ```toml
  memberId = "node-1"   # "node-2" on the second instance, and so on
  ```

### Database behaviour

The schema is created automatically on first start — you only provide the database and credentials. MySQL and PostgreSQL are both supported; use whichever your ops team already runs.

If the coordination database becomes unreachable:

- The **active node** keeps polling — it already holds the lease.
- **Standby nodes** cannot fail over until the database is reachable again.

Make sure the coordination database itself has the availability guarantees (replicas, backups) you'd expect from critical infrastructure.

### Clock synchronization

Heartbeat-based coordination assumes reasonably synchronized clocks across nodes. Significant clock skew can cause premature failover or delayed detection. Use NTP or an equivalent time-sync service on every node.

### Separate groups for separate directories

Listeners with different `coordinationGroup` values coordinate independently. Use this to run several logical pipelines on the same cluster — each group elects its own active node without interfering with the others.

## What's next

- [FTP / SFTP](ftp-sftp.md) — service, listener, and file-handler reference
- [Scaling and high availability](../../../../deploy-operate/deploy/scaling-ha.md) — deployment-level scaling and HA strategies
