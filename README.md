# grpc-etcd-resolver
Client Service Discovery for gRPC with etcd

## Usage

You will need
* a running etcd cluster
* a gRPC server that registers itself under a service directory (etcd key)
* a gRPC client that provides the service directory in the target URI

### Client-side Example

```[java]
ManagedChannelBuilder
    .forTarget("etcd:///" + SERVICE_DIR)
    .nameResolverFactory(EtcdNameResolverProvider.forEndpoints(endpoints))
    .loadBalancerFactory(RoundRobinLoadBalancerFactory.getInstance())
    .usePlaintext(true)
    .build();
```

### Server-side Example

Pseudocode:
```[java]
etcd.getLeaseClient()
  .keepAlive(Duration.ofSeconds(15).toMillis(), new StreamObserver<LeaseKeepAliveResponse>() {
    @Override
    public void onNext(LeaseKeepAliveResponse value) {
        Uri uri = getCurrentUri();
        PutOption options = PutOption.newBuilder().withLeaseId(value.getID()).build();
        etcd.getKVClient().put(SERVICE_DIR, ByteSequence.fromUri(uri), options);
    }
});
```
