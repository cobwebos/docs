---
title: Azure AD 域服务中的常见警报和解决方法 | Microsoft Docs
description: 了解如何解决作为 Azure Active Directory 域服务运行状况的一部分而生成的常见警报
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 29bd7490ea9a03118f134ba11923ede2075e6fd5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722579"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>已知问题：Azure Active Directory 域服务中的常见警报和解决方法

作为应用程序的标识和身份验证的核心部分，Azure Active Directory 域服务 (Azure AD DS) 有时会出现问题。 如果遇到问题，这里有一些可帮助你使服务再次运行的常见警报以及相关的故障排除步骤。 你还可以随时[发起 Azure 支持请求][azure-support]以获得额外的排除故障帮助。

本文介绍有关 Azure AD DS 中常见警报的故障排除信息。

## <a name="aadds100-missing-directory"></a>AADDS100：缺少目录

### <a name="alert-message"></a>警报消息

与托管域关联的 Azure AD 目录可能已被删除。*托管域不再处于受支持的配置中。Microsoft 无法监视、管理、修补和同步托管域。*

### <a name="resolution"></a>解决方法

出现此错误通常因为将 Azure 订阅移到新的 Azure AD 目录，并删除了与 Azure AD DS 关联的旧 Azure AD 目录。

此错误无法恢复。 若要解决此警报，请[删除现有托管域](delete-aadds.md)，并在新目录中重新创建。 如果在删除托管域时遇到问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101：Azure AD B2C 在此目录中运行

### <a name="alert-message"></a>警报消息

无法在 Azure AD B2C 目录中启用 Azure AD 域服务。

### <a name="resolution"></a>解决方法

Azure AD DS 会自动与 Azure AD 目录同步。 如果已为 B2C 配置了 Azure AD 目录，则将无法部署和同步 Azure AD DS。

若要使用 Azure AD DS，必须使用以下步骤在非 Azure AD B2C 目录中重新创建托管域：

1. 从现有 Azure AD 目录中[删除托管域](delete-aadds.md)。
1. 创建不是 Azure AD B2C 目录的新 Azure AD 目录。
1. [创建替换托管域](tutorial-create-instance.md)。

托管域的运行状况会在两小时内自动更新，并删除警报。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103：地址在公共 IP 范围内

### <a name="alert-message"></a>警报消息

已启用 Azure AD 域服务的虚拟网络的 IP 地址范围在公共 IP 范围内。*必须在具有专用 IP 地址范围的虚拟网络中启用 Azure AD 域服务。* 此配置影响 Microsoft 监视、管理、修补和同步托管域的功能。

### <a name="resolution"></a>解决方法

在开始前，请确保了解[专用 IP v4 地址空间](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)。

在虚拟网络内部，VM 可以向 Azure 资源发出请求，这些资源与为子网配置的资源属于同一个 IP 地址范围。 如果为子网配置了公共 IP 地址范围，则在虚拟网络内路由的请求可能无法到达预期的 Web 资源。 此配置可能导致 Azure AD DS 出现不可预知的错误。

> [!NOTE]
> 如果你在 Internet 中拥有为虚拟网络配置的 IP 地址范围，则可以忽略此警报。 但是，Azure AD 域服务无法通过此配置提交到 [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)，因为它可能会导致不可预知的错误。

若要解除此警报，请删除现有托管域，然后在具有专用 IP 地址范围的虚拟网络中重新创建它。 此过程具有破坏性，因为托管域不可用，且所创建的任何自定义资源（如 OU 或服务帐户）都将丢失。

1. 从目录中[删除托管域](delete-aadds.md)。
1. 若要更新虚拟网络 IP 地址范围，请在 Azure 门户中搜索并选择“虚拟网络”。 为错误地设置了公共 IP 地址范围的 Azure AD DS 选择虚拟网络。
1. 在“设置”下，选择“地址空间”。
1. 选择并编辑现有地址范围，或添加其他地址范围，以更新地址范围。 请确保新的 IP 地址范围在专用 IP 范围内。 准备就绪后，请保存更改。
1. 在左侧导航栏中，选择“子网”。
1. 选择要编辑的子网，或创建其他子网。
1. 更新或指定专用 IP 地址范围，然后保存更改。
1. [创建替换托管域](tutorial-create-instance.md)。 请确保选择具有专用 IP 地址范围的已更新虚拟网络子网。

