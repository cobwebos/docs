---
title: Azure Stack Edge Pro GPU 设备上的启用 Arc 的 Kubernetes 上部署 PHP 留言簿应用 |Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro 设备的启用了 Arc 的 Kubernetes 群集上，使用 GitOps 来部署 PHP 留言簿无状态应用程序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 3200cfe290cbba208c61e914b17ffa6cd65e6eee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899564"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>在 Azure Stack Edge Pro GPU 上启用启用了 Arc 的 Kubernetes 群集的 PHP 留言簿无状态应用程序

本文说明如何使用 Kubernetes 和 Azure Arc 构建和部署简单的多层 web 应用程序。此示例包含以下组件：

- 用于存储留言簿项的单实例 Redis 主机
- 用于为读取提供服务的多个复制 Redis 实例
- 多个 web 前端实例

部署是使用 Azure Stack Edge Pro 设备上启用了 Arc 的 Kubernetes 群集上的 GitOps 来完成的。 

此过程适用于已 [在 Azure Stack Edge Pro 设备上查看 Kubernetes 工作负荷](azure-stack-edge-gpu-kubernetes-workload-management.md) 的用户，并且熟悉 [Azure Arc 启用 Kubernetes (Preview) ](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)的概念。


## <a name="prerequisites"></a>必备条件

在部署无状态应用程序之前，请确保已在设备和将用于访问该设备的客户端上完成以下先决条件：

### <a name="for-device"></a>对于设备

1. 你有 Azure Stack Edge Pro 设备的1个节点的登录凭据。
    1. 设备已激活。 请参阅 [激活设备](azure-stack-edge-gpu-deploy-activate.md)。
    1. 设备通过 Azure 门户配置了计算角色，并具有 Kubernetes 群集。 请参阅 [配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。

1. 已在设备上的现有 Kubernetes 群集上启用了 Azure Arc，并在 Azure 门户中具有相应的 Azure Arc 资源。 有关详细步骤，请参阅 [在 Azure Stack Edge Pro 设备上启用 Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。

### <a name="for-client-accessing-the-device"></a>对于访问设备的客户端

1. 你具有将用于访问 Azure Stack Edge Pro 设备的 Windows 客户端系统。
  
    - 客户端正在运行 Windows PowerShell 5.0 或更高版本。 若要下载最新版本的 Windows PowerShell，请参阅 [安装 Windows powershell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)。
    
    - 您也可以将任何其他客户端与 [支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 结合使用。 本文介绍使用 Windows 客户端的过程。 
    
1. 你已完成在 [Azure Stack Edge Pro 设备上访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)中所述的过程。 你已：
    
    - 安装 `kubectl` 在客户端上  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - 请确保 `kubectl` 客户端版本不会从 Azure Stack Edge Pro 设备上运行的 Kubernetes 主版本中倾斜多个版本。 
      - 使用 `kubectl version` 检查在客户端上运行的 kubectl 的版本。 记下完整版本。
      - 在 Azure Stack Edge Pro 设备的本地 UI 中，切换到 " **概述** "，并记下 "Kubernetes" 软件号码。 
      - 请验证这两个版本是否与支持的 Kubernetes 版本中提供的映射兼容 <!--insert link-->.

1. 有一个 [可用于运行 Azure Arc 部署的 GitOps 配置](https://github.com/kagoyal/dbehaikudemo)。 在此示例中，你将使用以下 `yaml` 文件在 Azure Stack Edge Pro 设备上进行部署。

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>部署配置

按照以下步骤配置 Azure Arc 资源，以便通过 Azure 门户部署 GitOps 配置： 

1. 在 Azure 门户中，请切换到在设备上的 Kubernetes 群集上启用 Azure Arc 时创建的 Azure Arc 资源。 

    ![中转到 Azure Arc 资源](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. 请参阅 " **配置** " 并选择 " **+ 添加配置**"。

    ![中转到配置](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. 在 " **添加配置**" 中，为字段输入适当的值，并选择 " **应用**"。

    |参数  |说明 |
    |---------|---------|
    |配置名称     | 配置资源的名称。        |
    |操作员实例名称     |用于标识特定配置的操作员的实例名称。 Name 是长度为253个字符的字符串，它必须仅为小写、字母数字、连字符和句点。         |
    |Operator 命名空间     | 设置为 **demotestguestbook** ，因为它与部署中指定的命名空间匹配 `yaml` 。 <br> 字段定义安装操作员的命名空间。 Name 是长度为253个字符的字符串，它必须仅为小写、字母数字、连字符和句点。         |
    |存储库 URL     |<br>Git 存储库的路径， `http://github.com/username/repo` 或 `git://github.com/username/repo` 格式 GitOps 配置所在的位置。         |
    |运算符范围     | 选择 **命名空间**。 <br>这会定义操作员的安装范围。 选择此作为命名空间。 操作员将安装在部署 yaml 文件中指定的命名空间中。       |
    |运算符类型     | 保留默认值。 <br>默认情况下，此值指定操作员的类型设置为 flux。        |
    |运算符 params     | 将其留空。 <br>此字段包含要传递给 flux 运算符的参数。        |
    |Helm     | 将此设置为 " **已禁用**"。 <br>如果要执行基于图表的部署，请启用此选项。        |


    ![添加配置](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. 配置部署开始， **操作员状态** 显示为 " **挂起**"。 

    ![中转到配置](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. 部署需花费几分钟时间。 部署完成后， **操作员状态** 显示为 " **已安装**"。

    ![中转到配置](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)


## <a name="verify-deployment"></a>验证部署

通过 GitOps 配置进行的部署将创建 `demotestguestbook` 位于 git 存储库中的部署文件中指定的命名空间 `yaml` 。

1. 应用 GitOps 配置后， [连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
1. 运行以下命令以列出与 `demotestguestbook` 部署相对应的命名空间中运行的 pod。

    `kubectl get pods -n <your-namespace>`
  
    下面是一个示例输出。
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. 在此示例中，前端服务部署为类型： LoadBalancer。 你将需要查找此服务的 IP 地址以查看留言簿。 运行以下命令。

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. 的前端服务 `type:LoadBalancer` 具有外部 IP 地址。 此 IP 来自在设备上配置计算网络设置时为外部服务指定的 IP 地址范围。 使用此 IP 地址查看 URL 上的留言簿： `https://<external-IP-address>` 。

    ![查看留言簿](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>删除部署

若要删除部署，可以从 Azure 门户中删除配置。 这会删除创建的对象，包括部署和服务。

1. 在 Azure 门户中，请参阅 Azure Arc 资源 > 配置。 
1. 找到要删除的配置。 选择 .。。调用上下文菜单并选择 " **删除**"。
    ![删除配置](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

删除配置可能需要几分钟时间。
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>后续步骤

了解如何 [使用 Kubernetes 仪表板监视 Azure Stack Edge Pro 设备上的部署](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)
