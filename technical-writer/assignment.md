# Debug Operations in Kubernetes Applications Reference

Use the [`kubectl`](https://kubernetes.io/docs/reference/kubectl/) command-line interface (CLI) to communicate with a Kubernetes (K8s) cluster control plane and perform debug operations on your Kubernetes applications. For information on  debugging clusters, refer to [Troubleshooting Clusters](https://kubernetes.io/docs/tasks/debug/debug-cluster/).

The following sections list helpful `kubectl` operations when debugging Kubernetes applications. Additional information on these operations is found in the [Kubernetes Quick Reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/).

>[!NOTE] title
>message

>[!tip] title
>messages

>[!warning] title
>message

>[!quote] title
>message

**NOTE:** If you have multiple containers within a pod, you can append `-c <container-name>` after any `<pod-name>` to specify the container to debug.

```
kubectl logs <pod-name> [-c <container-name>]          
```

## `get pods`

Use `kubectl get pods` to list all available pods and statuses in the current namespace.

### Example

```
kubectl get pods ...
```

| Options                  | Description                                                         | Returns                                                                                         |
| ------------------------ | ------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `--namespace <pod-name>` | List all pods and statuses in the specified namespace.              | `NAME`, `READY`, `STATUS`, `RESTARTS`, `AGE`                                                    |
| `--all-namespaces`       | List all pods and statuses in all namespaces within the cluster.    | `NAMESPACE`, `NAME`, `READY`, `STATUS`, `RESTARTS`, `AGE`                                       |
| `-o wide`                | List all pods in the current namespace with additional information. | `NAME`, `READY`, `STATUS`, `RESTARTS`, `AGE`, `IP`, `NODE`, `NOMINATED NODE`, `READINESS GATES` |
| `-o yaml`                | View the YAML file of the specified pod.                            | YAML file                                                                                       |

## `describe` and `logs`

Use [`kubectl describe`](#describe) or [`kubectl logs`](#logs) to gather additional details regarding your pod. Use these commands in tandem to diagnose the source of the error.

### `describe`

Use `kubectl describe` to identify immediate, high-level issues with the pod's functionality, such as scheduling and image retrieval. `kubectl describe` gives more details than `kubectl logs`.   There are two main variations of this command.

#### Example

```
kubectl describe ...
```

| Options          | Description                                     |
| ---------------- | ----------------------------------------------- |
| `pod <pod-name>` | Retrieve additional details on a specified pod. |
| `pods`           | Retrieve additional details on all pods.        |

***OUTPUT EXAMPLE HERE?*** 

### `logs`

Use `kubectl logs` to retrieve (dump) logs generated from a container within a pod or to monitor (stream) logs in real-time as they are created. `kubectl logs` is preferable to `kubectl describe` if the suspected issue lies within the pod's application, such as runtime errors. 

#### Example

```
kubectl logs ...
```

| Options                                          | Description                                                                                                                 |
| ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `<pod name>`                                     | Dump logs from all containers within specified pod.                                                                         |
| `<pod-name> -c <container-name>`                 | Dump logs from a specified container within the indicated pod.                                                              |
| `<pod-name> --previous`                          | Dump logs from all containers of the previous`<pod-name>` instance.                                                         |
| `<pod-name> -c <container-name> --previous`      | Dump logs from the specified container of the previous `<pod-name>` instance.                                               |
| `-l name=myLabel`                                | ***CAN'T VERIFY THIS ONE--FIGURE IT OUT!*** Dump logs from containers within pod that have a label of `<pod-label>`.        |
| `-l name=myLabel -c <container-name>`            | ***SAME. CURSE LABELS.*** Dump logs from a specified container within the indicated pod that have a label of `<pod-label>`. |
| `-f <pod-name>`                                  | ***HOW DO WE TERMINATE?*** Stream logs from all containers within a specified pod.                                          |
| `-f <pod-name> -c <container-name>`              | Stream logs from a specified container within the indicated pod.                                                            |
| `-f <pod-name> -l name=myLabel --all-containers` | Stream logs from all containers within `<pod-name>` that have a label of `<pod-label>`.                                     |

**TIP:** If a pod cannot start or keeps crashing, you may not be able to retrieve any logs. In this situation, create a clone of the pod using [`kubectl debug`](#debug) and refer to the clone's logs for additional troubleshooting.

## `exec`

Execute a command on a running container using `kubectl exec`.

Commands can be performed with or without a shell; using a shell allows you to interact with the container itself and bypass the pod, making it easier to debug or inspect the applications and services in the container. For information on setting up a shell in a running container, refer to the official [Kubernetes documentation](https://kubernetes.io/docs/tasks/debug/debug-application/get-shell-running-container/).   

### Example

Use the below syntax to execute a command. 

```
kubectl exec <pod-name> -- <command> 
```

Following is a list of common commands used with `kubectl exec`.

| Command                                     | Description                                  |
| ------------------------------------------- | -------------------------------------------- |
| `ls /<directory-path>`                      | List files in the indicated directory.       |
| `du -sh /<directory-path>`                  | Check disk usage in the indicated directory. |
| `printenv`                                  | Print environmental variables.               |
| `ps -aux`                                   | Check running processes.                     |
| `cat /<directory-path>/myapp.log`           | View logs from a specific file.              |
| `top`                                       | Check resource usage.                        |
| `ping <service-name>`                       | Ping service.                                |
| `curl http://<service-name>:<service-port>` | Test HTTP connectivity.                      |
| `touch /<directory-path>/file.txt`          | Create new file.                             |
| `/<directory-path>/file.sh`                 | Run a script.                                |

## `debug`

Use  `kubectl debug` to create an interactive clone of a pod or container that is not terminated if an error occurs within a container. Clones are separate from and do not interact with the pod or container they are created from, making them optimal for debugging. 

### Example

```
kubectl debug ...
```

| Options                                                    | Description                                                                                                      |
| ---------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `<pod-name> -c <container-name> -it --image=<debug-image>` | Create a temporary debug container in a running pod. Append `-- bin/sh` to interact with the pod within a shell. |
| `<pod-name> --image=<debug-image> --copy-to=<new-pod>`     | Create a copy of a crashed pod.                                                                                  |

For more information on (debugging Kubernetes applications?), refer to (official documentation link here).


#  Debug Operations in Kubernetes - Original

Kubernetes contains several commands, sometimes we can use these commands to do things. A good command to know is kubectl get pods which is used to get a list of all pods that are available and what their status is. Just rememember that when you use this command tat you may have to specify the `namespace`.

```shell
kubectl get pods --namespace 
```

Speaking of commands, kubectl is the CLI that is used to interact with k8s. The kubectl cli commmunicates with the kubernettes API server.  Another command that is helpful is the kubectl logs command. In Azure, kubernetess is available, just like other cloud providers. This command is used to retrive the logs of a specific pod - do use this when you have to review logs or need to debug a container. Another we will dicuss is the `kubectl exec` command. A command that we can use to debug a container from the inside or to explore the the enviroment of the container itself.  I recommend when debugging you start with kubectl get pods, then `kubectl logs` and lastly we can use `kubectl exec` to explore the inside of the container and review other log files or configurations. 

**Note:** The command `kubectl debug` is another option to considering when debugging a container. This command can be used to create a clone of a pod that does not terminate if an error is experienced inside the container. 



# References

- https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-

- [What is Kubernetes](https://kubernetes.io/docs/concepts/overview/)
