# DataStax Enterprise Docker

[![Build Status](https://travis-ci.org/LukeTillman/dse-docker.svg?branch=master)](https://travis-ci.org/LukeTillman/dse-docker)

A Docker image for [DataStax Enterprise][datastax-enterprise]. Please use the 
[GitHub repository][github-repo] for opening issues.

## Usage

> **Note:** Not meant for production use. See [this whitepaper on DataStax.com][whitepaper] for 
> details on setting up DSE and Docker in production.

### Starting DSE

By default, this image will start DSE *in Cassandra only* mode. For example:

```console
docker run --name my-dse -d luketillman/datastax-enterprise:TAG
```

You should replace `TAG` in all of these examples with the version of DSE you are trying to
start. (See the [Docker Hub tags][docker-hub-tags] for a list of available versions.)

The image's entrypoint script runs the command `dse cassandra` and will append any switches you
provide to that command. So it's possible to start DSE in any of the other supported modes by
adding switches to the end of your `docker run` command.

#### Example: Start a Graph Node

```console
docker run --name my-dse -d luketillman/datastax-enterprise:TAG -g
```

In the container, this will run `dse cassandra -g` to start a graph node.

#### Example: Start an Analytics (Spark) Node

```console
docker run --name my-dse -d luketillman/datastax-enterprise:TAG -k
```

In the container, this will run `dse cassandra -k` to start an analytics node.

#### Example: Start a Search Node

```console
docker run --name my-dse -d luketillman/datastax-enterprise:TAG -s
```

In the container, this will run `dse cassandra -s` to start a search node.

You can also use combinations of those switches. For more examples, see the [Starting DSE][start-dse]
documentation.

### Starting Related Tools

With a node running, use `docker exec` to run other tools. For example, the `nodetool status` 
command:

```console
docker exec -it some-dse nodetool status
```

Or to connect with `cqlsh`:

```console
docker exec -it some-dse cqlsh
```

### Environment Variables

The following environment variables can be set at runtime to override configuration. Setting the 
following values will override the corresponding settings in the `cassandra.yaml` configuration 
file:

 - **`LISTEN_ADDRESS`**: The IP address to listen for connections from other nodes. Defaults to 
     the hostname's IP address.
 - **`BROADCAST_ADDRESS`**: The IP address to advertise to other nodes. Defaults to the same 
     value as the `LISTEN_ADDRESS`.
 - **`RPC_ADDRESS`**: The IP address to listen for client/driver connections. Defaults to 
     `0.0.0.0` (i.e. wildcard).
 - **`BROADCAST_RPC_ADDRESS`**: The IP address to advertise to clients/drivers. Defaults to the 
    same value as the `BROADCAST_ADDRESS`.
 - **`SEEDS`**: The comma-delimited list of seed nodes for the cluster. Defaults to this node's 
     `BROADCAST_ADDRESS` if not set and will only be set the first time the node is started.
 - **`START_RPC`**: Whether to start the Thrift RPC server. Will leave the default in the 
     `cassandra.yaml` file if not set.
 - **`CLUSTER_NAME`**: The name of the cluster. Will leave the default in the `cassandra.yaml` 
     file if not set.
 - **`NUM_TOKENS:`**: The number of tokens randomly assigned to this node. Will leave the 
     default in the `cassandra.yaml` file if not set.

The configuration files for DSE (under `$install_dir/resources` in the tarball) are also exposed 
as a volume (see below).

### Volumes

The following volumes are created and can be mounted to the host system:

- **`/var/lib/cassandra`**: Data from Cassandra
- **`/var/lib/spark`**: Data from DSE Analytics w/ Spark
- **`/var/log/cassandra`**: Logs from Cassandra
- **`/var/log/spark`**: Logs from Spark
- **`/opt/dse/resources`**: Most configuration files including `cassandra.yaml`, `dse.yaml`, and
    more can be found here.

### Logging

You can view logs via Docker's container logs:

```console
docker logs some-dse
```

## Builds

Build and publish scripts are available in the `build` folder of the repository. All those 
scripts are meant to be run from the root of the repository. For example:

```console
> ./build/docker-build.sh
```

Continuous integration builds are handled by Travis.


[datastax-enterprise]: http://www.datastax.com/products/datastax-enterprise
[whitepaper]: http://www.datastax.com/wp-content/uploads/resources/DataStax-WP-Best_Practices_Running_DSE_Within_Docker.pdf
[github-repo]: https://github.com/LukeTillman/dse-docker
[docker-hub-tags]: https://hub.docker.com/r/luketillman/datastax-enterprise/tags/
[start-dse]: http://docs.datastax.com/en/latest-dse/datastax_enterprise/admin/startDseStandalone.html