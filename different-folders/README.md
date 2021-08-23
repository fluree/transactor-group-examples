## Setting Up a Transactor Group in Different Folders

You can run a transactor group on three different ports on the same server by housing each transactor's data and configuration in a different folder. When running this type of configuration, you need to make sure to change `fdb-api-port` to ensure that each transactor serves the UI to a different port. You also need to make sure every server has the exact same `fdb-group-servers`. Finally, each server must have a different `fdb-group-this-server`, and that server name needs to be one of the ones specified in `fdb-group-servers`. An example below:

Key | Fluree-1 | Fluree-2 | Fluree-3
-- | -- | -- | -- 
fdb-api-port | 8090 | 8091 | 8092
fdb-group-this-server | myserver1 | myserver2 | myserver3
fdb-group-servers  | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792 | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792 | myserver1@localhost:9790,myserver2@localhost:9791,myserver3@localhost:9792

You can navigate to `different-folders/`. You'll see that `different-folders/` contains three folders: `fluree-1`, `fluree-2`, and `fluree-3`.

You'll need to download any version of Fluree (0.13 and higher) and copy `fluree_server.jar` into each folder: `fluree-1/fluree_server.jar`, `fluree-2/fluree_server.jar`, and `fluree-3/fluree_server.jar`.

Now open three different terminal windows and navigate to `fluree-1/`, `fluree-2/`, and `fluree-3/`, respectively.  In each of those terminal windows, you can issue `./fluree_start.sh` to start. 

When you see a log like the below, you can `cmd + click` on the URL to open the admin UI for each server. 

```
INFO  fluree.db.peer.http-api - Starting web server on port: 8090 with an open API.
INFO  fluree.db.peer.http-api -
INFO  fluree.db.peer.http-api - http://localhost:8090
INFO  fluree.db.peer.http-api -
```

You can try creating new ledgers, making transactions, etc, and all of the work will be replicated on each of the three instances of Fluree that are running. 