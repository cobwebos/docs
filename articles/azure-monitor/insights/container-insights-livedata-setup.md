---
title: 为容器设置 Azure 监视器 实时数据（预览） |微软文档
description: 本文介绍如何在不使用容器的 Azure 监视器使用 kubectl 的情况下设置容器日志（stdout/stderr）和事件的实时视图。
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275368"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>如何设置实时数据（预览）功能

要使用 Azure 监视器查看 Azure 库伯内斯服务 （AKS） 群集中的容器的实时数据（预览），需要配置身份验证以授予访问库贝内斯数据的权限。 此安全配置允许直接通过 Azure 门户中的 Kubernetes API 实时访问数据。

此功能支持以下方法来控制对日志、事件和指标的访问：

- 没有启用 Kubernetes RBAC 授权的 AKS
- 启用了 Kubernetes RBAC 授权的 AKS
    - AKS 配置群集角色绑定**[群集监视用户](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- 启用了基于 SAML 的 Azure Active Directory (AD) 单一登录的 AKS

这些说明需要对 Kubernetes 群集进行管理访问，如果配置为使用 Azure 活动目录 （AD） 进行用户身份验证，则对 Azure AD 进行管理访问。  

本文介绍如何配置身份验证以控制对群集中实时数据（预览）功能的访问：

- 启用基于角色的访问控制 （RBAC） 支持 AKS 群集
- Azure 活动目录集成 AKS 群集。 

>[!NOTE]
>此功能不支持作为[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)启用的 AKS 群集。 此功能依赖于通过浏览器通过代理服务器直接访问 Kubernetes API。 启用网络安全性来阻止 Kubernetes API 来自此代理将阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 区域均可用，包括Azure 中国。 它目前在 Azure 美国政府版中不可用。

## <a name="authentication-model"></a>身份验证模型

实时数据（预览）功能使用 Kubernetes API，与`kubectl`命令行工具相同。 Kubernetes API 终结点使用自签名证书，您的浏览器将无法验证该证书。 此功能利用内部代理使用 AKS 服务验证证书，确保流量受信任。

Azure 门户会提示您验证 Azure 活动目录群集的登录凭据，并在群集创建期间（并在本文中重新配置）将您重定向到客户端注册设置。 此行为与 所需的`kubectl`身份验证过程类似。 

>[!NOTE]
>群集的授权由 Kubernetes 及其配置的安全模型管理。 访问此功能的用户需要权限才能下载库贝内斯配置 *（kubeconfig），* 类似于运行`az aks get-credentials -n {your cluster name} -g {your resource group}`。 此配置文件包含**Azure Kubernetes 服务群集用户角色**的授权和身份验证令牌，在启用 Azure RBAC 和 AKS 群集的情况下，未启用 RBAC 授权。 当使用基于 Azure 活动目录 （AD） SAML 的单号登录启用 AKS 时，它包含有关 Azure AD 和客户端注册详细信息的信息。

>[!IMPORTANT]
>此功能的用户需要[Azure Kubernetes 群集用户角色](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions)到群集才能下载`kubeconfig`并使用此功能。 用户**不需要**参与者访问群集才能利用此功能。 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>将群集监视用户与启用 RBAC 的群集一起使用

为了消除需要应用其他配置更改，以允许 Kubernetes 用户角色绑定**群集用户**访问实时数据（预览）功能后启用[RBAC](#configure-kubernetes-rbac-authorization)授权， AKS 添加了一个新的 Kubernetes 群集角色绑定称为**群集监视用户**。 此群集角色绑定具有访问 Kubernetes API 的所有必要权限，以及使用实时数据（预览）功能的终结点。

为了使用此新用户使用实时数据（预览）功能，您需要成为 AKS 群集资源上的["参与者"](../../role-based-access-control/built-in-roles.md#contributor)角色的成员。 默认情况下，容器的 Azure 监视器配置为使用此用户进行身份验证。 如果群集上不存在群集监视用户角色绑定，则群集**User**将改为用于身份验证。

AKS 在 2020 年 1 月发布了此新角色绑定，因此在 2020 年 1 月之前创建的群集没有该绑定。 如果群集是在 2020 年 1 月之前创建的，则可以通过在群集上执行 PUT 操作或在群集 tha 上执行任何其他操作（如更新群集版本）将新**群集监视用户**添加到现有群集。

## <a name="kubernetes-cluster-without-rbac-enabled"></a>未启用 RBAC 的 Kubernetes 群集

如果 Kubernetes 群集未配置 Kubernetes RBAC 授权或集成 Azure AD 单一登录，则不需执行这些步骤。 这是因为默认情况下，在非 RBAC 配置中，您具有管理权限。

## <a name="configure-kubernetes-rbac-authorization"></a>配置库伯内斯 RBAC 授权

启用 Kubernetes RBAC 授权时，将使用两个用户：**群集用户**和**群集管理员**来访问库伯奈斯 API。 这类似于在没有管理选项`az aks get-credentials -n {cluster_name} -g {rg_name}`的情况下运行。 这意味着**群集用户**需要被授予对 Kubernetes API 中的端点的访问权限。

以下示例步骤演示如何从此 yaml 配置模板配置群集角色绑定。

1. 复制并粘贴 yaml 文件，然后将其另存为 LogReaderRBAC.yaml。  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
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

2. 要更新配置，请运行以下命令： `kubectl apply -f LogReaderRBAC.yaml`。

>[!NOTE] 
> 如果将`LogReaderRBAC.yaml`文件的早期版本应用于群集，请通过复制和粘贴上述步骤 1 中显示的新代码来更新它，然后运行步骤 2 中显示的命令，将其应用于群集。

## <a name="configure-ad-integrated-authentication"></a>配置广告集成身份验证 

配置为使用 Azure 活动目录 （AD） 进行用户身份验证的 AKS 群集利用访问此功能的人员的登录凭据。 在此配置中，你可以使用自己的 Azure AD 身份验证令牌登录到 AKS 群集。

必须重新配置 Azure AD 客户端注册，以允许 Azure 门户将授权页重定向为受信任的重定向 URL。 然后，Azure AD 的用户通过**群集角色**和**群集角色绑定**直接授予对同一库伯奈斯 API 终结点的访问权限。 

有关库伯内斯高级安全设置的详细信息，请查看[库伯内斯文档](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)。 

>[!NOTE]
>如果要创建新的启用 RBAC 的群集，请参阅[将 Azure 活动目录与 Azure 库伯奈斯服务集成](../../aks/azure-ad-integration.md)，然后按照步骤配置 Azure AD 身份验证。 在创建客户端应用程序的步骤中，该部分中的注释突出显示了为 Azure 监视器为与下面的步骤 3 中指定的容器创建的两个重定向 URL。

### <a name="client-registration-reconfiguration"></a>客户端注册重新配置

1. 在**Azure 活动目录> Azure 门户中的应用注册**下，在 Azure AD 中查找 Kubernetes 群集的客户端注册。

2. 从左侧窗格中选择**身份验证**。 

3. 将两个重定向 URL 添加到此列表作为**Web**应用程序类型。 第一个基 URL 值应为 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，第二个基 URL 值应为 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`。

    >[!NOTE]
    >如果在 Azure 中国中使用此功能，则第一个基本 URL 值应为`https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，第二个基本 URL 值`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`应为 。 
    
4. 注册重定向 URL 后，在 **"隐式授予"** 下，选择 **"访问令牌**"和 **"ID 令牌"** 选项，然后保存更改。

>[!NOTE]
>通过 Azure Active Directory 配置身份验证以便实现单一登录的操作只能在初次部署新 AKS 群集过程中完成。 不能为已部署的 AKS 群集配置单一登录。
  
>[!IMPORTANT]
>如果使用更新的 URI 重新配置了用于用户身份验证的 Azure AD，请清除浏览器的缓存，确保更新的身份验证令牌已下载并应用。

## <a name="grant-permission"></a>授予权限

每个 Azure AD 帐户都必须被授予 Kubernetes 中相应 API 的权限，才能访问实时数据（预览）功能。 授予 Azure 活动目录帐户的步骤与[Kubernetes RBAC 身份验证](#configure-kubernetes-rbac-authorization)部分中描述的步骤类似。 在将 yaml 配置模板应用于群集之前，请将**群集角色绑定**下的**群集用户**替换为所需的用户。 

>[!IMPORTANT]
>如果为其授予 RBAC 绑定的用户在同一个 Azure AD 租户中，请根据 userPrincipalName 分配权限。 如果用户位于其他 Azure AD 租户中，请查询并使用 objectId 属性。

有关配置 AKS 群集**群集角色绑定**的其他帮助，请参阅[创建 RBAC 绑定](../../aks/azure-ad-integration-cli.md#create-rbac-binding)。

## <a name="next-steps"></a>后续步骤

现在，您已经拥有设置身份验证，您可以在群集中实时查看[指标](container-insights-livedata-metrics.md)、[部署](container-insights-livedata-deployments.md)[、事件和日志](container-insights-livedata-overview.md)。
