---
title: 设置用于容器实时数据（预览版）的 Azure Monitor| Microsoft Docs
description: 本文介绍在不将 kubectl 与适用于容器的 Azure Monitor 配合使用的情况下，如何设置容器日志 (stdout/stderr) 和事件的实时视图。
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: references_regions
ms.openlocfilehash: cbd697f743d0d3369a8c35537ac2755ae5d3fc8c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516458"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>如何设置实时数据（预览版）功能

若要从 Azure Kubernetes 服务 (AKS) 群集通过用于容器的 Azure Monitor 查看实时数据（预览版），你需要通过配置身份验证来授予访问 Kubernetes 数据的权限。 此安全配置允许在 Azure 门户中直接通过 Kubernetes API 实时访问你的数据。

此功能支持采用以下方法来控制对日志、事件和指标的访问：

- 没有启用 Kubernetes RBAC 授权的 AKS
- 启用了 Kubernetes RBAC 授权的 AKS
    - 配置了群集角色绑定 **[clusterMonitoringUser](/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)** 的 AKS
- 启用了基于 SAML 的 Azure Active Directory (AD) 单一登录的 AKS

这些说明要求对 Kubernetes 群集具有管理访问权限，在配置为使用 Azure Active Directory (AD) 进行用户身份验证的情况下，要求对 Azure AD 具有管理访问权限。

本文介绍了如何配置身份验证，以便从群集中控制对实时数据（预览版）功能的访问：

- 启用了基于角色的访问控制 (RBAC) 的 AKS 群集
- 集成了 Azure Active Directory 的 AKS 群集。

>[!NOTE]
>此功能不支持以[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)形式启用的 AKS 群集。 此功能依赖于从浏览器通过代理服务器直接访问 Kubernetes API。 启用网络安全以阻止通过此代理访问 Kubernetes API 会阻止此流量。

## <a name="authentication-model"></a>身份验证模型

实时数据（预览版）功能使用与 `kubectl` 命令行工具等同的 Kubernetes API。 Kubernetes API 终结点使用自签名证书，你的浏览器无法验证该证书的有效性。 此功能使用内部代理通过 AKS 服务来验证证书，确保流量受信任。

Azure 门户会提示你验证你用于 Azure Active Directory 群集的登录凭据，并将你重定向到你在创建群集期间设置（并在本文中重新配置）的客户端注册。 此行为类似于 `kubectl` 所需的身份验证过程。

>[!NOTE]
>对你的群集的授权由 Kubernetes 以及为它配置的安全模型管理。 访问此功能的用户需要有权下载 Kubernetes 配置 (*kubeconfig*)，类似于运行 `az aks get-credentials -n {your cluster name} -g {your resource group}`。 如果启用了 Azure RBAC 并且 AKS 群集未启用 RBAC 授权，则此配置文件包含 **Azure Kubernetes 服务群集用户角色**的授权和身份验证令牌。 当为 AKS 启用了 Azure Active Directory (AD) 基于 SAML 的单一登录时，它包含有关 Azure AD 的信息和客户端注册详细信息。

>[!IMPORTANT]
>此功能的用户需要具有群集的 [Azure Kubernetes 群集用户角色](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions)才能下载 `kubeconfig` 并使用此功能。 用户**不**需要具有群集的参与者访问权限便可使用此功能。

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>将 clusterMonitoringUser 用于启用了 RBAC 的群集

