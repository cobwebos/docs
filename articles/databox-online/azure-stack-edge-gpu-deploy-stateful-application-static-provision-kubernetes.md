---
title: 在 Azure Stack Edge Pro 设备上，使用 kubectl 通过静态预配的共享部署 Kubernetes 有状态应用 |Microsoft Docs
description: 介绍如何使用 Azure Stack Edge Pro GPU 设备上的 kubectl 通过静态预配的共享来创建和管理 Kubernetes 有状态应用程序部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/18/2020
ms.author: alkohli
ms.openlocfilehash: 8366c5b7a05b35891bcf87e446229357a5511359
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899534"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>使用 kubectl 在 Azure Stack Edge Pro 设备上使用 PersistentVolume 运行 Kubernetes 有状态应用程序

本文介绍如何使用 PersistentVolume (PV) 和部署在 Kubernetes 中部署单实例有状态应用程序。 部署使用 `kubectl` 现有 Kubernetes 群集上的命令并部署 MySQL 应用程序。 

此过程适用于已 [在 Azure Stack Edge Pro 设备上查看 Kubernetes 存储](azure-stack-edge-gpu-kubernetes-storage.md) 并且熟悉 [Kubernetes 存储](https://kubernetes.io/docs/concepts/storage/)的概念的用户。


## <a name="prerequisites"></a>必备知识

在部署有状态应用程序之前，请确保已在设备和将用于访问设备的客户端上完成以下先决条件：

### <a name="for-device"></a>对于设备

- 你有 Azure Stack Edge Pro 设备的1个节点的登录凭据。
    - 设备已激活。 请参阅 [激活设备](azure-stack-edge-gpu-deploy-activate.md)。
    - 设备通过 Azure 门户配置了计算角色，并具有 Kubernetes 群集。 请参阅 [配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。

### <a name="for-client-accessing-the-device"></a>对于访问设备的客户端

- 你具有将用于访问 Azure Stack Edge Pro 设备的 Windows 客户端系统。
    - 客户端正在运行 Windows PowerShell 5.0 或更高版本。 若要下载最新版本的 Windows PowerShell，请参阅 [安装 Windows powershell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)。
    
    - 您也可以将任何其他客户端与 [支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 结合使用。 本文介绍使用 Windows 客户端的过程。 
    
    - 你已完成在 [Azure Stack Edge Pro 设备上访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)中所述的过程。 你已：
      - `userns1`通过命令创建命名空间 `New-HcsKubernetesNamespace` 。 
      - 通过命令创建了一个用户 `user1` `New-HcsKubernetesUser` 。 
      - 已通过命令授予对的 `user1` 访问权限 `userns1` `Grant-HcsKubernetesNamespaceAccess` 。       
      - 已 `kubectl` 在客户端上安装，并将 `kubeconfig` 具有用户配置的文件保存到 C： \\ Users \\ &lt; &gt; \\ kube。 
    
    - 请确保 `kubectl` 客户端版本不会从 Azure Stack Edge Pro 设备上运行的 Kubernetes 主版本中倾斜多个版本。 
        - 使用 `kubectl version` 检查在客户端上运行的 kubectl 的版本。 记下完整版本。
        - 在 Azure Stack Edge Pro 设备的本地 UI 中，切换到 " **概述** "，并记下 "Kubernetes" 软件号码。 
        - 请验证这两个版本是否与支持的 Kubernetes 版本中提供的映射兼容 <!-- insert link-->. 


你已准备好在 Azure Stack Edge Pro 设备上部署有状态应用程序。 

## <a name="provision-a-static-pv"></a>预配静态 PV

若要以静态方式预配 PV，需要在设备上创建共享。 按照以下步骤为 SMB 共享预配 PV。 

> [!NOTE]
> 本操作指南文章中使用的特定示例不适用于 NFS 共享。 通常情况下，可以在包含非数据库应用程序的 Azure Stack 边缘设备上预配 NFS 共享。

1. 选择是要创建边缘共享还是边缘本地共享。 按照 [添加共享](azure-stack-edge-manage-shares.md#add-a-share) 中的说明创建共享。 请确保选中 " **使用与边缘计算的共享**" 复选框。

    ![适用于 PV 的边缘本地共享](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. 如果决定使用现有共享，则需要安装共享，而不是创建新的共享。
    
        在 Azure Stack Edge 资源的 "Azure 门户中，请参阅" **共享**"。 在现有共享列表中，选择并单击要使用的共享。

        ![为 PV 选择现有本地共享](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. 在出现提示时选择 " **装入** 并确认装载"。  

        ![装载适用于 PV 的现有本地共享](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. 记下共享名。 创建此共享时，会在 Kubernetes 群集中自动创建一个永久性卷对象，该对象对应于创建的 SMB 共享。 

## <a name="deploy-mysql"></a>部署 MySQL

现在，你将通过创建 Kubernetes 部署并将其连接到你在前面步骤中使用 PersistentVolumeClaim (PVC) 创建的 PV 来运行有状态应用程序。 

`kubectl`用于创建和管理有状态应用程序部署的所有命令都需要指定与该配置关联的命名空间。 若要在 kubectl 命令中指定命名空间，请使用 `kubectl <command> -n <your-namespace>` 。

1. 获取命名空间中 Kubernetes 群集上运行的 pod 的列表。 Pod 是在 Kubernetes 群集上运行的应用程序容器或进程。

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   以下是命令使用情况的示例：
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   输出应说明找不到)  (的资源，因为群集上没有运行任何应用程序。

1. 你将使用以下 YAML 文件。 该 `mysql-deployment.yml` 文件描述了运行 MySQL 并引用 PVC 的部署。 文件定义了的卷装载 `/var/lib/mysql` ，然后创建了一个 PVC 来寻找 20 GB 的卷。 

    在前面的步骤中创建共享时，静态预配的任何现有 PV 都满足此声明。 在设备上，将为每个共享创建一个较大的 PV 32 TB。 PV 满足 PVC 规定的要求，并且 PVC 应绑定到此 PV。

    将以下文件复制并保存 `mysql-deployment.yml` 到 Windows 客户端上用于访问 Azure Stack Edge Pro 设备的文件夹中。
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. 将保存为文件，并将其另存为 `mysql-pv.yml` 保存的同一文件夹 `mysql-deployment.yml` 。 若要使用前面使用创建的 SMB 共享 `kubectl` ，请将 `volumeName` PVC 对象中的字段设置为该共享的名称。 

    > [!NOTE] 
    > 请确保 YAML 文件具有正确的缩进。 可以检查 YAML 不 [起毛](http://www.yamllint.com/) 的以验证并保存。
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. 部署 `mysql-pv.yaml` 文件。

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    下面是部署的示例输出。

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   记下创建的 PVC 的名称。 稍后将使用它。 

4. 部署文件的内容 `mysql-deployment.yml` 。

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    下面是部署的示例输出。
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. 显示有关部署的信息。

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. 列出由部署创建的 pod。

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    下面是一个示例输出。

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. 检查 PersistentVolumeClaim。

    `kubectl describe pvc <your-pvc-name>`

    下面是一个示例输出。

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>验证 MySQL 是否正在运行


若要针对运行 MySQL 的 pod 中的容器运行命令，请键入：

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

下面是一个示例输出。

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>删除部署

若要删除部署，请按名称删除已部署的对象。 这些对象包括部署、服务和 PVC。
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

下面是删除部署和服务时的示例输出。

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
下面是删除 PVC 时的示例输出。

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```                                                                                         

在删除 PVC 后，PV 不再绑定到 PVC。 由于在创建共享时预配了 PV，你将需要删除该共享。 执行以下步骤：

1. 卸载共享。 在 Azure 门户中，请 > "共享" 中转到 **Azure Stack Edge 资源** ，并选择并单击要卸载的共享。 选择 " **卸载** "，然后确认操作。 等待共享卸载。 卸载 (会释放共享，因此 Kubernetes 群集中的关联 PersistentVolume) 。 

    ![卸载本地共享以实现 PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. 你现在可以选择 " **删除** 并确认删除" 来删除共享。 这也会删除共享和相应的 PV。

    ![删除用于 PV 的本地共享](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>后续步骤

若要了解如何动态设置存储，请参阅 [在 Azure Stack Edge Pro 设备上通过动态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)
