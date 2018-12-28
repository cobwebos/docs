---
title: 实时查看 Azure Monitor 以获取容器日志 | Microsoft Docs
description: 本文介绍在不将 kubectl 与适用于容器的 Azure Monitor 配合使用的情况下，如何实时查看容器日志 (stdout/stderr)。
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: da11bb0669bf6bde2c65b2a7a0badaa1ae35abda
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189111"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>如何使用用于容器的 Azure Monitor（预览版）实时查看容器日志
此功能目前为预览版，可以实时查看 Azure Kubernetes 服务 (AKS) 容器日志 (stdout/stderr)，不需运行 kubectl 命令。 选择此选项时，会在“容器”视图的容器性能数据表下显示新的窗格，它会显示由容器引擎生成的实时日志记录，用于进一步以实时方式排查问题。  

实时日志支持以三种不同的方式来控制对日志的访问：

1. 没有启用 Kubernetes RBAC 授权的 AKS 
2. 启用了 Kubernetes RBAC 授权的 AKS
3. 启用了基于 Azure Active Directory (AD) SAML 的单一登录的 AKS 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>未启用 RBAC 的 Kubernetes 群集
 
如果 Kubernetes 群集未配置 Kubernetes RBAC 授权或集成 Azure AD 单一登录，则不需执行这些步骤。 由于 Kubernetes 授权使用 kube-api，因此需要只读权限。

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC 授权
如果已启用 Kubernetes RBAC 授权，则需应用群集角色绑定。 以下示例步骤演示如何从此 yaml 配置模板配置群集角色绑定。   

1. 复制并粘贴 yaml 文件，然后将其另存为 LogReaderRBAC.yaml。  

   ```
   kind: ClusterRole 
   apiVersion: rbac.authorization.k8s.io/v1 
   metadata:   
      name: containerHealth-log-reader 
   rules: 
      - apiGroups: [""]   
        resources: ["pods/log"]   
        verbs: ["get"] 
   --- 
   kind: ClusterRoleBinding 
   apiVersion: rbac.authorization.k8s.io/v1 
   metadata:   
      name: containerHealth-read-logs-global 
   subjects:   
      - kind: User     
        name: clusterUser
        apiGroup: rbac.authorization.k8s.io 
    roleRef:   
       kind: ClusterRole
       name: containerHealth-log-reader
       apiGroup: rbac.authorization.k8s.io 
   ```

2. 运行以下命令以创建群集规则绑定：`kubectl create -f LogReaderRBAC.yaml`。 

## <a name="configure-aks-with-azure-active-directory"></a>为 AKS 配置 Azure Active Directory
可将 AKS 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 如果是第一次进行此项配置，请参阅[将 Azure Active Directory 与 Azure Kubernetes 服务集成](../../aks/aad-integration.md)。 在创建[客户端应用程序](../../aks/aad-integration.md#create-client-application)并指定**重定向 URI** 的过程中，需将另一 URI 添加到列表 ** https://ininprodeusuxbase.microsoft.com/***。  

>[!NOTE]
>通过 Azure Active Directory 配置身份验证以便实现单一登录的操作只能在初次部署新 AKS 群集过程中完成。 不能为已部署的 AKS 群集配置单一登录。  
> 

## <a name="view-live-logs"></a>查看实时日志
查看**容器**时，可以**查看容器日志**或**查看容器实时日志**。  选择“查看容器实时日志”时，会在容器性能数据表下显示新的窗格，并会显示由容器引擎生成的实时日志记录，用于进一步以实时方式排查问题。  
1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在“Microsoft Azure”菜单中选择“监视”，然后选择“容器”。  
3. 在“受监视的容器”视图下的列表中选择一个容器。  
4. 选择“容器”视图，然后会在首选容器的属性面板上列出“查看容器实时日志”链接。  
5. 如果使用 AAD 为 AKS 群集配置了 SSO，系统会在该浏览器会话期间向你提示进行首次使用所需的身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

成功进行身份验证以后，实时日志窗格会显示在中间窗格的底部。 如果提取状态指示器显示绿色复选标记（位于窗格最右侧），则意味着可以检索数据。
    
  ![实时日志窗格检索的数据](./media/container-insights-live-logs/live-logs-pane-01.png)  

在搜索栏中，可以按关键字进行筛选，在日志中突出显示该文本。   

  ![实时日志窗格筛选器示例](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

若要暂停自动滚动并控制窗格的行为，以便通过手动方式滚动浏览读取的新日志数据，请单击“滚动”选项。  若要重新启用自动滚动，请再次直接单击“滚动”选项。  也可暂停对日志数据的检索，只需单击“暂停”选项即可。准备继续时，请直接单击“开始”。  

![实时日志窗格的暂停实时视图](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>后续步骤
若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。