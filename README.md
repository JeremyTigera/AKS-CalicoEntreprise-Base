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

## Installing Calico Entreprise on AKS

Prepare the log storage type for Calico Entreprise, we will use GCE Persistent Disks.<br/>
In order to do it you can create a yaml manifest called storageclasscalico.yaml<br/>
```
vi storageclasscalico.yaml
```
Copy Paste this manifest:
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: tigera-elasticsearch
provisioner: kubernetes.io/azure-file
mountOptions:
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

Apply the manifest:
```
kubectl apply -f storageclasscalico.yaml
```
Check if the _StorageClass_ has been created:
```
kubectl get storageclass tigera-elasticsearch
```
**Time to deploy the Calico Entreprise's resources or application**

Install the Tigera operator and custom resource definitions
```
kubectl create -f https://docs.tigera.io/manifests/tigera-operator.yaml
```
Install the Prometheus operator and related custom resource definitions
```
kubectl create -f https://docs.tigera.io/manifests/tigera-prometheus-operator.yaml
```
Now we have to license the product, refer to your Tigera representative to get the document.
We have to create a config.json file and insert the token.<br/>
Create a config.json file in the current directory:
```
vi config.json
```
Install your pull secret - referencing the same config.json file:
```
kubectl create secret generic tigera-pull-secret --type=kubernetes.io/dockerconfigjson -n tigera-operator --from-file=.dockerconfigjson=config.json
```
Install the AKS-specific custom resources
```
kubectl create -f https://docs.tigera.io/manifests/aks/custom-resources.yaml
```
Check the tigera status out using this command:
```
kubectl get tigerastatus
```
You can use _watch kubectl get tigerastatus_ or _kubectl get tigerastatus -w_ and wait until everything is ready.<br/>

Create the license file so we can apply the update
```
vi license.yaml
```
In order to use Calico Enterprise, you must install the license provided to you by Tigera, copy paste the license in the _license.yaml_ file and then apply the manifest.
```
kubectl apply -f license.yaml
```
You can now monitor progress of the _tigerastatus_ seens previously.

