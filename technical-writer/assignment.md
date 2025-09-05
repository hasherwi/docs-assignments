# Debug Operations in Kubernetes with `kubectl`

One tool to debug Kubernetes operations is the `kubectl` command line interface (CLI). The `kubectl` CLI communicates with the Kubernetes application programming interface (API) server.

## Prerequisites

- Install `kubectl`. Use a version of `kubectl` that is compatible with your Kubernetes cluster.
  - Use the Kubernetes [Install Tools](https://kubernetes.io/docs/tasks/tools/) for additional guidance.
- Configure the `$HOME/.kube/config` file to allow `kubectl` to communicate with your Kubernetes cluster.
  - See [Kubeconfig](https://docs.spectrocloud.com/clusters/cluster-management/kubeconfig/).

## Commands

### `kubectl get`

`kubectl get` provides a list of all resource objects within a namespace and their status.

```console
$ kubectl get pods
NAME                                                  READY   STATUS    RESTARTS      AGE
pod1                                                  1/1     Running   1 (24m ago)   2d23h
pod2                                                  1/1     Running   2 (12m ago)   10d1h
pod3                                                  1/1     Running   0             4d20h
pod4                                                  1/1     Running   0             12m
```

A list of supported resources within the connected Kubernetes cluster is retrieved with the command `kubectl api-resources`. See X.

> **_Note:_**
When using any `kubectl` command with namespaced resources, the `default` namespace is used unless otherwise specified. To use a different namespace use the `--namespace` or `-n` command flags. Alternatively, retrieve objects across all namespaces with the flags `--all-namespaces` or `-A`. See X.

```console
$ kubectl get services --namespace myapp-namespace
NAME                                TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service1                            LoadBalancer   10.101.94.125    localhost     8000:32259/TCP   2d23h
service2                            NodePort       10.101.105.247   <none>        6379/TCP         2d23h
service3                            ClusterIP      10.106.49.233    <none>        8080/TCP         2d23h
```

### `kubectl describe`

To retrieve all metadata about a single Kubernetes object, use the `kubectl describe` command.

```console
$ kubectl describe secret my-secret
Name:         my-secret
Namespace:    default
Labels:       app.kubernetes.io/name=myapp
              app.kubernetes.io/managed-by=Helm
Annotations:  meta.helm.sh/release-name: myapp
              meta.helm.sh/release-namespace: default

Type:  Opaque

Data
====
mysecret-password:       10 bytes
```

### `kubectl logs`

Use `kubectl logs` to retrieve the logs of a specific container. If multiple containers exist within the pod, use the command flags `--container` or `-c` to use a specific container. Use the `-f` to stream the logs rather than retrieving them once.

```console
kubectl logs <pod-name> [--container <container-name>]
```

> **_Note:_**
`kubectl logs` relies on the container's logs streaming to `STDOUT` and `STDERR`.

### `kubectl exec`

Use the `kubectl exec` command runs another command inside a container or to explore the the environment of the container. If multiple containers exist within the pod, use the command flags `--container` or `-c` to use a specific container. The command to run inside the container follows the final instance of `--`.

```console
$ kubectl exec <pod-name> -- ping -c 4 spectrocloud.com
PING spectrocloud.com (10.3.50.11): 56 data bytes
64 bytes from 10.3.50.11: seq=0 ttl=63 time=46.389 ms
64 bytes from 10.3.50.11: seq=1 ttl=63 time=44.176 ms
64 bytes from 10.3.50.11: seq=2 ttl=63 time=43.195 ms
64 bytes from 10.3.50.11: seq=3 ttl=63 time=43.454 ms

--- spectrocloud.com ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 43.195/44.303/46.389 m
```

### `kubectl debug`

Use the `kubectl debug` command to create temporary objects or make temporary changes to debug operations. For example, this command can be used to create a debugging container within a pod.

```console
kubectl debug <pod-name> -it --image=busybox
```

## Recommended Process

There are many methods to debugging. A common approach is to work "top-down". An example is below to determine the root cause of the failure of an application inside of a container.

- Use `kubectl get` to identify the status of a pod.
- Use `kubectl describe` to identify the status of a container with that pod.
- Use `kubectl logs` to look at the logs inside of the container.
- Use `kubectl exec` or `kubectl debug` to make changes within the container.

## References

- [Monitoring, Logging, and Debugging](https://kubernetes.io/docs/tasks/debug/)
- [kubectl reference](https://kubernetes.io/docs/reference/kubectl/generated/).