托管域的运行状况会在两小时内自动更新，并删除警报。

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106：找不到 Azure 订阅

### <a name="alert-message"></a>警报消息

与托管域关联的 Azure 订阅已删除。Azure AD 域服务需要有效的订阅才能继续正常工作。

### <a name="resolution"></a>解决方法

Azure AD DS 要求使用有效的订阅，且该订阅不能移动到其他订阅。 如果已删除托管域与之关联的 Azure 订阅，则必须重新创建 Azure 订阅和托管域。

1. [创建 Azure 订阅](../cost-management-billing/manage/create-subscription.md)。
1. 从现有 Azure AD 目录中[删除托管域](delete-aadds.md)。
1. [创建替换托管域](tutorial-create-instance.md)。

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107：Azure 订阅已禁用

### <a name="alert-message"></a>警报消息

与托管域关联的 Azure 订阅处于非活动状态。Azure AD 域服务需要有效的订阅才能继续正常工作。

### <a name="resolution"></a>解决方法

Azure AD DS 要求使用有效的订阅。 如果托管域与之关联的 Azure 订阅未处于活动状态，则必须续订以重新激活该订阅。

1. [续订 Azure 订阅](../cost-management-billing/manage/subscription-disabled.md)。
2. 续订订阅后，可通过 Azure AD DS 通知重新启用托管域。

再次启用托管域后，托管域的运行状况会在两小时内自动更新，并删除警报。

## <a name="aadds108-subscription-moved-directories"></a>AADDS108：订阅已移至其他目录

### <a name="alert-message"></a>警报消息

Azure AD 域服务使用的订阅已移到另一个目录。Azure AD 域服务需在同一目录中具有活动的订阅才能正常运行。

### <a name="resolution"></a>解决方法

Azure AD DS 要求使用有效的订阅，且该订阅不能移动到其他订阅。 如果已移动托管域与之关联的 Azure 订阅，则请将该订阅移回到上一个目录，或[从现有目录中删除托管域](delete-aadds.md)，并[在所选订阅中创建替换托管域](tutorial-create-instance.md)。

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109：找不到托管域的资源

### <a name="alert-message"></a>警报消息

用于托管域的资源已删除。需要此资源才能让 Azure AD 域服务正常运行。

### <a name="resolution"></a>解决方法

