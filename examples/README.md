Run the examples and experiment with taking down services and bringing them back up...

```
# start etcd
$ ./bin/etcd

# start both servers
$ ./examples/build/install/examples/bin/ping-server 5001
$ ./examples/build/install/examples/bin/ping-server 5002

# (optionally) watch with etcdctl client
$ ETCDCTL="ETCDCTL_API=3 ./bin/etcdctl

# start the client loop
$ ./examples/build/install/examples/bin/ping-client
```
