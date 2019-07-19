---
title: 实时查看 Azure Monitor 以获取容器日志 | Microsoft Docs
description: 本文介绍在不将 kubectl 与适用于容器的 Azure Monitor 配合使用的情况下，如何实时查看容器日志 (stdout/stderr) 和事件。
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
ms.openlocfilehash: 968ee4c8bb5d7e09ef3c345c46f6c7b839e0e25a
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990039"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>如何实时查看日志和事件（预览）
适用于容器的 Azure Monitor 包含一项功能，该功能目前为预览版，可以实时查看 Azure Kubernetes 服务 (AKS) 容器日志 (stdout/stderr) 和事件，不需运行 kubectl 命令。 选择任一选项后，“节点”、“控制器”和“容器”视图上的性能数据表下方将显示新窗格    。 它显示了容器引擎生成的实时日志记录和事件，可以进一步实时排查问题。

>[!NOTE]
>若要使用此功能，用户必须有群集资源的“参与者”访问权限。 
>

实时日志支持以三种不同的方式来控制对日志的访问：

1. 没有启用 Kubernetes RBAC 授权的 AKS
2. 启用了 Kubernetes RBAC 授权的 AKS
3. 启用了基于 SAML 的 Azure Active Directory (AD) 单一登录的 AKS

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

2. 如果是首次配置它, 请运行以下命令来应用群集规则绑定: `kubectl create -f LogReaderRBAC.yaml`。 如果在我们引入实时事件日志之前，你已经启用了对实时日志预览版的支持，则请运行以下命令来更新配置：`kubectl apply -f LogReaderRBAC.yaml`。

## <a name="configure-aks-with-azure-active-directory"></a>为 AKS 配置 Azure Active Directory

可将 AKS 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 如果是第一次配置它，请参阅[将 Azure Active Directory 与 Azure Kubernetes 服务集成](../../aks/azure-ad-integration.md)。 在创建[客户端应用程序](../../aks/azure-ad-integration.md#create-the-client-application)的步骤中，请指定以下内容：

- **重定向 URI (可选)** ：这是一个 **Web** 应用程序类型，基 URL 值应该为 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`。
- 注册应用程序以后，请在“概览”页的左窗格中选择“身份验证”   。 在“身份验证”页的“高级设置”下，隐式授予“访问令牌”和“ID 令牌”，然后保存所做的更改。    

>[!NOTE]
>通过 Azure Active Directory 配置身份验证以便实现单一登录的操作只能在初次部署新 AKS 群集过程中完成。 不能为已部署的 AKS 群集配置单一登录。
  
>[!IMPORTANT]
>如果使用更新的 URI 重新配置了用于用户身份验证的 Azure AD，请清除浏览器的缓存，确保更新的身份验证令牌已下载并应用。   

## <a name="view-live-logs-and-events"></a>查看实时日志和事件

可以在“节点”、“控制器”和“容器”视图中查看容器引擎生成的实时日志事件。    从属性窗格中选择“查看实时数据(预览)”选项，  此时一个窗格会呈现在性能数据表下面，你可以在其中查看持续流中的日志和事件。

![节点属性窗格的“查看实时日志”选项](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

日志和事件消息数存在限制，具体取决于在视图中选择的资源类型。

| 查看 | 资源类型 | 日志或事件 | 显示的数据 |
|------|---------------|--------------|----------------|
| Nodes | 节点 | Event | 选择节点时，不会对事件进行筛选，会显示群集范围的 Kubernetes 事件。 窗格标题会显示群集的名称。 |
| Nodes | Pod | Event | 选择 Pod 时，会根据其命名空间对事件进行筛选。 窗格标题会显示 Pod 的命名空间。 | 
| 控制器 | Pod | Event | 选择 Pod 时，会根据其命名空间对事件进行筛选。 窗格标题会显示 Pod 的命名空间。 |
| 控制器 | 控制器 | Event | 选择控制器时，会根据其命名空间对事件进行筛选。 窗格标题会显示控制器的命名空间。 |
| 节点/控制器/容器 | 容器 | 日志 | 窗格标题会显示进行容器分组时所使用的 Pod 的名称。 |

如果使用 AAD 为 AKS 群集配置了 SSO，系统会在该浏览器会话期间向你提示进行首次使用所需的身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

成功进行身份验证以后，实时日志窗格会显示在中间窗格的底部。 如果提取状态指示器显示绿色复选标记（位于窗格最右侧），则意味着可以检索数据。
    
  ![实时日志窗格检索的数据](./media/container-insights-live-logs/live-logs-pane-01.png)  

在搜索栏中，可以按关键字进行筛选，在日志或事件中突出显示该文本。在最右侧的搜索栏中，显示了有多少结果与筛选器匹配

  ![实时日志窗格筛选器示例](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

在查看事件时，可以进一步对结果进行限制，只需使用在搜索栏右侧发现的“筛选器”  药丸图标即可。 根据所选资源，药丸图标会列出可供选择的 Pod、命名空间或群集。  

若要暂停自动滚动并控制窗格的行为，以便通过手动方式滚动浏览读取的新数据，请单击“滚动”选项。  若要重新启用自动滚动，请再次直接单击“滚动”选项。  也可暂停对日志或事件数据的检索，只需单击“暂停”选项即可。准备继续时，请直接单击“开始”。    

![实时日志窗格的暂停实时视图](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

可以转到 Azure Monitor 日志来查看容器历史日志，只需从下拉列表“在 Analytics 中查看”中选择“查看容器日志”即可。  

## <a name="next-steps"></a>后续步骤

- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。

- 请参阅[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看预定义的查询，以及用于发警报、可视化或分析群集的评估或自定义示例。
