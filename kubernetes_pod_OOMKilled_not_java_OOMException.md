# Heap+Non Heap usage not matching with process usage
In kubernetes world, when JVM is the only process running in container and Heap+nonheap usage metrics are way lower than system memory usage(container_memory_working_set/
container_memory_rss). 

## Debug
look for JVM committed memory(changes over time not fixed at start of process and is reserved memory from the OS without easily freeing it up metrics). 

## Refer
▪ https://medium.com/@karthik.jeyapal/memory-settings-for-java-process-running-inkubernetes-pod-6d0a2e092ce5
▪ https://docs.oracle.com/javase%2F7%2Fdocs%2Fapi%2F%2F/java/lang/management/MemoryUsage.html

## Note
JVM total(heap + non heap) committed memory might still be less than system memory usage.

# How to tune MaxRAMPercentage value
1. start with a reasonable percentage value
2. monitor WSS/RSS is well within kubernetes limits, if not within limits:
    1. monitor maximum heap usage: if consistently high then might need to allocate more % to heap. Worst case inc. pod level total memory limits.
    2. if the maximum heap usage is OK, try with giving more space to non heap allocation. In worst case inc. pod level total memory limits.

# Monitoring Kubernetes OOM (linux OOM killed different from JVM OOM exception)
## Refer
https://sysdig.com/blog/troubleshoot-kubernetes-oom/

Kubernetes, a process can reach any of these limits:
1. A Kubernetes Limit set on the container.
2. A Kubernetes ResourceQuota set on the namespace.
3. The node’s actual Memory size.

When using node exporter in Prometheus, there’s one metric called node_vmstat_oom_kill. It’s important to track when an OOM kill happens, but you might want to get ahead and have visibility of such an event before it happens.
Instead, you can check how close a process is to the Kubernetes limits: 
```
(sum by (namespace,pod,container)
(rate(container_cpu_usage_seconds_total{container!=""}[5m])) / sum by
(namespace,pod,container)
(kube_pod_container_resource_limits{resource="cpu"})) > 0.8
```
