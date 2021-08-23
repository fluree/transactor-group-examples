# Dynamic Config Change

To dynamically add a server, you must have a valid transactor group up and running. Let's use the configuration above where we run 3 Fluree instances out of the same folder.

The settings for the first three servers are as follows:

| Key                        | Fluree-1                                                                   | Fluree-2                                                                   | Fluree-3                                                                   |
| -------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| fdb-api-port               | 8090                                                                       | 8091                                                                       | 8092                                                                       |
| fdb-group-log-directory    | data/a/group/                                                              | data/b/group/                                                              | data/c/group/                                                              |
| fdb-storage-file-root | data/a/ledger/                                                             | data/b/ledger/                                                             | data/c/ledger/                                                             |
| fdb-group-this-server      | myserver1                                                                  | myserver2                                                                  | myserver3                                                                  |
| fdb-group-servers          | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792 | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792 | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792 |

To start this up, we can clone down the tx-group-ex-dirs repo.

```all
git clone https://github.com/fluree/tx-group-ex-dirs.git
```

Then you can navigate to `dynamic-config-change/` directory. You'll need to download any version of Fluree (0.13 and higher) and copy `fluree_server.jar` into `dynamic-config-change/` folder: `dynamic-config-change/fluree_server.jar`.

Now open three different terminal windows and navigate to `dynamic-config-change/` in each one.

In each of those terminal windows, you can issue `./fluree_start.sh fluree_sample_1.properties`, `./fluree_start.sh fluree_sample_2.properties`, and `./fluree_start.sh fluree_sample_3.properties` respectively.

Wait until all three servers are up and running. Now we can add a fourth server. In order to add the fourth server, we DO NOT have to shut down the first three servers. We do not have to change their settings. We only have to do two steps:

1. Start up `myserver4` with the following settings:

| Key                        | Value                                                                                               |
| -------------------------- | --------------------------------------------------------------------------------------------------- |
| fdb-join?                  | true                                                                                                |
| fdb-group-catch-up-rounds  | 10                                                                                                  |
| fdb-api-port               | 8093                                                                                                |
| fdb-group-log-directory    | data/d/group/                                                                                       |
| fdb-storage-file-root | data/d/ledger/                                                                                      |
| fdb-group-this-server      | myserver4                                                                                           |
| fdb-group-servers          | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792,myserver4@localhost:9793 |

The `fdb-join?` is the setting that tells this Fluree instance to wait before initiating. This instance will NOT initiate until another server (from fdb-group-servers) starts feeding it log files. `fdb-group-catch-up-rounds` is the number of rounds to wait until myserver4 is caught up with the other servers. If you have a lot of ledgers or blocks already on the other instances, you may want to increase this.

First we need to start `myserver4`. To do this, you can open a terminal and issue `./fluree_start.sh fluree_sample_4.properties`.

You'll see some logs like:

```all
INFO  fluree.db.peer.http-api - Starting web server on port: 8093 with an open API.
INFO  fluree.db.peer.http-api -
INFO  fluree.db.peer.http-api - http://localhost:8093
INFO  fluree.db.peer.http-api -
INFO  f.d.ledger.consensus.tcp - TCP read channel closed for server: myserver4. Closing TCP loop.
INFO  f.d.ledger.consensus.tcp - TCP read channel closed for server: myserver4. Closing TCP loop.
```

2. Issue a request to any active server (in this case, to server `myserver1`) to add `myserver4`.

```all
  curl \
   -H "Content-Type: application/json" \
   -d '{"server": "myserver4"}' \
   http://localhost:8090/fdb/add-server
```

If adding the server is successful, you should see a log like:

```all
Committing myserver4 add to the network configuration. Change command id: eeeda1b7-c939-4eea-885c-96b0e87f394b
```

If it was not successfully added, you'll see a message like:

```all
WARN  fluree.raft.leader - Server myserver4 did not sync in the allotted number of rounds. Please delete this server's files, and attempt to add again. Depending on your network's connectivity, you may want to increase :fdb-group-catch-up-rounds.
```

This is a beta feature, so if you encounter any issues, please email `support@flur.ee`.

### Dynamically Removing a Server

If you have a server running, you can dynamically remove a server by issue a request like the below to any server in the network (including the server to be removed).

```all
  curl \
   -H "Content-Type: application/json" \
   -d '{"server": "myserver1"}' \
   http://localhost:8090/fdb/remove-server
```
