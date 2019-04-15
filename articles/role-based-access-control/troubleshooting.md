---
title: 对 Azure 资源的 RBAC 问题进行故障排除 | Microsoft Docs
description: 对 Azure 资源基于角色的访问控制 (RBAC) 问题进行故障排除。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d85c49cc8533b88382de81f8f12fde7116afb69a
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407583"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>对 Azure 资源的 RBAC 问题进行故障排除

本文解答有关 Azure 资源的基于角色的访问控制 (RBAC) 的常见问题，以便你了解在 Azure 门户中使用角色时可能出现的情况，并可对访问问题进行故障排除。

## <a name="problems-with-rbac-role-assignments"></a>RBAC 角色分配出现问题

- 如果您不能在添加 Azure 门户中的角色分配**访问控制 (IAM)** 因为**添加** > **添加角色分配**选项处于禁用状态或因为遇到权限错误"对象 id 的客户端不具有执行操作"，检查你目前已使用分配有角色的用户`Microsoft.Authorization/roleAssignments/write`如权限[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)在想要将角色分配的范围。
- 如果收到错误消息"可以创建没有更多的角色分配 (代码：RoleAssignmentLimitExceeded)"时尝试将角色分配，尝试减少通过将角色分配到组的角色分配。 Azure 对于每个订阅最多支持 **2000** 个角色分配。

## <a name="problems-with-custom-roles"></a>自定义角色出现问题

