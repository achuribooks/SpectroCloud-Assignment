# Debug Kubernetes Application Reference

Use the [`kubectl`](https://kubernetes.io/docs/reference/kubectl/) command-line interface to communicate with a [Kubernetes](https://kubernetes.io/docs/concepts/overview/) cluster control plane and perform debug operations on your Kubernetes applications. For information on debugging clusters, refer to [Troubleshooting Clusters](https://kubernetes.io/docs/tasks/debug/debug-cluster/).

The following sections list helpful `kubectl` commands and flags for debugging applications. Additional information on these operations can be found in the [Kubernetes Getting Started](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-) guide.

## `get pod`

Use `kubectl get pod` to list all available pods and their statuses in the current namespace. Append `<pod-name>` to view information regarding a specific pod. To view a list of namespaces, use `kubectl get namespace`.

### Example

```
kubectl get pod [<pod-name>] ...
```

| Options | Description | Output |
| --- | --- | --- |
| `--namespace <namespace-name>` | List pods and statuses in the specified namespace. | `NAME`, `READY`, `STATUS`, `RESTARTS`, `AGE` |
| `--all-namespaces` | List pods and statuses in all namespaces within the cluster. | `NAMESPACE`, `NAME`, `READY`, `STATUS`, `RESTARTS`, `AGE` |
| `-o wide` | List additional information for pods within the current namespace. | `NAME`, `READY`, `STATUS`, `RESTARTS`, `AGE`, `IP`, `NODE`, `NOMINATED NODE`, `READINESS GATES` |
| `-o yaml` | View the YAML file of pods within the current namespace. | YAML file |

## `describe` and `logs`

Use [`kubectl describe`](#describe) or [`kubectl logs`](#logs) to gather additional pod details. Use these commands in tandem to diagnose the source of the error.

### `describe`

Use `kubectl describe` to identify immediate, high-level issues with pod functionality, such as scheduling and image retrieval. `kubectl describe` provides more details than `kubectl logs`.   

```
kubectl describe pod [<pod-name>] ...
```

### `logs`

Use `kubectl logs <pod-name>` to retrieve (dump) logs generated from a container within a pod or to monitor (stream) logs in real-time as they are created. `kubectl logs` is preferable to `kubectl describe` if the suspected issue is within the pod's application, such as runtime errors. 

If you have multiple containers within a pod, append `-c <container-name>` to any `<pod-name>` to specify which container to debug.

#### Example

```
kubectl logs <pod-name> [-c <container-name>] ...
```

| Options  | Description |
| --- | --- |          
| `-f` | Stream logs from containers within `<pod-name>`. |
| `-l <label-key>=<label-name>` | Dump logs from containers within `<pod-name>` that have a label of `<label-name>`. |
| `--previous` | Dump logs from containers of the previous `<pod-name>` instance. |  

>**TIP:** If a pod cannot start or keeps crashing, you may not be able to retrieve logs. In this situation, create a clone of the pod using [`kubectl debug`](#debug) and refer to the clone's logs for additional troubleshooting.

## `exec`

Execute a command on a running container using `kubectl exec`.

Commands can be performed with or without a shell; using a shell allows you to interact with the container and bypass the pod, making it easier to debug or inspect applications in the container. For information on setting up a shell in a running container, refer to the official [Kubernetes documentation](https://kubernetes.io/docs/tasks/debug/debug-application/get-shell-running-container/).   

Generally, any command that can be used in a terminal can be used with `kubectl exec`, so long as the command is installed on the container's image.

```
kubectl exec <pod-name> -- <command> 
```

## `debug`

Use `kubectl debug` to create an interactive clone of a pod or container without affecting the original. The clone is not terminated if an error occurs within a container, making it optimal for debug operations.

### Example

```
kubectl debug <pod-name> ...
```

| Options | Description |
| --- | --- |
| `-c <container-name> -it --image=<debug-image>` | Create a temporary debug container in a running pod. |
| `--image=<debug-image> --copy-to=<new-pod>`     | Create a new pod based on a crashed pod. |

For more information on debugging Kubernetes applications, refer to the official [Kubernetes documentation](https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/).