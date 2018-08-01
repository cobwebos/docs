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
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186707"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Azure Kubernetes 服务 (AKS) 中的群集自动缩放程序 - 预览版

Azure Kubernetes 服务 (AKS) 提供一个灵活的解决方案用于在 Azure 中部署托管的 Kubernetes 群集。 随着资源需求的增大，群集自动缩放程序可让群集得到扩展，以便在设置的约束基础上满足这种需求。 群集自动缩放程序 (CA) 基于挂起的 pod 缩放代理节点，以此实现这一目的。 它会定期扫描群集以检查挂起的 pod 或空节点，并在可能的情况下增加大小。 默认情况下，CA 每隔 10 秒扫描挂起的 pod，如果有 10 分钟以上不需要某个节点，则会删除该节点。 与横向 pod 自动缩放程序 (HPA) 配合使用时，HPA 会按需更新 pod 副本和资源。 如果在完成此 pod 缩放后没有足够的节点或者没有不需要的节点，则 CA 会做出响应，并在新节点集上安排 pod。

> [!IMPORTANT]
> Azure Kubernetes 服务 (AKS) 群集自动缩放程序集成目前以**预览版**提供。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。
>

## <a name="prerequisites"></a>先决条件

本文档假设有一个已启用 RBAC 的 AKS 群集。 如果你需要 AKS 群集，请参阅 [Azure Kubernetes 服务 (AKS) 快速入门][aks-quick-start]。

 若要使用群集自动缩放程序，群集必须使用 Kubernetes v1.10.X 或更高版本，并且必须启用 RBAC。 若要升级群集，请参阅有关[升级 AKS 群集][aks-upgrade]的文章。

## <a name="gather-information"></a>收集信息

以下列表显示了必须在自动缩放程序定义中提供的所有信息。

- *订阅 ID*：与此群集所用的订阅相对应的 ID
- *资源组名称*：群集所属的资源组的名称 
- *群集名称*：群集的名称
- *客户端 ID*：权限生成步骤授予的应用 ID
- *客户端机密*：权限生成步骤授予的应用机密
- *租户 ID*：租户（帐户所有者）的 ID
- *节点资源组*：包含群集中代理节点的资源组的名称
- *节点池名称*：要缩放的节点池的名称
- *最小节点数*：群集中存在的节点的最小数目
- *最大节点数*：群集中存在的节点的最大数目
- *VM 类型*：用于生成 Kubernetes 群集的服务

使用以下命令获取订阅 ID： 

``` azurecli
az account show --query id
```

运行以下命令生成一组 Azure 凭据：

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

应用 ID、密码和租户 ID 是后续步骤中使用的 clientID、clientSecret 和 tenantID。

运行以下命令获取节点池的名称。 

```console
$ kubectl get nodes --show-labels
```

输出：

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

然后，提取标签 **agentpool** 的值。 群集节点池的默认名称是“nodepool1”。

若要获取节点资源组的名称，请提取标签 **kubernetes.azure.com<span></span>/cluster** 的值。 节点资源组名称通常采用 MC_[resource-group]\_[cluster-name]_[location] 格式。

vmType 参数引用所用的服务（此处为 AKS）。

现在，应已获取以下信息：

- 订阅 ID
- resourceGroup
- ClusterName
- ClientID
- ClientSecret
- TenantID
- NodeResourceGroup
- VMType

接下来，请使用 base64 将所有这些值编码。 例如，若要使用 base64 将 VMType 值编码：

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>创建机密
使用此数据和上一步骤中找到的值，为部署创建采用以下格式的机密：

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

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
kubectl create -f cluster-autoscaler-containerservice.yaml
```

若要检查群集自动缩放程序是否正在运行，请使用以下命令并检查 pod 列表。 如果前缀为“cluster-autoscaler”的 pod 正在运行，则表示已部署群集自动缩放程序。

```console
kubectl -n kube-system get pods
```

若要查看群集自动缩放程序的状态，请运行

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

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
