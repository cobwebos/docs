---
title: 实时查看 Azure Monitor 以获取容器日志 | Microsoft Docs
description: 本文介绍容器日志 (stdout/stderr) 和事件的实时视图，而无需在用于容器与 Azure Monitor 中使用 kubectl。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2019
ms.author: magoedte
ms.openlocfilehash: 376a7f3f83cc7fcf7490675d9c0aef1513862e8a
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521734"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>如何在实时 （预览版） 中查看日志和事件
用于容器的 azure 监视器包含一项功能，目前以预览版提供，而无需运行 kubectl 命令提供到您的 Azure Kubernetes 服务 (AKS) 容器日志 (stdout/stderr) 和事件的实时视图。 选择任一选项，一个新窗格将显示下面的性能数据的表上**节点**，**控制器**，并**容器**视图。 它显示实时日志记录和生成容器引擎，为进一步协助解决在真实时间中的问题的事件。 

>[!NOTE]
>**参与者**需要即可使用此功能的访问权限的群集资源。
>

实时日志支持以三种不同的方式来控制对日志的访问：

1. 没有启用 Kubernetes RBAC 授权的 AKS 
2. 启用了 Kubernetes RBAC 授权的 AKS
3. AKS 上启用与 Azure Active Directory (AD) 基于 SAML 的单一登录 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>未启用 RBAC 的 Kubernetes 群集
 
如果 Kubernetes 群集未配置 Kubernetes RBAC 授权或集成 Azure AD 单一登录，则不需执行这些步骤。 Kubernetes 授权使用 kube-api，因此需要只读权限。

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC 授权
如果已启用 Kubernetes RBAC 授权，则需应用群集角色绑定。 以下示例步骤演示如何从此 yaml 配置模板配置群集角色绑定。 

1. 复制并粘贴 yaml 文件，然后将其另存为 LogReaderRBAC.yaml。  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
        kind: ClusterRole 
        name: containerHealth-log-reader 
        apiGroup: rbac.authorization.k8s.io 
    subjects: 
       - kind: User 
         name: clusterUser 
         apiGroup: rbac.authorization.k8s.io
    ```

2. 如果要第一次配置，则通过运行以下命令创建群集规则绑定： `kubectl create -f LogReaderRBAC.yaml`。 如果您以前启用了支持的实时日志预览之前我们引入了实时事件日志，以更新你的配置，运行以下命令： `kubectl apply -f LiveLogRBAC.yml`。 

## <a name="configure-aks-with-azure-active-directory"></a>为 AKS 配置 Azure Active Directory
可将 AKS 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 如果是第一次进行此项配置，请参阅[将 Azure Active Directory 与 Azure Kubernetes 服务集成](../../aks/azure-ad-integration.md)。 在创建[客户端应用程序](../../aks/azure-ad-integration.md#create-client-application)并指定“重定向 URI”的过程中，需向列表添加另一个 URI `https://ininprodeusuxbase.microsoft.com/*`。  

>[!NOTE]
>通过 Azure Active Directory 配置身份验证以便实现单一登录的操作只能在初次部署新 AKS 群集过程中完成。 不能为已部署的 AKS 群集配置单一登录。 您必须配置从身份验证**应用注册 （旧版）** 为了在 URI 中支持通配符的使用和，而 Azure AD 中的选项将其添加到列表中，将其注册为**本机**应用。
> 

## <a name="view-live-logs-and-events"></a>查看实时日志和事件

生成从容器引擎，可以查看实时日志事件**节点**，**控制器**，并**容器**视图。 从属性窗格中选择**查看实时数据 （预览版）** 选项和一个窗格，从中可以查看日志和事件持续流中的性能数据表格如下所示。 

![节点属性窗格中查看实时日志选项](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

根据在视图中选择哪种资源类型，日志和事件消息来限制。

| 查看 | 资源类型 | 日志或事件 | 显示数据 |
|------|---------------|--------------|----------------|
| Nodes | 节点 | 事件 | 选择节点时事件未筛选和显示群集范围内的 Kubernetes 事件。 窗格标题中都会显示群集的名称。 |
| Nodes | Pod | 事件 | 选择一个 pod 时事件都筛选到其命名空间。 窗格标题中都会显示 pod 的命名空间。 | 
| 控制器 | Pod | 事件 | 选择一个 pod 时事件都筛选到其命名空间。 窗格标题中都会显示 pod 的命名空间。 |
| 控制器 | 控制器 | 事件 | 选择一个控制器时事件都筛选到其命名空间。 窗格标题中都会显示控制器的命名空间。 |
| 节点/控制器/容器 | 容器 | 日志 | 窗格标题中都会显示容器的 pod 的名称进行分组所依据。 |

如果使用 AAD 为 AKS 群集配置了 SSO，系统会在该浏览器会话期间向你提示进行首次使用所需的身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

成功进行身份验证以后，实时日志窗格会显示在中间窗格的底部。 如果提取状态指示器显示绿色复选标记（位于窗格最右侧），则意味着可以检索数据。
    
  ![实时日志窗格检索的数据](./media/container-insights-live-logs/live-logs-pane-01.png)  

在搜索栏中，您可以按关键字以突出显示该文本在日志或事件，并在最右侧的搜索栏中，它会显示出该筛选器匹配的结果数。   

  ![实时日志窗格筛选器示例](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

若要挂起自动滚动和控制窗格的行为并且使您能够读取新数据中手动滚动，单击**滚动**选项。 若要重新启用自动滚动，只需单击**滚动**试选项。 此外可以通过单击暂停日志或事件数据的检索**暂停**选项，并准备好继续时，只需单击**播放**。  

![实时日志窗格的暂停实时视图](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

你可以转到 Azure Monitor 日志，可以通过选择查看历史容器的日志**查看容器日志**从下拉列表**视图中分析**。

## <a name="next-steps"></a>后续步骤
- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。
- 视图[记录查询示例](container-insights-log-search.md#search-logs-to-analyze-data)若要查看预定义的查询和示例，以评估或自定义的警报、 可视化，或分析你的群集。
