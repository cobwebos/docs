---
title: 使用 kubectl 在 Azure Stack Edge 设备上通过动态预配的共享来部署 Kubernetes 有状态应用 |Microsoft Docs
description: 介绍如何在 Microsoft Azure Stack 边缘设备上使用 kubectl 通过动态预配的共享来创建和管理 Kubernetes 有状态应用程序部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: e83b2b1cb7013b9d72bd2c2de8c7636d5c9d8773
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083198"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-device"></a>使用 kubectl 在 Azure Stack Edge 设备上使用 StorageClass 运行 Kubernetes 有状态应用程序

本文介绍如何在 Kubernetes 中使用 StorageClass 动态设置存储和部署来部署单实例有状态应用程序。 部署使用 `kubectl` 现有 Kubernetes 群集上的命令并部署 MySQL 应用程序。 

此过程适用于已 [在 Azure Stack Edge 设备上查看 Kubernetes 存储](azure-stack-edge-gpu-kubernetes-storage.md) 并且熟悉 [Kubernetes 存储](https://kubernetes.io/docs/concepts/storage/)的概念的用户。


## <a name="prerequisites"></a>先决条件

在部署有状态应用程序之前，请确保已在设备和将用于访问设备的客户端上完成以下先决条件：

### <a name="for-device"></a>对于设备

- 你有 Azure Stack Edge 设备的1节点的登录凭据。
    - 设备已激活。 请参阅 [激活设备](azure-stack-edge-gpu-deploy-activate.md)。
    - 设备通过 Azure 门户配置了计算角色，并具有 Kubernetes 群集。 请参阅 [配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。

### <a name="for-client-accessing-the-device"></a>对于访问设备的客户端

- 你具有将用于访问 Azure Stack Edge 设备的 Windows 客户端系统。
    - 客户端正在运行 Windows PowerShell 5.0 或更高版本。 若要下载最新版本的 Windows PowerShell，请参阅 [安装 Windows powershell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)。
    
    - 您也可以将任何其他客户端与 [支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 结合使用。 本文介绍使用 Windows 客户端的过程。 
    
    - 你已完成在 [Azure Stack Edge 设备上访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)中所述的过程。 你已：
      - `userns1`通过命令创建命名空间 `New-HcsKubernetesNamespace` 。 
      - 通过命令创建了一个用户 `user1` `New-HcsKubernetesUser` 。 
      - 已通过命令授予对的 `user1` 访问权限 `userns1` `Grant-HcsKubernetesNamespaceAccess` 。       
      - 已 `kubectl` 在客户端上安装，并将 `kubeconfig` 具有用户配置的文件保存到 C： \\ Users \\ &lt; &gt; \\ kube。 
    
    - 请确保 `kubectl` 客户端版本不会从 Azure Stack Edge 设备上运行的 Kubernetes 主版本中倾斜多个版本。 
        - 使用 `kubectl version` 检查在客户端上运行的 kubectl 的版本。 记下完整版本。
        - 在 Azure Stack Edge 设备的本地 UI 中，请参阅 " **概述** " 并记下 "Kubernetes" 软件号码。 
        - 请验证这两个版本是否与支持的 Kubernetes 版本中提供的映射兼容<!-- insert link-->. 


你已准备好在 Azure Stack Edge 设备上部署有状态应用程序。 


## <a name="deploy-mysql"></a>部署 MySQL

现在，你将通过创建 Kubernetes 部署并使用 PersistentVolumeClaim (PVC) 将其连接到内置 StorageClass 来运行有状态的应用程序。 

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

1. 你将使用以下 YAML 文件。 该 `mysql-deployment.yml` 文件描述了运行 MySQL 并引用 PVC 的部署。 文件定义了的卷装载 `/var/lib/mysql` ，然后创建了一个 PVC 来寻找 20 GB 的卷。 设置了动态 PV，并将 PVC 绑定到了此 PV。

    将以下文件复制并保存 `mysql-deployment.yml` 到用于访问 Azure Stack Edge 设备的 Windows 客户端上的文件夹中。
    
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
              claimName: mysql-pv-claim-sc
    ```
    
2. 将保存为文件，并将其另存为 `mysql-pvc.yml` 保存的同一文件夹 `mysql-deployment.yml` 。 若要在附加的数据磁盘上使用 Azure Stack Edge 设备的内置 StorageClass，请将 `storageClassName` PVC 对象中的字段设置为 `ase-node-local` ，并将 accessModes 设置为 `ReadWriteOnce` 。 

    > [!NOTE] 
    > 请确保 YAML 文件具有正确的缩进。 可以检查 YAML 不 [起毛](http://www.yamllint.com/) 的以验证并保存。
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. 部署 `mysql-pvc.yaml` 文件。

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    下面是部署的示例输出。

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   请注意创建的 PVC 的名称 `mysql-pv-claim-sc` 。 稍后将使用它。 

4. 部署文件的内容 `mysql-deployment.yml` 。

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    下面是部署的示例输出。
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. 显示有关部署的信息。

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
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
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. 列出由部署创建的 pod。

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    下面是一个示例输出。

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. 检查 PersistentVolumeClaim。

    `kubectl describe pvc <your-pvc-name>`

    下面是一个示例输出。

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>验证 MySQL 是否正在运行

若要验证应用程序是否正在运行，请键入：

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

提示时提供密码。 密码在 `mysql-deployment` 文件中。

下面是一个示例输出。

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
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
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>后续步骤

若要了解如何通过 kubectl 配置网络，请参阅 [在 Azure Stack Edge 设备上部署无状态应用程序](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)
