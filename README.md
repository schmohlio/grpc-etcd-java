# grpc-etcd-java
Client Service Discovery for gRPC with etcd

## Usage

You will need
* a running etcd cluster
* a gRPC server that registers itself under a service directory (etcd key)
* a gRPC client that provides the service directory in the target URI

### Client-side Example

```[java]
ManagedChannelBuilder
    .forTarget("etcd:///" + SERVICE_DIR) // note that the Authority is omitted
    .nameResolverFactory(EtcdNameResolverProvider.forEndpoints(endpoints))
    .loadBalancerFactory(RoundRobinLoadBalancerFactory.getInstance())
    .usePlaintext(true)
    .build();
```

### Server-side Example

Pseudocode:
```[java]
long leaseId = etcd.getLeaseClient().grant(TTL).get().getID();

etcd.getKVClient().put(
        ByteSequence.from(PREFIX + getCurrentUri(), Charsets.US_ASCII),
        ByteSequence.from(Long.toString(leaseId), Charsets.US_ASCII),
        PutOption.newBuilder().withLeaseId(leaseId).build());

etcd.getLeaseClient().keepAlive(leaseId, new EtcdServiceRegisterer());
});
```
