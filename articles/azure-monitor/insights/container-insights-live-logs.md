---
title: 实时查看 Azure Monitor 以获取容器日志 | Microsoft Docs
description: 本文介绍容器日志（stdout/stderr）和事件的实时视图，无需将 kubectl 与 Azure Monitor for 容器一起使用。
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: d947b44177e9aa5777d759286d982e974e378497
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389789"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>如何实时查看日志和事件（预览）
容器 Azure Monitor 包含一项功能，该功能目前处于预览阶段，可提供 Azure Kubernetes 服务（AKS）容器日志（stdout/stderr）和事件的实时视图，而无需运行 kubectl 命令。 选择任一选项时，"**节点**"、"**控制器**" 和 "**容器**" 视图的 "性能" 数据表下方将显示一个新窗格。 它显示了由容器引擎生成的实时日志记录和事件，以进一步帮助排查问题。

>[!NOTE]
>此功能在所有 Azure 区域（包括 Azure 中国）中可用。 它当前在 Azure 美国政府版中不可用。

>[!NOTE]
>要使此功能正常工作， **Azure Kubernetes Service 群集用户角色**需要对群集资源的访问权限。 [了解有关 Azure Kubernetes 群集用户角色的详细信息](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions)。

实时日志支持三种不同的方法来控制对日志的访问：

1. 没有启用 Kubernetes RBAC 授权的 AKS
2. 启用了 Kubernetes RBAC 授权的 AKS
3. AKS 启用了基于 SAML 的 Azure Active Directory （AD）单一登录

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

2. 如果是首次配置它，请运行以下命令来应用群集规则绑定： `kubectl create -f LogReaderRBAC.yaml`。 如果你以前在引入实时事件日志前启用了对实时日志预览的支持，若要更新你的配置，请运行以下命令： `kubectl apply -f LogReaderRBAC.yaml`。

## <a name="configure-aks-with-azure-active-directory"></a>为 AKS 配置 Azure Active Directory

可将 AKS 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 如果是首次配置它，请参阅将[Azure Active Directory 与 Azure Kubernetes 服务集成](../../aks/azure-ad-integration.md)。 在创建[客户端应用程序](../../aks/azure-ad-integration.md#create-the-client-application)的步骤中，指定以下各项：

-  **重定向 URI**：需要创建两个**Web**应用程序类型。 第一个基本 URL 值应为 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，第二个基 URL 值应为 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`。
- 注册应用程序之后，从 "**概述**" 页上，从左侧窗格中选择 "**身份验证**"。 在 "**身份验证**" 页的 "**高级设置**" 下，隐式授予**访问令牌**和**ID 令牌**，并保存所做的更改。

>[!NOTE]
>如果在 Azure 中国区使用此功能，则第一个 "基本 URL" 值应为 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，第二个 "基 URL" 值应为 "`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`"。

>[!NOTE]
>只能在初次部署新的 AKS 群集期间完成为单一登录配置身份 Azure Active Directory 验证。 不能为已部署的 AKS 群集配置单一登录。
  
>[!IMPORTANT]
>如果使用更新的 URI 将 Azure AD 重新配置为进行用户身份验证，请清除浏览器的缓存，以确保下载并应用更新的身份验证令牌。   

## <a name="view-live-logs-and-events"></a>查看实时日志和事件

可以从 "**节点**"、"**控制器**" 和 "**容器**" 视图中的容器引擎生成实时日志事件。 在 "属性" 窗格中，选择 "**查看实时数据（预览）** " 选项，并在性能数据表下面显示一个窗格，可在其中查看连续流中的日志和事件。

![节点属性窗格查看实时日志选项](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

日志和事件消息受限于在视图中选择的资源类型。

| 查看 | 资源类型 | 日志或事件 | 显示的数据 |
|------|---------------|--------------|----------------|
| 节点数 | 节点 | 事件 | 如果选择了节点，则不会筛选事件，并显示群集范围的 Kubernetes 事件。 窗格标题显示群集的名称。 |
| 节点数 | Pod | 事件 | 选择 pod 时，会将事件筛选到其命名空间。 窗格标题显示 pod 的命名空间。 | 
| 控制器 | Pod | 事件 | 选择 pod 时，会将事件筛选到其命名空间。 窗格标题显示 pod 的命名空间。 |
| 控制器 | 控制器 | 事件 | 选择控制器时，会将事件筛选到其命名空间。 窗格标题显示控制器的命名空间。 |
| 节点/控制器/容器 | 容器 | 日志 | 窗格标题显示容器的分组容器的名称。 |

如果使用 AAD 为 AKS 群集配置了 SSO，系统会在该浏览器会话期间向你提示进行首次使用所需的身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

成功进行身份验证以后，实时日志窗格会显示在中间窗格的底部。 如果提取状态指示器显示绿色复选标记（位于窗格最右侧），则意味着可以检索数据。
    
  ![实时日志窗格检索的数据](./media/container-insights-live-logs/live-logs-pane-01.png)  

在搜索栏中，可以按关键词进行筛选以突出显示日志或事件中的文本，并在最右侧的搜索栏中显示与筛选器匹配的结果数。

  ![实时日志窗格筛选器示例](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

查看事件时，还可以使用搜索栏右侧的**筛选器**欣然来限制结果。 根据所选的资源，欣然会列出要从中选择的 pod、命名空间或群集。  

若要暂停自动滚动并控制窗格的行为，并允许您手动滚动浏览新数据，请单击**滚动**选项。 若要重新启用自动滚动，只需再次单击 "**滚动**" 选项。 还可以通过单击 "**暂停**" 选项暂停对日志或事件数据的检索，并在准备好继续时，只需单击 "**播放**"。  

![实时日志窗格的暂停实时视图](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

可以通过在分析的下拉列表**视图中**选择 "**查看容器日志**"，来访问 Azure Monitor 日志以查看历史容器日志。

## <a name="next-steps"></a>后续步骤

- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以便对群集进行警报、可视化或分析。
