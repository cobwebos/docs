---
title: Azure Stack Edge Pro GPU 设备上的 Kubernetes 上启用 Azure Arc |Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro GPU 设备上的现有 Kubernetes 群集上启用 Azure Arc。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/01/2020
ms.author: alkohli
ms.openlocfilehash: c38b0b1d3a2e71502ac86bf46771ecfb637ba15d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952210"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上的 Kubernetes 群集上启用 Azure Arc

本文介绍如何在 Azure Stack Edge Pro 设备上的现有 Kubernetes 群集上启用 Azure Arc。 

此过程适用于已 [在 Azure Stack Edge Pro 设备上查看了 Kubernetes 工作负荷](azure-stack-edge-gpu-kubernetes-workload-management.md) 的用户，并且熟悉 [Azure Arc 启用 Kubernetes (Preview) ？](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)的概念。


## <a name="prerequisites"></a>先决条件

在 Kubernetes 群集上启用 Azure Arc 之前，请确保已在 Azure Stack Edge Pro 设备和将用于访问设备的客户端上完成以下先决条件：

### <a name="for-device"></a>对于设备

1. 你有 Azure Stack Edge Pro 设备的1个节点的登录凭据。
    1. 设备已激活。 请参阅 [激活设备](azure-stack-edge-gpu-deploy-activate.md)。
    1. 设备通过 Azure 门户配置了计算角色，并具有 Kubernetes 群集。 请参阅 [配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。

1. 你已拥有订阅的所有者访问权限。 你需要在你的服务主体的角色分配步骤中执行此访问。
 

### <a name="for-client-accessing-the-device"></a>对于访问设备的客户端

1. 你具有将用于访问 Azure Stack Edge Pro 设备的 Windows 客户端系统。
  
    - 客户端正在运行 Windows PowerShell 5.0 或更高版本。 若要下载最新版本的 Windows PowerShell，请参阅 [安装 Windows powershell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)。
    
    - 您也可以将任何其他客户端与 [支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 结合使用。 本文介绍使用 Windows 客户端的过程。 
    
1. 你已完成在 [Azure Stack Edge Pro 设备上访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)中所述的过程。 你已：
    
    - 安装 `kubectl` 在客户端上  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - 请确保 `kubectl` 客户端版本不会从 Azure Stack Edge Pro 设备上运行的 Kubernetes 主版本中倾斜多个版本。 
      - 使用 `kubectl version` 检查在客户端上运行的 kubectl 的版本。 记下完整版本。
      - 在 Azure Stack Edge Pro 设备的本地 UI 中，切换到 " **软件更新** "，并记下 Kubernetes 服务器版本号。 
    
        ![验证 Kubernetes 服务器版本号](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - 请验证这两个版本是否兼容。 

<!-- az cli version requirements-->

## <a name="register-kubernetes-resource-providers"></a>注册 Kubernetes 资源提供程序

在 Kubernetes 群集上启用 Azure Arc 之前，你将需要启用并注册 `Microsoft.Kubernetes` `Microsoft.KubernetesConfiguration` 订阅。 

1. 若要启用资源提供程序，请在 Azure 门户中，访问计划用于部署的订阅。 请访问 **资源提供程序**。 
1. 在右侧窗格中，搜索要添加的提供程序。 在此示例中， `Microsoft.Kubernetes` 和 `Microsoft.KubernetesConfiguration` 。

    ![注册 Kubernetes 资源提供程序](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. 选择资源提供程序，然后在命令栏顶部选择 " **注册**"。 注册花费几分钟时间。 

    ![注册 Kubernetes 资源提供程序2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. 刷新 UI，直到你看到资源提供程序已注册。 对两个资源提供程序重复此过程。
    
    ![注册 Kubernetes 资源提供程序3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

你还可以通过注册资源提供程序 `az cli` 。 有关详细信息，请参阅为 [启用了 Azure Arc 的两个提供程序 Kubernetes](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>创建服务主体，分配角色

1. 请确保你具有 `Subscription ID` 用于 Azure Stack Edge 服务的资源部署的资源组的名称。 若要获取订阅 ID，请转到 Azure 门户中的 Azure Stack Edge 资源。 导航到 **概述 > Essentials**。

    ![获取订阅 ID](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    若要获取资源组名称，请转到 " **属性**"。

    ![获取资源组名称](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. 若要创建服务主体，请通过使用以下命令 `az cli` 。

    `az ad sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    有关如何登录到的信息 `az cli` ，请 [在 Azure 门户中开始 Cloud Shell](../cloud-shell/quickstart-powershell.md?view=azure-cli-latest#start-cloud-shell)

    示例如下。 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. 记下 `appID` 、、和， `name` `password` `tenantID` 因为你将在下一命令中将其用作输入。

1. 创建新的服务主体后，将 `Kubernetes Cluster - Azure Arc Onboarding` 角色分配给新创建的主体。 这是一个内置的 Azure 角色 (在命令) 中使用具有有限权限的角色 ID。 使用以下命令：

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    示例如下。
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    有关如何创建服务主体和执行角色分配的详细信息，请参阅 [创建支持 Azure Arc 的载入服务主体](https://docs.microsoft.com/azure/azure-arc/kubernetes/create-onboarding-service-principal)中的步骤。


## <a name="enable-arc-on-kubernetes-cluster"></a>在 Kubernetes 群集上启用 Arc

按照以下步骤配置 Azure Arc 管理的 Kubernetes 群集：

1. [连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 。

1. 类型：

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`

    若要在 Azure Stack Edge Pro 设备上部署 Azure Arc，请确保使用 [受支持的 Azure arc 区域](../azure-arc/kubernetes/overview.md#supported-regions)。Azure Arc 目前为预览版。 你还可以使用命令来确定要在 cmdlet 中传递的区域的确切名称 `az account list-locations` 。
    
    以下是示例：
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    在 Azure 门户中，应使用在前面的命令中提供的名称创建资源。

    ![中转到 Azure Arc 资源](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. 若要验证 Azure Arc 是否已成功启用，请从 PowerShell 界面运行以下命令：

    `kubectl get deployments -n azure-arc`

    此命令将查找部署在 `azure-arc` 与 Azure Arc 对应的命名空间中的所有应用程序。

    下面是一个示例输出，显示了在命名空间中的 Kubernetes 群集上部署的 Azure Arc 代理 `azure-arc` 。 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    还可以在命名空间中获取 Kubernetes 群集上运行的盒的列表 `azure-arc` 。 Pod 是在 Kubernetes 群集上运行的应用程序容器或进程。 

    使用以下命令：
    
    `kubectl get pods -n azure-arc`
    
    下面是一个示例输出。
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


如前面的输出所示，启用了 Azure Arc 的 Kubernetes 包括在部署到命名空间的群集中运行的几个 (操作员) 代理 `azure-arc` 。

- `config-agent`：监视群集上应用的源代码管理配置资源的已连接群集并更新符合性状态
- `controller-manager`：是运算符的运算符，用于协调 Azure Arc 组件之间的交互
- `metrics-agent`：收集其他 Arc 代理的指标，以确保这些代理表现出最佳性能
- `cluster-metadata-operator`：收集分类元数据-群集版本、节点计数和 Azure Arc 代理版本
- `resource-sync-agent`：将上面提到的群集元数据同步到 Azure
- `clusteridentityoperator`：启用了 Azure Arc 的 Kubernetes 目前支持系统分配的标识。 clusteridentityoperator 维护其他代理用于与 Azure 进行通信的托管服务标识 (MSI) 证书。
- `flux-logs-agent`：从部署为源代码管理配置一部分的 flux 操作员收集日志。
- `connect-agent`：与 Azure Arc 资源通信。

### <a name="remove-arc-from-the-kubernetes-cluster"></a>从 Kubernetes 群集中删除 Arc

若要删除 Azure Arc 管理，请执行以下步骤：

1. 1. [连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 。
2. 类型：

    `Remove-HcsKubernetesAzureArcAgent` 


## <a name="next-steps"></a>后续步骤

若要了解如何运行 Azure Arc 部署，请参阅 [在 Azure Stack Edge Pro 设备上通过 GitOps 部署无状态 PHP 留言簿应用程序](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)