AKS 添加了一个名为 **clusterMonitoringUser** 的新的 Kubernetes 群集角色绑定，这样就不需要在进行[启用 RBAC](#configure-kubernetes-rbac-authorization) 的授权后应用额外的配置更改来允许 Kubernetes 用户角色绑定 **clusterUser** 对实时数据（预览版）功能进行访问。 此群集角色绑定具有现成的所有必需权限，可以访问 Kubernetes API 和用于使用实时数据（预览版）功能的终结点。

为了通过此新用户使用实时数据（预览版）功能，你需要是 AKS 群集资源上的[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色的成员。 当启用了适用于容器的 Azure Monitor 时，默认情况下，它配置为使用此用户进行身份验证。 如果群集中不存在 clusterMonitoringUser 角色绑定，则会改用 **clusterUser** 进行身份验证。

AKS 在 2020 年 1 月发布了此新的角色绑定，因此在 2020 年 1 月之前创建的群集没有此项。 如果你有一个在 2020 年 1 月之前创建的群集，可以通过在该现有群集上执行 PUT 操作将新的 **clusterMonitoringUser** 添加到该群集，也可以通过在群集上执行任何会在群集上执行 PUT 操作的其他操作（例如更新群集版本）来这样做。

## <a name="kubernetes-cluster-without-rbac-enabled"></a>未启用 RBAC 的 Kubernetes 群集

如果 Kubernetes 群集未配置 Kubernetes RBAC 授权或集成 Azure AD 单一登录，则不需执行这些步骤。 这是因为你在非 RBAC 配置中默认拥有管理权限。

## <a name="configure-kubernetes-rbac-authorization"></a>配置 Kubernetes RBAC 授权

当启用 Kubernetes RBAC 授权时，将使用两个用户（**clusterUser** 和 **clusterAdmin**）来访问 Kubernetes API。 这类似于在不使用管理选项的情况下运行 `az aks get-credentials -n {cluster_name} -g {rg_name}`。 这意味着需要向 **clusterUser** 授予对 Kubernetes API 中的终结点的访问权限。

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

2. 若要更新配置，请运行以下命令：`kubectl apply -f LogReaderRBAC.yaml`。

>[!NOTE]
> 如果已将以前版本的 `LogReaderRBAC.yaml` 文件应用于群集，请通过复制并粘贴上面步骤 1 中所示的新代码对该文件进行更新，然后运行步骤 2 中显示的命令将其应用于群集。

## <a name="configure-ad-integrated-authentication"></a>配置 AD 集成式身份验证

配置为使用 Azure Active Directory (AD) 进行用户身份验证的 AKS 群集会利用访问此功能的人员的登录凭据。 在此配置中，你可以使用自己的 Azure AD 身份验证令牌登录到 AKS 群集。

必须重新配置 Azure AD 客户端注册，以允许 Azure 门户将授权页重定向为受信任的重定向 URL。 然后通过 **ClusterRoles** 和 **ClusterRoleBindings**，授予来自 Azure AD 的用户直接访问相同 Kubernetes API 终结点的权限。

有关 Kubernetes 中的高级安全设置的详细信息，请查看 [Kubernetes 文档](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)。

>[!NOTE]
>如果你要创建新的启用了 RBAC 的群集，请参阅[将 Azure Active Directory 与 Azure Kubernetes 服务集成](../../aks/azure-ad-integration-cli.md)并按照步骤来配置 Azure AD 身份验证。 在创建客户端应用程序的步骤中，该部分中的一个注释突出显示了你需要为适用于容器的 Azure Monitor 创建的与下面步骤 3 中指定的 URL 匹配的两个重定向 URL。

### <a name="client-registration-reconfiguration"></a>重新配置客户端注册

1. 在 Azure 门户中的“Azure Active Directory”>“应用注册”下，找到你在 Azure AD 中的 Kubernetes 群集的客户端注册。

2. 从左侧窗格中选择“身份验证”。

3. 将两个重定向 URL 作为 **Web** 应用程序类型添加到此列表。 第一个基 URL 值应为 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，第二个基 URL 值应为 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`。

    >[!NOTE]
    >如果你在 Azure 中国版中使用此功能，则第一个基 URL 值应为 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，第二个基 URL 值应为 `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`。

4. 注册重定向 URL 后，在“隐式授权”下选择“访问令牌”和“ID 令牌”选项，然后保存所做的更改。

>[!NOTE]
>通过 Azure Active Directory 配置身份验证以便实现单一登录的操作只能在初次部署新 AKS 群集过程中完成。 不能为已部署的 AKS 群集配置单一登录。

>[!IMPORTANT]
>如果使用更新的 URI 重新配置了用于用户身份验证的 Azure AD，请清除浏览器的缓存，确保更新的身份验证令牌已下载并应用。

## <a name="grant-permission"></a>授予权限

必须向每个 Azure AD 帐户授予对 Kubernetes 中相应 API 的权限，以便访问实时数据（预览版）功能。 向 Azure Active Directory 帐户授权的步骤类似于 [Kubernetes RBAC 身份验证](#configure-kubernetes-rbac-authorization)部分所述的步骤。 将 yaml 配置模板应用于群集之前，请将 **ClusterRoleBinding** 下的 **clusterUser** 替换为所需的用户。

>[!IMPORTANT]
>如果你为其授予 RBAC 绑定的用户在同一个 Azure AD 租户中，请根据 userPrincipalName 分配权限。 如果该用户位于不同的 Azure AD 租户中，请查询并使用 objectId 属性。

有关配置 AKS 群集 **ClusterRoleBinding** 的更多帮助信息，请参阅[创建 RBAC 绑定](../../aks/azure-ad-integration-cli.md#create-rbac-binding)。

## <a name="next-steps"></a>后续步骤

现在，你已设置了身份验证，可以从群集中实时查看[指标](container-insights-livedata-metrics.md)、[部署](container-insights-livedata-deployments.md)以及[事件和日志](container-insights-livedata-overview.md)了。
