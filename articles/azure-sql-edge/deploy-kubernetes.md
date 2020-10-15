---
title: 在 Kubernetes 中部署 Azure SQL Edge 容器 - Azure SQL Edge
description: 了解如何在 Kubernetes 中部署 Azure SQL Edge 容器
keywords: SQL Edge, 容器, kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 16ad757fc00439bb390a7e0dea902901c468dd1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90929230"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>在 Kubernetes 中部署 Azure SQL Edge 容器

Azure SQL Edge 既可以通过 Kubernetes 上运行的 Azure IoT Edge 作为 IoT Edge 模块部署在 Kubernetes 容器上，也可以作为独立的容器 Pod 部署在 Kubernetes 容器上。 本文的其余部分将重点介绍如何在 Kubernetes 群集上部署独立容器。 有关在 Kubernetes 上部署 Azure IoT Edge 的信息，请参阅 [Kubernetes 上的 Azure IoT Edge（预览版）](https://microsoft.github.io/iotedge-k8s-doc/introduction.html)。

本教程演示如何在 kubernetes 群集上的容器中配置高度可用的 Azure SQL Edge 实例。 

> [!div class="checklist"]
> * 创建 SA 密码
> * 创建存储
> * 创建部署
> * 使用 SQL Server Management Studio (SSMS) 进行连接
> * 验证故障和恢复

Kubernetes 1.6 及更高版本支持[存储类](https://kubernetes.io/docs/concepts/storage/storage-classes/)、[永久性卷声明](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)和 [Azure 磁盘卷类型](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk)。 可以在 Kubernetes 中本机创建和管理 Azure SQL Edge 实例。 本文中的示例介绍如何创建[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)，以实现类似于共享磁盘故障转移群集实例的高可用性配置。 在此配置中，Kubernetes 充当群集业务流程协调程序的作用。 容器中的 Azure SQL Edge 实例发生故障时，业务流程协调程序会启动附加到同一永久性存储的容器的另一实例。

![Kubernetes 群集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge.png)

在上图中，`azure-sql-edge`是 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 中的容器。 Kubernetes 协调群集中的资源。 [副本集](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)可确保在节点发生故障后自动恢复 Pod。 应用程序会连接到服务。 在这种情况下，该服务表示负载均衡器，承载着 `azure-sql-edge` 发生故障后保持不变的 IP 地址。

在下图中，`azure-sql-edge` 容器发生了故障。 作为业务流程协调程序，Kubernetes 可保证副本集中正常实例的计数正确，并根据配置启动新容器。 业务流程协调程序会在同一节点上启动新 Pod，并且 `azure-sql-edge` 会重新连接到同一个永久性存储。 该服务会连接到重新创建的 `azure-sql-edge`。

![Pod 出现故障后 Kubernetes 群集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

在下图中，托管 `azure-sql-edge` 容器的节点发生了故障。 业务流程协调程序在不同的节点上启动新 Pod，并且 `azure-sql-edge` 重新连接到同一个永久性存储。 该服务会连接到重新创建的 `azure-sql-edge`。

![节点出现故障后 Kubernetes 群集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>先决条件

* **Kubernetes 群集**
   - 本教程需要使用 Kubernetes 群集。 这些步骤使用 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) 来管理群集。 

   - 就本教程而言，我们将使用 Azure Kubernetes 服务来部署 Azure SQL Edge。 若要使用 `kubectl` 在 AKS 中创建单节点 Kubernetes 群集并连接到此群集，请参阅[部署 Azure Kubernetes 服务 (AKS) 群集](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-deploy-cluster)。 

   >[!NOTE]
   >若要防范节点发生故障，Kubernetes 群集需要使用多个节点。

* **Azure CLI**
   - 本教程中的说明已针对 Azure CLI 2.10.1 进行了验证。

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>为 SQL Edge 部署创建 kubernetes 命名空间

在 kubernetes 群集中创建新的命名空间。 此命名空间将用于部署 SQL Edge 和所有必需项目。 有关 Kubernetes 命名空间的详细信息，请参阅[命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)。

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>创建 SA 密码

在 Kubernetes 群集中创建 SA 密码。 Kubernetes 可以管理敏感的配置信息，例如密码（即[密钥](https://kubernetes.io/docs/concepts/configuration/secret/)）。

以下命令可为 SA 帐户创建密码：

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   使用复杂的密码替换 `MyC0m9l&xP@ssw0rd`。

## <a name="create-storage"></a>创建存储

请在 Kubernetes 群集中配置[永久性卷](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)和[永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection)。 请完成下列步骤： 

1. 请创建清单以定义存储类和永久性卷声明。  清单可指定存储配置程序、参数和[回收策略](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming)。 Kubernetes 群集使用此清单来创建永久性存储。 

   以下 yaml 示例定义了存储类和永久性卷声明。 存储类配置程序为 `azure-disk`，因为此 Kubernetes 群集位于 Azure 中。 存储帐户类型为 `Standard_LRS`。 永久性卷声明的名称为 `mssql-data`。 永久性卷声明元数据包含将其连接回存储类的注释。 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   保存文件（例如，pvc.yaml  ）。

2. 在 Kubernetes 中创建永久性卷声明。

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` 是保存文件的位置。

   永久性卷自动创建为 Azure 存储帐户，并绑定到永久性卷声明。 

    ![永久性卷声明命令的屏幕截图](media/deploy-kubernetes/pvc-cmd.png)

3. 验证永久性卷声明。

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` 是永久性卷声明的名称。

   在上述步骤中，永久性卷声明的名称为 `mssql-data`。 要查看有关永久性卷声明的元数据，请运行以下命令：

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   返回的元数据包含一个名为 `Volume` 的值。 此值映射到 blob 的名称。

   ![返回的元数据（包括卷）的屏幕截图](media/deploy-kubernetes/describe-volume.png)

4. 请验证永久性卷。

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` 返回有关自动创建并绑定到永久性卷声明的永久性卷的元数据。 

## <a name="create-the-deployment"></a>创建部署

在此示例中，托管 Azure SQL Edge 实例的容器被描述为 Kubernetes 部署对象。 部署会创建副本集。 副本集会创建 Pod。 

在此步骤中，请创建一个清单，用于基于 Azure SQL Edge Docker 映像描述容器。 该清单引用 `mssql-data` 永久性卷声明以及已应用于 Kubernetes 群集的 `mssql` 密钥。 清单还将描述一项[服务](https://kubernetes.io/docs/concepts/services-networking/service/)。 此服务是负载均衡器。 负载均衡器保证在恢复 Azure SQL Edge 实例后 IP 地址仍保持不变。 

1. 创建用于描述部署的清单（YAML 文件）。 下面的示例描述了部署，包括基于 Azure SQL Edge 容器映像的容器。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   请将上述代码复制到名为 `sqldeployment.yaml` 的新文件中。 请更新以下值： 

   * MSSQL_PID `value: "Developer"`：设置容器以运行 Azure SQL Edge 开发人员版。 Developer 版本未获得生产数据许可。 如果部署是用于生产，请将版本设置为 `Premium`。 

      >[!NOTE]
      >有关详细信息，请参阅[如何为 Azure SQL Edge 授予许可](https://azure.microsoft.com/pricing/details/sql-edge/)。

   * `persistentVolumeClaim`设置用户帐户 ：此值需要 `claimName:` 的条目，该条目映射到用于永久性卷声明的名称。 本教程使用 `mssql-data`。 

   * `name: SA_PASSWORD`设置用户帐户 ：请配置容器映像以设置 SA 密码，如本部分中所定义。

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Kubernetes 部署容器时，它引用名为 `mssql` 的密钥以获取密码的值。 

   >[!NOTE]
   >使用 `LoadBalancer` 服务类型，可在端口 1433（通过 Internet）远程访问 Azure SQL Edge 实例。

   保存文件（例如 sqledgedeploy.yaml）。

2. 创建部署。

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` 是保存文件的位置。

   ![部署命令的屏幕截图](media/deploy-kubernetes/deploy-sql-cmd.png)

   部署和服务现已创建。 Azure SQL Edge 实例位于连接到永久性存储的容器中。

   若要查看 Pod 的状态，请键入 `kubectl get pod -n <namespace name>`。

   ![获取 Pod 命令的屏幕截图](media/deploy-kubernetes/get-sql-pod-cmd.png)

   在上图中，Pod 的状态为 `Running`。 此状态表示容器已准备就绪。 此过程可能需要几分钟时间。

   >[!NOTE]
   >创建部署后，可能需要几分钟时间才能看到 Pod。 延迟是因为群集从 Docker 中心拉取 Azure SQL Edge 容器映像。 在第一次拉取映像后，如果是向已缓存了映像的节点进行部署，则后续部署的速度可能更快。 

3. 请验证服务是否正在运行。 运行以下命令：

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   此命令会返回正在运行的服务，以及该服务的内部和外部 IP 地址。 请记下 `mssql-deployment` 服务的外部 IP 地址。 请使用此 IP 地址连接到 Azure SQL Edge。 

   ![获取服务命令的屏幕截图](media/deploy-kubernetes/get-service-cmd.png)

   有关 Kubernetes 群集中的对象状态的详细信息，请运行：

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>连接到 Azure SQL Edge 实例

如果按照描述配置了容器，则可以从 Azure 虚拟网络外部连接应用程序。 请使用服务的 `sa` 帐户和外部 IP 地址。 请使用配置为 Kubernetes 密钥的密码。 有关连接到 Azure SQL Edge 实例的详细信息，请参阅[连接到 Azure SQL Edge](connect.md)。

## <a name="verify-failure-and-recovery"></a>验证故障和恢复

若要验证故障和恢复，则可以删除 Pod。 请执行以下步骤：

1. 请列出运行 Azure SQL Edge 的 Pod。

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   请记下运行 Azure SQL Edge 的 Pod 的名称。

2. 删除 Pod。

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` 是从上一步返回的 Pod 名称的值。 

Kubernetes 会自动重新创建 Pod 以恢复 Azure SQL Edge 实例，并连接到永久性存储。 使用 `kubectl get pods` 验证是否部署了新的 Pod。 使用 `kubectl get services` 验证新容器的 IP 地址是否相同。 

## <a name="summary"></a>总结

本教程介绍了如何将 Azure SQL Edge 容器部署到 Kubernetes 群集以实现高可用性。 

> [!div class="checklist"]
> * 创建 SA 密码
> * 创建存储
> * 创建部署
> * 使用 Azure SQL Edge Management Studio (SSMS) 进行连接
> * 验证故障和恢复

## <a name="next-steps"></a>后续步骤

- [Kubernetes 简介](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [在 SQL Edge 中将机器学习和人工智能与 ONNX 结合使用](onnx-overview.md)。
- [使用 IoT Edge 通过 SQL Edge 生成端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的数据流式处理](stream-data.md)

