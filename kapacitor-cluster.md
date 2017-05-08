# Kapacitor Clustering

Enterprise Kapacitor is capabable of running as a cluster.

Benifits of running Kapacitor as a cluster:

* High Availability - Run multiple redundant copies of tasks so that failures do not disrupt task behavior.
* Alert Deduplication - Deduplicate the alerts generated from the various tasks.

## Setting up a Kapacitor cluster

Clustered Kapacitor is simply a set of Kapacitor instances that are aware of each other.
To boostrap a cluster first start several Kapacitor instances and second tell them about each other.

What follows is a simple walk through on how to setup two or more Kapacitor instances and form a cluster.

### A note about DNS and hostnames

In order for Kapacitor instances to be able to communicate with each other they need to be able resolve each other's addresses.
The `hostname` setting for each Kapacitor instance is the DNS/IP of the instance, all other Kapacitor instances need to be able to resolve and access that address.

If your network is such that instances have different addresses for public vs private networks there are configuration settings for the advertise address of each of the respecitve services.

### Starting the first instance

Start a Kapacitor instance, we will call this instance `serverA`.

```sh
serverA$ kapacitord -config /path/to/kapacitor.conf
```

List the members of the cluster for that instance.
The list should only have one entry of itself.

```sh
serverA$ kapacitor-ctl member list
State: uninitialized
Cluster ID: 876ddfb4-1879-4f40-87e2-4080c04d3096
Local Member ID: f74f3547-efaf-4e6e-8b05-fb12b19f8287
Member ID                               Gossip Address RPC Address    API Address    Roles  Status
f74f3547-efaf-4e6e-8b05-fb12b19f8287    serverA:9090   serverA:9091   serverA:9092   worker alive
```

Notice that there are three addresses associated with the instance.
Each address exposes a service.
Below is a table laying out the purpose for each service.

| Service | Public/Private | Default Port | Network Protocol | Description                                                                                           |
| ------- | -------------- | ------------ | ---------------- | ----------                                                                                            |
| Gossip  | Private        | 9090         | TCP and UDP      | Kapacitor uses a gossip protocol to maintain cluster membership and otherwise communicate.            |
| RPC     | Private        | 9091         | TCP              | Kapacitor uses the RPC service for peer to peer communication between instances.                      |
| API     | Public         | 9092         | TCP              | Kapacitor exposes an HTTP REST API, all external systems communicate with Kapacitor via this service. |

Services that are marked private need not be exposed to any other systems, but only to other Kapacitor instances.
In other words private means, private to the cluster.

### Starting the next instance

Start another Kapacitor instance, we will call this instance `serverB`.

```sh
serverB$ kapacitord -config /path/to/kapacitor.conf
```

Again get the information for this new Kapacitor instance.

```sh
serverB$ kapacitor-ctl member list
State: uninitialized
Cluster ID: 9acd33e6-ed88-4601-98df-6b73c1c78427
Local Member ID: 13eeefdd-41b5-453f-928e-cb9c55fd2a5d
Member ID                               Gossip Address RPC Address    API Address    Roles  Status
13eeefdd-41b5-453f-928e-cb9c55fd2a5d    serverB:9090   serverB:9091   serverB:9092   worker alive
```

Now back on serverA we want to add serverB to the cluster.
To do this we tell serverA about serverB's RPC address.
ServerA will then initiate a connection to serverB over the RPC service and begin the process of joining the cluster.

```sh
serverA$ kapacitor-crtl member add serverB:9091
```

Check that both instances know about each other.

```sh
serverA$ kapacitor-ctl member list
State: initialized
Cluster ID: 876ddfb4-1879-4f40-87e2-4080c04d3096
Local Member ID: f74f3547-efaf-4e6e-8b05-fb12b19f8287
Member ID                               Gossip Address RPC Address    API Address    Roles  Status
f74f3547-efaf-4e6e-8b05-fb12b19f8287    serverA:9090   serverA:9091   serverA:9092   worker alive
13eeefdd-41b5-453f-928e-cb9c55fd2a5d    serverB:9090   serverB:9091   serverB:9092   worker alive
```

```sh
serverB$ kapacitor-ctl member list
State: initialized
Cluster ID: 876ddfb4-1879-4f40-87e2-4080c04d3096
Local Member ID: f74f3547-efaf-4e6e-8b05-fb12b19f8287
Member ID                               Gossip Address RPC Address    API Address    Roles  Status
f74f3547-efaf-4e6e-8b05-fb12b19f8287    serverA:9090   serverA:9091   serverA:9092   worker alive
13eeefdd-41b5-453f-928e-cb9c55fd2a5d    serverB:9090   serverB:9091   serverB:9092   worker alive
```

Notice now that the cluster state is `initialized` and that the clsuter IDs are the same for both isntances.

### Beyond two instances

That is all there is to it.
You can add more instances by calling the add method on any member of the cluster.


### Removing instances

Instances can also be remove as needed.
When an instance is removed from a cluster, the removed instance enters the uninitialized state and becomes a cluster of one again.

As an example to remove serverB run this command on serverA or serverB:

```sh
serverA$ kapacitor-ctl member remove 13eeefdd-41b5-453f-928e-cb9c55fd2a5d
```