- 如果需要有关如何创建自定义角色的步骤，请参阅使用自定义角色教程[Azure PowerShell](tutorial-custom-role-powershell.md)或[Azure CLI](tutorial-custom-role-cli.md)。
- 如果你无法更新现有的自定义角色，请检查你目前已使用分配有角色的用户`Microsoft.Authorization/roleDefinition/write`如权限[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator).
- 如果您无法删除自定义角色，并收到错误消息"已存在引用角色 (代码：RoleDefinitionHasAssignments)"，则仍在使用自定义角色的角色分配。 删除这些角色分配，然后重试删除自定义角色。
- 如果收到错误消息"角色定义超限。 可以创建没有更多的角色定义 (代码：RoleDefinitionLimitExceeded)"时尝试创建新的自定义角色，删除不会使用任何自定义角色。 Azure 在一个租户中最多支持 **2000** 个自定义角色。
- 如果收到错误类似于"客户端有权对作用域 / {subscriptionid}，执行操作 'Microsoft.Authorization/roleDefinitions/write，但找不到链接的订阅"时尝试更新自定义角色，检查是否一个或多个[可分配范围](role-definitions.md#assignablescopes)已在租户中删除。 如果已删除作用域，然后创建支持票证，如没有自助服务的解决方案可在这一次。

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>在租户之间移动订阅时恢复 RBAC

- 如果您需要有关如何将订阅转让的步骤与其他 Azure AD 租户，请参阅[的 Azure 订阅所有权转让给另一个帐户](../billing/billing-subscription-transfer.md)。
- 如果将订阅转让到不同的 Azure AD 租户，所有角色分配从源 Azure AD 租户中永久删除和不会迁移到目标 Azure AD 租户。 必须在目标租户中重新创建角色分配。
- 如果你是 Azure AD 全局管理员和你没有订阅的访问权限后租户间移动，使用**访问 Azure 资源管理**开关切换为暂时[提升你的访问权限](elevate-access-global-admin.md)若要获取订阅的访问权限。

## <a name="issues-with-service-admins-or-co-admins"></a>服务管理员或共同管理员出现问题

- 如果遇到问题的服务管理员或协同管理员，请参阅[添加或更改 Azure 订阅管理员](../billing/billing-add-change-azure-subscription-administrator.md)和[经典订阅管理员角色、 Azure RBAC 角色和 Azure AD管理员角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>访问被拒绝或权限错误

- 如果收到权限错误"对象 id 的客户端不具有范围执行操作的授权 (代码：AuthorizationFailed)"时尝试创建资源，检查你目前已使用在所选范围内具有对资源的写入权限的角色分配的用户。 例如，若要管理的资源组中的虚拟机，你应该[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色上的资源组 （或父作用域）。 为每个内置角色的权限的列表，请参阅[Azure 资源的内置角色](built-in-roles.md)。
- 如果遇到权限错误"你没有权限来创建支持请求"时尝试创建或更新支持票证，检查你目前已使用分配有角色的用户`Microsoft.Support/supportTickets/write`权限，如[支持请求参与者](built-in-roles.md#support-request-contributor)。

## <a name="rbac-changes-are-not-being-detected"></a>未检测到 RBAC 更改

Azure 资源管理器有时会缓存配置和数据以提高性能。 创建或删除角色分配时，更改最多可能需要 30 分钟才能生效。 如果使用的是 Azure 门户、Azure PowerShell 或 Azure CLI，则可以通过注销和登录来强制刷新角色分配更改。 如果使用 REST API 调用进行角色分配更改，则可以通过刷新访问令牌来强制刷新。

## <a name="web-app-features-that-require-write-access"></a>需要写访问权限的 Web 应用功能

如果为用户授予单个 Web 应用的只读访问权限，某些功能可能会被禁用，这可能不是你所期望的。 以下管理功能需要对 Web 应用具有**写**访问权限（参与者或所有者），并且在任何只读方案中不可用。

* 命令（例如启动、停止等。）
* 更改设置（如常规配置、缩放设置、备份设置和监视设置）
* 访问发布凭据和其他机密（如应用设置和连接字符串）
* 流式处理日志
* 诊断日志配置
* 控制台（命令提示符）
* 活动和最新部署（适用于本地 Git 连续部署）
* 估计费用
* Web 测试
* 虚拟网络（如果虚拟网络是以前具有写访问权限的用户所配置的，则仅对读者可见）。

如果无法访问以上任何磁贴，则需要让管理员提供对 Web 应用的“参与者”访问权限。

## <a name="web-app-resources-that-require-write-access"></a>需要写访问权限的 Web 应用资源

由于存在几个相互作用的不同资源，Web 应用程序是复杂的。 下面是包含几个网站的典型资源组：

![Web 应用程序资源组](./media/troubleshooting/website-resource-model.png)

因此，如果只授予某人对 Web 应用的访问权限，则 Azure 门户中的网站边栏选项卡上的很多功能将被禁用。

这些项需要对与网站对应的**应用服务计划**具有**写**访问权限：  

* 查看 Web 应用的定价层（“免费”或“标准”）  
* 规模配置（实例数、虚拟机大小、自动缩放设置）  
* 配额（存储、带宽、CPU）  

这些项需要对包含网站的整个**资源组**具有**写**访问权限：  

* SSL 证书和绑定（SSL 证书可以在同一资源组和地理位置中的站点之间共享）  
* 警报规则  
* 自动缩放设置  
* Application Insights 组件  
* Web 测试  

## <a name="virtual-machine-features-that-require-write-access"></a>需要写访问权限的虚拟机功能

与 Web 应用类似，虚拟机边栏选项卡上的某些功能需要对虚拟机或资源组中的其他资源具有写访问权限。

虚拟机与域名、虚拟网络、存储帐户和警报规则相关。

这些项需要对**虚拟机**具有**写**访问权限：

* 终结点  
* IP 地址  
* 磁盘  
* 扩展  

这些项需要对**虚拟机**和其所在的**资源组**（以及域名）具有**写**访问权限：  

* 可用性集  
* 负载均衡集  
* 警报规则  

如果无法访问以上任何磁贴，则需要让管理员提供对资源组的“参与者”访问权限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和写访问权限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要写入权限。 例如，如果给用户分配读者角色，他们将无法查看函数应用中的函数。 门户将显示 (无访问权限)。

![函数应用无访问权限](./media/troubleshooting/functionapps-noaccess.png)

读者可单击“平台功能”选项卡，然后单击“所有设置”查看与函数应用（类似于 Web 应用）相关的一些设置，但无法修改任何这些设置。

## <a name="next-steps"></a>后续步骤
* [使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](role-assignments-portal.md)
* [查看 Azure 资源的 RBAC 更改的活动日志](change-history-report.md)

