# Debug Operations in Kubernetes Reference - Working Draft

\[intro paragraph here]
Use the kubectl command-line interface (CLI) to perform debug operations on your Kubernetes pods. 

Observations:
- SpectroCloud doesn't seem to alphabetize tables. It looks like it follows a logical order of how the commands (parameters?) are executed. Or, a natural flow of how they may be used.

| Command                      | Description                                                                                                                                           | notes to self                                                                                                                                        |     |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| `kubectl`                      | A K8s-specific CLI that interacts with a K8s cluster control plane                                                                                    |                                                                                                                                                      |     |
| `kubectl get pods --namespace` | Lists all available pods and statuses in the namespace; specify the namespace, if applicable.                                                         | mentions specifying namespace; mentions when starting the debugging process to use this command                                                      |     |
| `kubectl logs <pod name>`      | Retrieves the logs of a specific pod. For additional details on the pod's event and status, use `kubectl describe pod <pod name`.                     | mentions using after `kubectl get pods` Since the literal title of this is debug ops, this might need to be highlighted or pulled out in another way |     |
| `kubectl exec`                 | used to debug a container from the inside or explore the internal environment of the container; can also be used to review other log files or configs | says to use last/after `kubectl logs`                                                                                                                |     |
| `kubectl debug`                | used to create a clone of a pod that does not terminate if an error happens, giving you a better view of what's happening                             | is listed as another option when debugging a container, but in what way?                                                                             |     |







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