Azure AD DS 会创建附加资源以供其正常运行，例如公共 IP 地址、虚拟网络接口和负载均衡器。 如果上述任一资源被删除，则托管域将处于不受支持的状态，并阻止对域进行管理。 有关这些资源的详细信息，请参阅 [Azure AD DS 使用的网络资源](network-considerations.md#network-resources-used-by-azure-ad-ds)。

上述其中一个所需资源被删除时，将生成此警报。 如果资源的删除时间还不到 4 小时，则 Azure 平台还有机会自动重新创建已删除的资源。 以下步骤概述了如何检查运行状况和资源删除的时间戳：

1. 在 Azure 门户中，搜索并选择“域服务”。 选择你的托管域，例如 *aaddscontoso.com*。
1. 在左侧导航栏中，选择“运行状况”。
1. 在运行状况页上，选择 ID 为“AADDS109”的警报。
1. 警报包含一个时间戳，指出了首次发出该警报的时间。 如果该时间戳还不到 4 小时，则 Azure 平台也许能自动重新创建资源并解除警报。

    如果警报超过 4 小时，则托管域将处于不可恢复状态。 [删除托管域](delete-aadds.md)，然后[创建替换托管域](tutorial-create-instance.md)。

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110：与托管域关联的子网已满

### <a name="alert-message"></a>警报消息

选择用于部署 Azure AD 域服务的子网已满，没有空间用于保留需要创建的其他域控制器。

### <a name="resolution"></a>解决方法

Azure AD DS 的虚拟网络子网需要足够的 IP 地址以供自动创建的资源使用。 此 IP 地址空间包括在出现维护事件时创建替换资源的需求。 为了最大程度地降低可用 IP 地址不足的风险，请不要将其他资源（如你自己的 VM）部署到与托管域相同的虚拟网络子网中。

此错误无法恢复。 若要解除此警报，请[删除现有托管域](delete-aadds.md)并重新创建它。 如果在删除托管域时遇到问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111：服务主体未经授权

### <a name="alert-message"></a>警报消息

Azure AD 域服务用来为域提供服务的服务主体无权管理 Azure 订阅中的资源。该服务主体需要获取权限才能为托管域提供服务。

### <a name="resolution"></a>解决方法

某些自动生成的服务主体可用于管理和创建托管域的资源。 如果更改了其中一个服务主体的访问权限，则域将无法正确管理资源。 以下步骤说明如何了解服务主体并向服务主体授予访问权限：

1. 了解[基于角色的访问控制以及如何在 Azure 门户中授予对应用程序的访问权限](../role-based-access-control/role-assignments-portal.md)。
2. 评审 ID 为“abba844e-bc0e-44b0-947a-dc74e5d09022”的服务主体所具有的访问权限，并授予在以前的某个日期拒绝的访问权限。

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112：托管域中没有足够的 IP 地址

### <a name="alert-message"></a>警报消息

*我们发现，此域中虚拟网络的子网可能没有足够的 IP 地址。Azure AD 域服务在启用它的子网中至少需要两个可用的 IP 地址。我们建议在该子网中至少提供 3-5 个备用 IP 地址。如果在该子网中部署了其他虚拟机，从而耗尽了可用的 IP 地址，或者子网中可用 IP 地址数量有限制，则可能会发生这种情况。*

### <a name="resolution"></a>解决方法

Azure AD DS 的虚拟网络子网需要足够的 IP 地址以供自动创建的资源使用。 此 IP 地址空间包括在出现维护事件时创建替换资源的需求。 为了最大程度地降低可用 IP 地址不足的风险，请不要将其他资源（如你自己的 VM）部署到与托管域相同的虚拟网络子网中。

若要解除此警报，请删除现有托管域，然后在具有足够大 IP 地址范围的虚拟网络中重新创建它。 此过程具有破坏性，因为托管域不可用，且所创建的任何自定义资源（如 OU 或服务帐户）都将丢失。

1. 从目录中[删除托管域](delete-aadds.md)。
1. 若要更新虚拟网络 IP 地址范围，请在 Azure 门户中搜索并选择“虚拟网络”。 为具有较小 IP 地址范围的托管域选择虚拟网络。
1. 在“设置”下，选择“地址空间”。
1. 选择并编辑现有地址范围，或添加其他地址范围，以更新地址范围。 请确保新的 IP 地址范围足够大，可满足托管域子网范围的需求。 准备就绪后，请保存更改。
1. 在左侧导航栏中，选择“子网”。
1. 选择要编辑的子网，或创建其他子网。
1. 更新或指定足够大的 IP 地址范围，然后保存更改。
1. [创建替换托管域](tutorial-create-instance.md)。 请确保选择具有足够大 IP 地址范围的已更新虚拟网络子网。

托管域的运行状况会在两小时内自动更新，并删除警报。

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113：资源不可恢复

### <a name="alert-message"></a>警报消息

检测到 Azure AD 域服务使用的资源处于意外状态，且无法恢复。

### <a name="resolution"></a>解决方法

此错误无法恢复。 若要解除此警报，请[删除现有托管域](delete-aadds.md)并重新创建它。 如果在删除托管域时遇到问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

## <a name="aadds114-subnet-invalid"></a>AADDS114：子网无效

### <a name="alert-message"></a>警报消息

为 Azure AD 域服务部署选择的子网无效，且不可用。

### <a name="resolution"></a>解决方法

此错误无法恢复。 若要解除此警报，请[删除现有托管域](delete-aadds.md)并重新创建它。 如果在删除托管域时遇到问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

## <a name="aadds115-resources-are-locked"></a>AADDS115：资源已锁定

### <a name="alert-message"></a>警报消息

由于目标范围已锁定，托管域使用的一个或多个网络资源无法运行。

### <a name="resolution"></a>解决方法

可以将资源锁应用于 Azure 资源以防止更改或删除。 由于 Azure AD DS 是一种托管服务，因此 Azure 平台需要具有进行配置更改的能力。 如果对某些 Azure AD DS 组件应用了资源锁，则 Azure 平台将无法执行其管理任务。

若要检查 Azure AD DS 组件上的资源锁并将其删除，请完成以下步骤：

1. 对于资源组中的每个托管域网络组件，如虚拟网络、网络接口或公共 IP 地址，请检查 Azure 门户中的操作日志。 这些操作日志应指示操作失败的原因以及应用资源锁的位置。
1. 选择应用有锁的资源，然后在“锁”下，选择并删除锁。

## <a name="aadds116-resources-are-unusable"></a>AADDS116：资源不可用

### <a name="alert-message"></a>警报消息

由于策略限制，托管域使用的一个或多个网络资源无法运行。

### <a name="resolution"></a>解决方法

对可控制配置操作范围的 Azure 资源和资源组应用策略。 由于 Azure AD DS 是一种托管服务，因此 Azure 平台需要具有进行配置更改的能力。 如果对某些 Azure AD DS 组件应用了策略，则 Azure 平台可能无法执行其管理任务。

若要检查 Azure AD DS 组件上应用的策略并对其进行更新，请完成以下步骤：

1. 对于资源组中的每个托管域网络组件，如虚拟网络、NIC 或公共 IP 地址，请检查 Azure 门户中的操作日志。 这些操作日志应指示操作失败的原因以及应用限制性策略的位置。
1. 选择应用了某个策略的资源，然后在“策略”下选择并编辑该策略，以降低其限制性。

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500：同步在一段时间内未完成

### <a name="alert-message"></a>警报消息

*托管域上次于 [date] 与 Azure AD 进行同步。用户可能无法登录到托管域，或者组成员身份可能未与 Azure AD 同步。*

### <a name="resolution"></a>解决方法

[检查 Azure AD DS 运行状况](check-health.md)，看看是否有任何警报指示托管域的配置存在问题。 网络配置问题可能会阻止与 Azure AD 的同步。 如果你能够解除指示配置问题的警报，则请等待两个小时，然后返回查看同步是否已成功完成。

以下常见原因可导致同步在托管域中停止：

* 所需的网络连接被阻止。 若要详细了解如何检查 Azure 虚拟网络是否存在问题以及相关要求，请参阅[对网络安全组进行故障排除](alert-nsg.md)以及 [Azure AD DS 服务的网络要求](network-considerations.md)。
*  部署托管域时，密码同步未设置或未成功完成。 你可以为[仅限云的用户](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)或[本地的混合用户](tutorial-configure-password-hash-sync.md)设置密码同步。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501：已在一段时间内未执行备份

### <a name="alert-message"></a>警报消息

*托管域上次于 [date] 进行备份。*

### <a name="resolution"></a>解决方法

[检查 Azure AD DS 运行状况](check-health.md)，看看是否有警报指示托管域的配置存在问题。 网络配置问题可能会阻止 Azure 平台成功进行备份。 如果你能够解除指示配置问题的警报，则请等待两个小时，然后返回查看同步是否已成功完成。

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503：由于订阅禁用而暂停

### <a name="alert-message"></a>警报消息

*由于与域关联的 Azure 订阅未处于活动状态，托管域已暂停。*

### <a name="resolution"></a>解决方法

> [!WARNING]
> 如果托管域挂起很长一段时间，则存在其被删除的危险。 请尽快查明挂起的原因。 有关详细信息，请参阅[了解 Azure AD DS 的挂起状态](suspension.md)。

Azure AD DS 要求使用有效的订阅。 如果托管域与之关联的 Azure 订阅未处于活动状态，则必须续订以重新激活该订阅。

1. [续订 Azure 订阅](../cost-management-billing/manage/subscription-disabled.md)。
2. 续订订阅后，可通过 Azure AD DS 通知重新启用托管域。

再次启用托管域后，托管域的运行状况会在两小时内自动更新，并删除警报。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504：由于配置无效而暂停

### <a name="alert-message"></a>警报消息

*由于配置无效，托管域已暂停。服务已很长时间无法为托管域管理、修补或更新域控制器。*

### <a name="resolution"></a>解决方法

> [!WARNING]
> 如果托管域挂起很长一段时间，则存在其被删除的危险。 请尽快查明挂起的原因。 有关详细信息，请参阅[了解 Azure AD DS 的挂起状态](suspension.md)。

[检查 Azure AD DS 运行状况](check-health.md)，看看是否有警报指示托管域的配置存在问题。 如果你能够解除指示配置问题的警报，则请等待两个小时，然后返回查看同步是否已完成。 准备就绪后，请[发起 Azure 支持请求][azure-support]以重新启用托管域。

## <a name="next-steps"></a>后续步骤

如果仍有问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md