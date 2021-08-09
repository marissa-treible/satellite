---

copyright:
  years: 2020, 2021
lastupdated: "2021-07-26"

keywords: azure storage, satellite storage, satellite config, satellite configurations, 

subcollection: satellite

---

{:DomainName: data-hd-keyref="APPDomain"}
{:DomainName: data-hd-keyref="DomainName"}
{:android: data-hd-operatingsystem="android"}
{:api: .ph data-hd-interface='api'}
{:apikey: data-credential-placeholder='apikey'}
{:app_key: data-hd-keyref="app_key"}
{:app_name: data-hd-keyref="app_name"}
{:app_secret: data-hd-keyref="app_secret"}
{:app_url: data-hd-keyref="app_url"}
{:authenticated-content: .authenticated-content}
{:beta: .beta}
{:c#: data-hd-programlang="c#"}
{:cli: .ph data-hd-interface='cli'}
{:codeblock: .codeblock}
{:curl: .ph data-hd-programlang='curl'}
{:deprecated: .deprecated}
{:dotnet-standard: .ph data-hd-programlang='dotnet-standard'}
{:download: .download}
{:external: target="_blank" .external}
{:faq: data-hd-content-type='faq'}
{:fuzzybunny: .ph data-hd-programlang='fuzzybunny'}
{:generic: data-hd-operatingsystem="generic"}
{:generic: data-hd-programlang="generic"}
{:gif: data-image-type='gif'}
{:go: .ph data-hd-programlang='go'}
{:help: data-hd-content-type='help'}
{:hide-dashboard: .hide-dashboard}
{:hide-in-docs: .hide-in-docs}
{:important: .important}
{:ios: data-hd-operatingsystem="ios"}
{:java: .ph data-hd-programlang='java'}
{:java: data-hd-programlang="java"}
{:javascript: .ph data-hd-programlang='javascript'}
{:javascript: data-hd-programlang="javascript"}
{:new_window: target="_blank"}
{:note .note}
{:note: .note}
{:objectc data-hd-programlang="objectc"}
{:org_name: data-hd-keyref="org_name"}
{:php: data-hd-programlang="php"}
{:pre: .pre}
{:preview: .preview}
{:python: .ph data-hd-programlang='python'}
{:python: data-hd-programlang="python"}
{:route: data-hd-keyref="route"}
{:row-headers: .row-headers}
{:ruby: .ph data-hd-programlang='ruby'}
{:ruby: data-hd-programlang="ruby"}
{:runtime: architecture="runtime"}
{:runtimeIcon: .runtimeIcon}
{:runtimeIconList: .runtimeIconList}
{:runtimeLink: .runtimeLink}
{:runtimeTitle: .runtimeTitle}
{:screen: .screen}
{:script: data-hd-video='script'}
{:service: architecture="service"}
{:service_instance_name: data-hd-keyref="service_instance_name"}
{:service_name: data-hd-keyref="service_name"}
{:shortdesc: .shortdesc}
{:space_name: data-hd-keyref="space_name"}
{:step: data-tutorial-type='step'}
{:subsection: outputclass="subsection"}
{:support: data-reuse='support'}
{:swift: .ph data-hd-programlang='swift'}
{:swift: data-hd-programlang="swift"}
{:table: .aria-labeledby="caption"}
{:term: .term}
{:terraform: .ph data-hd-interface='terraform'}
{:tip: .tip}
{:tooling-url: data-tooling-url-placeholder='tooling-url'}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:tsSymptoms: .tsSymptoms}
{:tutorial: data-hd-content-type='tutorial'}
{:ui: .ph data-hd-interface='ui'}
{:unity: .ph data-hd-programlang='unity'}
{:url: data-credential-placeholder='url'}
{:user_ID: data-hd-keyref="user_ID"}
{:vbnet: .ph data-hd-programlang='vb.net'}
{:video: .video}


