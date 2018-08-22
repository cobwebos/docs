---
title: Azure 上的 Kubernetes - 群集自动缩放程序
description: 了解如何结合 Azure Kubernetes 服务 (AKS) 使用群集自动缩放程序来根据需要自动缩放群集。
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: d121f2744292ba64436f0722ae60cc3bc2b8dfa7
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714122"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Azure Kubernetes 服务 (AKS) 中的群集自动缩放程序 - 预览版

Azure Kubernetes 服务 (AKS) 提供一个灵活的解决方案用于在 Azure 中部署托管的 Kubernetes 群集。 随着资源需求的增大，群集自动缩放程序可让群集得到扩展，以便在设置的约束基础上满足这种需求。 群集自动缩放程序 (CA) 基于挂起的 pod 缩放代理节点，以此实现这一目的。 它会定期扫描群集以检查挂起的 pod 或空节点，并在可能的情况下增加大小。 默认情况下，CA 每隔 10 秒扫描挂起的 pod，如果有 10 分钟以上不需要某个节点，则会删除该节点。 与[横向 pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA) 配合使用时，HPA 会按需更新 pod 副本和资源。 如果在完成此 pod 缩放后没有足够的节点或者没有不需要的节点，则 CA 会做出响应，并在新节点集上安排 pod。

本文介绍如何在代理节点上部署群集自动缩放程序。 但是，由于群集自动缩放程序部署在 kube 系统命名空间中，自动缩放程序不会缩减运行该 pod 的节点。

> [!IMPORTANT]
> Azure Kubernetes 服务 (AKS) 群集自动缩放程序集成目前以**预览版**提供。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。
>

## <a name="prerequisites"></a>先决条件

本文档假设有一个已启用 RBAC 的 AKS 群集。 如果你需要 AKS 群集，请参阅 [Azure Kubernetes 服务 (AKS) 快速入门][aks-quick-start]。

 若要使用群集自动缩放程序，群集必须使用 Kubernetes v1.10.X 或更高版本，并且必须启用 RBAC。 若要升级群集，请参阅有关[升级 AKS 群集][aks-upgrade]的文章。

## <a name="gather-information"></a>收集信息

若要生成权限，使群集自动缩放程序能够在群集中运行，请运行以下 bash 脚本：

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

CLUSTER_INFO=`az aks show --name $cluster_name  --resource-group $resource_group -o json`
NODE_RESOURCE_GROUP=`echo $CLUSTER_INFO | sed -e 's/^.*"nodeResourceGroup"[ ]*:[ ]*"//' -e 's/".*//' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

完成脚本中的步骤后，脚本将以机密的形式输出详细信息，如下所示：

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

接下来，运行以下命令获取节点池的名称。 

```console
$ kubectl get nodes --show-labels
```

输出：

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

然后，提取标签 **agentpool** 的值。 群集节点池的默认名称是“nodepool1”。

现在，可以使用机密和节点池创建部署图表。

## <a name="create-a-deployment-chart"></a>创建部署图表

创建名为 `aks-cluster-autoscaler.yaml` 的文件，并将以下 YAML 代码复制到其中。

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

复制并粘贴上一步骤中创建的机密，并将其插入到文件的开头。

接下来，若要设置节点范围，请在 `command` 下填写 `--nodes` 的参数，格式为 MIN:MAX:NODE_POOL_NAME。 例如：`--nodes=3:10:nodepool1` 将最小节点数设置为 3，将最大节点数设置为 10，将节点池名称设置为 nodepool1。

然后，在 **containers** 下的映像字段中填写要使用的群集自动缩放程序版本。 AKS 需要 v1.2.2 或更高版本。 此处的示例使用 v1.2.2。

## <a name="deployment"></a>部署

运行以下命令部署群集自动缩放程序

```console
kubectl create -f aks-cluster-autoscaler.yaml
```

若要检查群集自动缩放程序是否正在运行，请使用以下命令并检查 pod 列表。 应有一个前缀为“cluster-autoscaler”的 pod。 如果看到此 pod，则表示已部署群集自动缩放程序。

```console
kubectl -n kube-system get pods
```

若要查看群集自动缩放程序的状态，请运行

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>解释群集自动缩放程序的状态

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

使用群集自动缩放程序状态可在两个不同的级别查看群集自动缩放程序的状态：群集范围和每个节点组。 由于 AKS 目前仅支持一个节点池，因此这些指标是相同的。

* 运行状况指示节点的总体运行状况。 如果群集自动缩放程序难以在群集中创建或删除节点，则此状态将更改为“Unhealthy”。 此外，还细分不同节点的状态：
    * “Ready”表示可以在节点上安排 pod。
    * “Unready”表示节点在启动后已关闭。
    * “NotStarted”表示节点尚未完全启动。
    * “LongNotStarted”表示节点在合理的限制内未能启动。
    * “Registered”表示节点已注册到组中。
    * “Unregistered”表示节点在群集提供程序端存在，但未能注册到 Kubernetes 中。
  
* 当群集确定应在其中执行纵向扩展时，可以使用 ScaleUp 进行检查。
    * 当群集中的节点数发生更改或者节点状态发生更改时，将执行转换。
    * 就绪 (Ready) 节点数是群集中可用且准备就绪的节点数。 
    * cloudProviderTarget 是群集自动缩放程序确定群集需要处理其工作负荷的节点数。

* 使用 ScaleDown 可以检查是否有适合缩减的候选项。 
    * 缩减候选项是群集自动缩放程序确定可以删除，而不会影响群集处理其工作负荷的节点。 
    * 提供的时间显示群集上次检查缩减候选项的时间，及其上次转换时间。

最后，在 Events 下面，可以查看群集自动缩放程序执行的所有扩展或缩减事件、失败或成功的事件，及其时间。

## <a name="next-steps"></a>后续步骤

若要结合横向 pod 自动缩放程序使用群集自动缩放程序，请查看[缩放 Kubernetes 应用程序和基础结构][aks-tutorial-scale]。

学习 AKS 教程，了解有关部署和管理 AKS 的详细信息。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
