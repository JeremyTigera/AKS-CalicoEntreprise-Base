# Calico Entreprise for Azure AKS
This is the walkthrough to install a fresh Calico Entreprise in a newly deployed AKS

## Resources needed
Calico Entreprise documentations:
https://docs.tigera.io/about/about-calico-enterprise

Azure portal:
https://portal.azure.com/#home

How to deploy an Azure Kubernetes cluster:
https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough-portal

>_You can create the AKS Cluster with Calico Open Source CNI._

## Verifying the Cluster health
Start the cluster if not already started and use the Shell button to access it.<br/>
You can also click on the _Connect_ button in order to get access to your cluster.<br/>
You will be required to enter your subscription and credentials.<br/>

Check your nodes out:
```
kubectl get nodes
```

Then, you can run the command below to display everything running on your freshly deployed cluster:
```
kubectl get all -A
```

>_If you deployed the cluster with Calico Open Source, you will notice the __calico-system__ namespace._

Now everything is ready, we can proceed to the **Calico Entreprise** installation.