# Azure Disk CSI driver
{: #config-storage-azure-csi}

The Azure Disk CSI driver implements the CSI specification for container orchestrators to manage the lifecycle of Azure Disk volumes.
{: shortdesc}

For an overview of the available features of the Azure Disk CSI driver, see [Features](https://github.com/kubernetes-sigs/azuredisk-csi-driver#features){: external}.

The Azure Disk CSI driver template for {{site.data.keyword.satelliteshort}} is currently available in beta and should not be used for production workloads.
{: beta}

  
## Prerequisites
{: #sat-storage-azure-csi-prereq}

Set up [Azure Disk storage](https://docs.microsoft.com/en-us/azure/aks/azure-disk-csi){: external} for {{site.data.keyword.satelliteshort}} clusters by creating a storage configuration in your location. When you assign a storage configuration to your clusters, the storage drivers of the selected storage provider are installed in your cluster.
{: shortdesc}

To use the Azure Disk CSI driver storage template, complete the following tasks:

1. [Create a {{site.data.keyword.satelliteshort}} location](/docs/satellite?topic=satellite-locations).
2. [Create a {{site.data.keyword.satelliteshort}} cluster](/docs/satellite?topic=openshift-satellite-clusters) that runs on compute hosts in Azure. For more information about how to add hosts from Azure to your {{site.data.keyword.satelliteshort}} location so that you can assign them to a cluster, see [Adding Azure hosts to {{site.data.keyword.satelliteshort}}](/docs/satellite?topic=satellite-azure).
3. [Add your {{site.data.keyword.satelliteshort}} cluster to a cluster group](/docs/satellite?topic=satellite-cluster-config#setup-clusters-satconfig-groups).
4. [Label your worker nodes](#azure-disk-label-nodes).
5. [Create your configuration file](#azure-disk-config-file).

### Labeling your worker nodes
{: #azure-disk-label-nodes}

Complete the following steps to add the required labels to your worker nodes for the Azure Disk CSI driver template.
{: shortdesc}

1. List your Azure worker nodes and make a note of the `name` of each node.
    ```sh
    oc get nodes
    ```
    {: pre}

1. Get the details of each node and make a note of the `zone` that the node is in. For example: `eastus-1`.
  ```sh
  oc get nodes <node-name> -o yaml | grep zone
  ```
  {: pre}

1. Label your worker nodes with the `zone` value that you retrieved earlier. Replace `<node-name>` and `<zone>` with the node name and zone of your worker node. For example, if you have a worker node in zone: `eastus-1`, use the following commands to add `eastus-1` as a label to the worker node in the `eastus-1` zone.
    ```
    oc label node <node-name> topology.kubernetes.io/zone-
    oc label node <node-name> topology.kubernetes.io/zone=<zone> --overwrite
    ```
    {: pre}

1. Repeat the previous steps for each worker node.

### Creating your Azure configuration file
{: #azure-disk-config-file}

Create a configuration file that contains your Azure Disk settings.
{: shortdesc}

1. Copy the following cloud provider configuration JSON and save it as a filed called `azure.json` on your local machine. For more information about the cloud provider configuration file parameters, see [Cluster config](https://kubernetes-sigs.github.io/cloud-provider-azure/install/configs/#cluster-config).
    ```json
    {
        "cloud":"AzurePublicCloud",                    // required
        "tenantId": "xxxx-xxxx-xxxx-xxxx-xxxx",        // required
        "subscriptionId": "xxxx-xxxx-xxxx-xxxx-xxxx",  // required
        "resourceGroup": "resource-group-name",        // required
        "location": "location",                         // required
        "aadClientId": "xxxx-xxxx-xxxx-xxxx-xxxx",     // required if using service principal
        "aadClientSecret": "xxxx-xxxx-xxxx-xxxx-xxxx", // required if using service principal
        "useManagedIdentityExtension": false,          // set true if using managed identity
        "userAssignedIdentityID": "",                  // required if using managed identity
        "useInstanceMetadata": true,                   // optional
        "vmType": "standard",                          // optional
        "subnetName": "k8s-subnet",                    // optional
        "vnetName": "k8s-vnet-11111111",               // optional
        "vnetResourceGroup": "",                       // optional
        "cloudProviderBackoff": true                   // optional
    }
    ```
    {: codeblock}

1. Encode your cloud provider configuration file to base64.
  ```sh
  cat azure.json | base64 | awk '{printf $0}'; echo
  ```
  {: pre}

<br />


## Creating an Azure Disk configuration in the command line
{: #sat-storage-azure-csi-cli}

Create a storage configuration in the command line by using the Azure Disk template.
{: shortdesc}

1. Log in to the {{site.data.keyword.cloud_notm}} CLI.
    ```sh
    ibmcloud login
    ```
    {: pre}

1. Before you can create a storage configuration, follow the steps to set up a [{{site.data.keyword.satelliteshort}} location](/docs/satellite?topic=satellite-locations).
1. If you do not have any clusters in your location, [create a {{site.data.keyword.openshiftlong_notm}} cluster](/docs/openshift?topic=openshift-satellite-clusters) or [attach existing {{site.data.keyword.openshiftlong_notm}} clusters to your location](/docs/satellite?topic=satellite-cluster-config#existing-openshift-clusters).

1. List your {{site.data.keyword.satelliteshort}} locations and note the `Managed from` column.
    ```
    ibmcloud sat location ls
    ```
    {: pre}

1. Target the `Managed from` region of your {{site.data.keyword.satelliteshort}} location. For example, for `wdc` target `us-east`. For more information, see [{{site.data.keyword.satelliteshort}} regions](/docs/satellite?topic=satellite-sat-regions).
    ```sh
    ibmcloud target -r us-east
    ```
    {: pre}

1. If you use a resource group other than `default`, target it.
    ```sh
    ibmcloud target -g <resource-group>
    ```
    {: pre}
1. Review the [template parameters](#sat-storage-azure-disk-params-cli).
1. Create storage configuration. You can pass parameters by using the `-p "key=value"` format. For more information, see the `ibmcloud sat storage config create --name` [command](/docs/satellite?topic=satellite-satellite-cli-reference#cli-storage-config-create).
  ```sh
  ibmcloud sat storage config create --name <config_name> --location <location> --template-name azuredisk-csi-driver --template-version 1.4.0 -p "cloud-config=<base64-encoded-config-file>"
  ```
  {: pre}

1. Verify that your storage configuration is created.
  ```sh
  ibmcloud sat storage config get --config <config>
  ```
  {: pre}

1. [Assign your storage configuration to clusters](#assign-storage-azure).

<br />

## Assigning your Azure storage configuration to a cluster
{: #assign-storage-azure}

After you [create a {{site.data.keyword.satelliteshort}} storage configuration](#config-storage-azure-csi), you can assign you configuration to your {{site.data.keyword.satelliteshort}} clusters.
{: shortdesc}



1. List your {{site.data.keyword.satelliteshort}} storage configurations and make a note of the storage configuration that you want to assign to your clusters.
  ```sh
  ibmcloud sat storage config ls
  ```
  {: pre}

1. Get the ID of the cluster or cluster group that you want to assign storage to. To make sure that your cluster is registered with {{site.data.keyword.satelliteshort}} Config or to create groups, see [Setting up clusters to use with {{site.data.keyword.satelliteshort}} Config](/docs/satellite?topic=satellite-cluster-config#setup-clusters-satconfig).
  * **Group**
    ```sh
    ibmcloud sat group ls
    ```
    {: pre}

  * **Cluster**
    ```sh
    ibmcloud oc cluster ls --provider satellite
    ```
    {: pre}

  * **{{site.data.keyword.satelliteshort}}-enabled service cluster**
    ```sh
    ibmcloud sat service ls --location <location>
    ```
    {: pre}

1. Assign storage to the cluster or group that you retrieved in step 2. Replace `<group>` with the ID of your cluster group or `<cluster>` with the ID of your cluster. Replace `<config>` with the name of your storage config, and `<name>` with a name for your storage assignment. For more information, see the `ibmcloud sat storage assignment create` [command](/docs/satellite?topic=satellite-satellite-cli-reference#cli-storage-assign-create).

  * **Group**
    ```sh
    ibmcloud sat storage assignment create --group <group> --config <config> --name <name>
    ```
    {: pre}

  * **Cluster**
    ```sh
    ibmcloud sat storage assignment create --cluster <cluster> --config <config> --name <name>
    ```
    {: pre}

  * **{{site.data.keyword.satelliteshort}}-enabled service cluster**
    ```sh
    ibmcloud sat storage assignment create --service-cluster-id <cluster> --config <config> --name <name>
    ```
    {: pre}

1. Verify that your assignment is created.
  ```sh
  ibmcloud sat storage assignment ls (--cluster <cluster_id> | --service-cluster-id <cluster_id>) | grep <storage-assignment-name>
  ```
  {: pre}
5. Verify that the storage configuration resources are deployed.
  ```sh
  kubectl get pods -n kube-system | grep azure
  ```
  {: pre}

  **Example output**
  ```sh
  csi-azuredisk-controller-849d854b96-6jbjg   5/5     Running   0          167m
  csi-azuredisk-controller-849d854b96-lkplx   5/5     Running   0          167m
  csi-azuredisk-node-7qwlj                    3/3     Running   6          167m
  csi-azuredisk-node-8xm4c                    3/3     Running   6          167m
  csi-azuredisk-node-snsdb                    3/3     Running   6          167m
  ```
  {: screen}

1. List the Azure Disk storage classes.
  ```sh
  oc get sc | grep azure
  ```
  {: pre}

  **Example output**
  ```sh
  sat-azure-block-bronze           disk.csi.azure.com   Delete          Immediate              true                   167m
  sat-azure-block-bronze-metro     disk.csi.azure.com   Delete          WaitForFirstConsumer   true                   167m
  sat-azure-block-gold             disk.csi.azure.com   Delete          Immediate              true                   167m
  sat-azure-block-gold-metro       disk.csi.azure.com   Delete          WaitForFirstConsumer   true                   167m
  sat-azure-block-platinum         disk.csi.azure.com   Delete          Immediate              true                   167m
  sat-azure-block-platinum-metro   disk.csi.azure.com   Delete          WaitForFirstConsumer   true                   167m
  sat-azure-block-silver           disk.csi.azure.com   Delete          Immediate              true                   167m
  sat-azure-block-silver-metro     disk.csi.azure.com   Delete          WaitForFirstConsumer   true                   167m
  ```
  {: screen}

1. [Deploy an app that uses your Azure Disk storage](#storage-azure-csi-app-deploy).

<br />

## Deploying an app that uses your Azure Disk storage
{: #storage-azure-csi-app-deploy}

You can use the Azure Disk driver to create PVCs that you can use in your cluster workloads.
{: shortdesc}

1. Create a PVC that references an Azure Disk storage class that you created earlier.
   ```yaml
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: pvc-azuredisk
  spec:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 10Gi
    storageClassName: sat-azure-block-silver
  ```
  {: codeblock}

1. Create the PVC in your cluster.
   ```sh
   oc apply -f pvc-azuredisk.yaml
   ```
   {: pre}

1. Verify that the PVC is created and the status is `Bound`.
  ```sh
  oc get pvc
  ```
  {: pre}

1. Create a YAML configuration file for a stateful set that mounts the PVC that you created.
  ```yaml
  apiVersion: apps/v1
  kind: StatefulSet
  metadata:
    name: statefulset-azuredisk
    labels:
      app: nginx
  spec:
    podManagementPolicy: Parallel  # default is OrderedReady
    serviceName: statefulset-azuredisk
    replicas: 1
    template:
      metadata:
        labels:
          app: nginx
      spec:
        nodeSelector:
          "kubernetes.io/os": linux
        containers:
          - name: statefulset-azuredisk
            image: mcr.microsoft.com/oss/nginx/nginx:1.19.5
            command:
              - "/bin/bash"
              - "-c"
              - set -euo pipefail; while true; do echo $(date) >> /mnt/azuredisk/outfile; sleep 1; done
            volumeMounts:
              - name: persistent-storage
                mountPath: /mnt/azuredisk
    updateStrategy:
      type: RollingUpdate
    selector:
      matchLabels:
        app: nginx
    volumeClaimTemplates:
      - metadata:
          name: persistent-storage
          annotations:
            volume.beta.kubernetes.io/storage-class: sat-azure-block-silver
        spec:
          accessModes: ["ReadWriteOnce"]
          resources:
            requests:
              storage: 10Gi
   ```
   {: codeblock}

1. Create the pod in your cluster.
   ```sh
   oc apply -f statefulset-azuredisk.yaml
   ```
   {: pre}

1. Verify that the pod is deployed. Note that it might take a few minutes for your app to get into a `Running` state.
   ```sh
   oc get pods
   ```
   {: pre}

   Example output:
   ```sh
   NAME                                READY   STATUS    RESTARTS   AGE
   statefulset-azuredisk       1/1     Running   0          2m58s
   ```
   {: screen}

1. Verify that the app can write to your Azure Disk by logging in to your pod.
      ```sh
      oc exec statefulset-azuredisk -it bash
      ```
      {: pre}

1. View the contents of the `outfile` file to confirm that your app can write data to your persistent storage.
  ```sh
  cat /mnt/azuredisk/outfile
  ```
  {: pre}

  Example output:
  ```sh
  Fri Jul 16 07:49:39 EDT 2021
  Fri Jul 16 07:49:39 EDT 2021
  Fri Jul 16 07:49:39 EDT 2021
  ```
  {: screen}

1. Exit the pod.
  ```sh
  exit
  ```
  {: pre}


<br />

## Removing Azure Disk storage from your apps
{: #azure-disk-rm}

If you no longer need your Azure Disk configuration, you can remove your apps, PVCs, PVs, and assignment from your clusters.
{: shortdesc}

1. List your PVCs and note the name of the PVC that you want to remove.
   ```sh
   oc get pvc
   ```
   {: pre}

1. Remove any pods that mount the PVC.
   1. List all the pods that currently mount the PVC that you want to delete. If no pods are returned, you do not have any pods that currently use your PVC.
      ```sh
      oc get pods --all-namespaces -o=jsonpath='{range .items[*]}{"\n"}{.metadata.name}{":\t"}{range .spec.volumes[*]}{.persistentVolumeClaim.claimName}{" "}{end}{end}' | grep "<pvc_name>"
      ```
      {: pre}

      Example output:
      ```
      app    sat-azure-block-platinum
      ```
      {: screen}

   1. Remove the pod that uses the PVC. If the pod is part of a deployment, remove the deployment.
      ```sh
      oc delete pod <pod_name>
      ```
      {: pre}

      ```sh
      oc delete deployment <deployment_name>
      ```
      {: pre}

   1. Verify that the pod or the deployment is removed.
      ```sh
      oc get pods
      ```
      {: pre}

      ```sh
      oc get deployments
      ```
      {: pre}

1. Delete the PVC. Because all of the Azure Disk storage classes are specified with a `Delete` reclaim policy, the PV and the disks in your Azure account are automatically deleted when you delete the PVC.
   ```sh
   oc delete pvc <pvc_name>
   ```
   {: pre}

1. Verify that your PV is automatically removed.
  ```sh
  oc get pv
  ```
  {: pre}


<br />

## Removing the Azure Disk storage configuration from your cluster
{: #azure-disk-template-rm}

If you no longer plan on using Azure Disk storage in your cluster, you can use the CLI unassign your cluster from the storage configuration.
{: shortdesc}

Removing the storage configuration uninstalls the driver from all assigned clusters. Your PVCs, PVs, and data are not removed. However, you might not be able to access your data until you re-install the driver in your cluster again.
{: important}

1. List your storage assignments and find the one that you used for your cluster.
   ```sh
   ibmcloud sat storage assignment ls (--cluster <cluster_id> | --service-cluster-id <cluster_id>)
   ```
   {: pre}

2. Remove the assignment. After the assignment is removed, the driver pods and storage classes are removed from all clusters that were part of the storage assignment.
   ```sh
   ibmcloud sat storage assignment rm --assignment <assignment_ID>
   ```
   {: pre}

3. Verify that the driver is removed from your cluster.
   1. List of the storage classes in your cluster and verify that the storage classes are removed.
      ```sh
      oc get sc
      ```
      {: pre}

   2. List the pods in the `kube-system` namespace and verify that the storage driver pods are removed.
      ```sh
      oc get pods -n kube-system | grep azure
      ```
      {: pre}

4. Optional: Remove the storage configuration.
   1. List the storage configurations.
      ```sh
      ibmcloud sat storage config ls
      ```
      {: pre}

   2. Remove the storage configuration.
      ```sh
      ibmcloud sat storage config rm --config <config_name>
      ```
      {: pre}


<br />

## Parameter reference
{: #sat-storage-azure-disk-params-cli}

| Parameter | Required? | Description | Default value if not provided |
| --- | --- | --- | --- |
| `cloud-config` | Required | Enter the base64 encoded value that you created from your `azure.json` conifguration file. | N/A |
{: caption="Table 1. Parameter reference for Azure Disk storage" caption-side="top"}
{: summary="The rows are read from left to right. The first column is the parameter name. The second column is a brief description of the parameter."}

<br />

## Storage class reference
{: #azure-disk-sc-ref}

| Storage class name | IOPS range per disk | Size range | Disk type | Reclaim policy | Volume Binding Mode |
| --- | --- | --- | --- | --- | --- |
| `sat-azure-block-platinum` |  1200 - 160000 | 4 GiB - 64 TiB | SSD | Delete | Immediate |
| `sat-azure-block-platinum-metro`  | 1200 - 160000 | 4 GiB - 64 TiB | SSD | Delete | WaitForFirstConsumer |
| `sat-azure-block-gold` | 120 - 20000 | 32 GiB - 32 TiB | SSD | Delete | Immediate |
| `sat-azure-block-gold-metro` | 120 - 20000 | 32 GiB - 32 TiB | SSD | Delete | WaitForFirstConsumer |
| `sat-azure-block-silver`  | 120 - 6000 | N/A | SSD | Delete | Immediate |
| `sat-azure-block-silver-metro` | 120 - 6000 | N/A | SSD | Delete | WaitForFirstConsumer |
| `sat-azure-block-bronze`  | 500 - 2000 | 32 GiB - 32 TiB | HDD | Delete | Immediate |
| `sat-azure-block-bronze-metro` | 500 - 2000 | 32 GiB - 32 TiB | HDD | Delete | WaitForFirstConsumer |
{: caption="Table 2. Storage class reference for Azure Disk storage" caption-side="top"}
{: summary="The rows are read from left to right. The first column is the storage class name. The second column is the IOPs range per disk. The third column is the size range . The fourth column is the disk type. The fifth column is the reclaim policy. The sixth column is the volume binding mode."}

<br />

