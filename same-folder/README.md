# Setting Up a Transactor Group in One Folder

You can run a transactor group on three different ports on the same server and in the same folder by providing each running instance a different properties file, and changing key properies.

When running this type of configuration, you need to make sure to change `fdb-api-port` to ensure that each transactor serves the UI to a different port. You also need to make sure every server has the exact same `fdb-group-servers`. Finally, each server must have a different `fdb-group-this-server`, and that server name needs to be one of the ones specified in `fdb-group-servers`.

In addition, unlike when you run each instance from a different folder, you'll need to specify different `fdb-group-log-directory` and `fdb-storage-file-root`, so each instance stores its block, index, and log files in a different location.

An example below:

| Key                        | Fluree-1                                                                   | Fluree-2                                                                   | Fluree-3                                                                   |
| -------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| fdb-api-port               | 8090                                                                       | 8091                                                                       | 8092                                                                       |
| fdb-group-log-directory    | data/a/group/                                                              | data/b/group/                                                              | data/c/group/                                                              |
| fdb-storage-file-root | data/a/ledger/                                                             | data/b/ledger/                                                             | data/c/ledger/                                                             |
| fdb-group-this-server      | myserver1                                                                  | myserver2                                                                  | myserver3                                                                  |
| fdb-group-servers          | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792 | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792 | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792 |

Then you can navigate to `same-folder/` directory. You'll need to download any version of Fluree (0.13 and higher) and copy `fluree_server.jar` into `same-folder/` folder: `same-folder/fluree_server.jar`.

Now open three different terminal windows and navigate to `same-folder/` in each one.

In each of those terminal windows, you can issue `./fluree_start.sh fluree_sample_1.properties`, `./fluree_start.sh fluree_sample_2.properties`, and `./fluree_start.sh fluree_sample_3.properties` respectively.

When you see a log like the below, you can `cmd + click` on the URL to open the admin UI for each server.

```
INFO  fluree.db.peer.http-api - Starting web server on port: 8090 with an open API.
INFO  fluree.db.peer.http-api -
INFO  fluree.db.peer.http-api - http://localhost:8090
INFO  fluree.db.peer.http-api -
```

You can try creating new ledgers, making transactions, etc, and all of the work will be replicated on each of the three instances of Fluree that are running.
