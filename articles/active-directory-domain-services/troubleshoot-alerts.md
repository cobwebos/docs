---
title: Azure AD 域服务中的常见警报和解决方法 |Microsoft Docs
description: 了解如何解决作为 Azure Active Directory 域服务的运行状况状态的一部分生成的常见警报
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 0bb02e6436bf9c9ebb9e54efa73aeed03ab44f3e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512658"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>已知问题： Azure Active Directory 域服务中的常见警报和解决方法

作为应用程序的标识和身份验证的核心部分，Azure Active Directory 域服务（Azure AD DS）有时会出现问题。 如果遇到问题，则会出现一些常见的警报和相关的故障排除步骤，以帮助你再次运行操作。 你还可以随时[打开 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

本文提供 Azure AD DS 中常见警报的疑难解答信息。

## <a name="aadds100-missing-directory"></a>AADDS100：缺少目录

### <a name="alert-message"></a>警报消息

*与托管域关联的 Azure AD 目录可能已被删除。托管域不再处于受支持的配置中。Microsoft 无法监视、管理、修补和同步托管域。*

### <a name="resolution"></a>分辨率

此错误通常是在将 Azure 订阅移动到新的 Azure AD 目录，并且删除了与 Azure AD DS 关联的旧 Azure AD 目录时产生的。

此错误无法恢复。 若要解决此警报，请[删除现有 AZURE AD DS 托管域](delete-aadds.md)，并在新目录中重新创建它。 如果在删除 Azure AD DS 托管域时遇到问题，请[打开 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101：Azure AD B2C 在此目录中运行

### <a name="alert-message"></a>警报消息

无法在 Azure AD B2C 目录中启用 Azure AD 域服务。

### <a name="resolution"></a>分辨率

Azure AD DS 会自动与 Azure AD 目录同步。 如果 Azure AD 目录配置为 B2C，则无法部署和同步 Azure AD DS。

若要使用 Azure AD DS，必须使用以下步骤在非 Azure AD B2C 目录中重新创建托管域：

1. 从现有的 Azure AD 目录中[删除 AZURE AD DS 托管域](delete-aadds.md)。
1. 创建不是 Azure AD B2C 目录的新 Azure AD 目录。
1. [创建 AZURE AD DS 托管域的替换](tutorial-create-instance.md)。

Azure AD DS 托管域的运行状况在两小时内自动更新，并删除警报。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103：地址在公共 IP 范围内

### <a name="alert-message"></a>警报消息

*已启用 Azure AD 域服务的虚拟网络的 IP 地址范围位于公共 IP 范围内。必须在具有专用 IP 地址范围的虚拟网络中启用 Azure AD 域服务。此配置影响 Microsoft 监视、管理、修补和同步托管域的能力。*

### <a name="resolution"></a>分辨率

在开始之前，请确保了解[专用 IP v4 地址空间](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)。

在虚拟网络中，Vm 可以向为子网配置的相同 IP 地址范围内的 Azure 资源发出请求。 如果为子网配置公共 IP 地址范围，则在虚拟网络内路由的请求可能无法到达预期的 web 资源。 此配置可能会导致与 Azure AD DS 发生不可预知的错误。

> [!NOTE]
> 如果你拥有在虚拟网络中配置的 internet 中的 IP 地址范围，则可以忽略此警报。 但是，Azure AD 域服务无法通过此配置提交到[SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)]，因为这可能导致不可预知的错误。

若要解决此警报，请删除现有 Azure AD DS 托管域，并在具有专用 IP 地址范围的虚拟网络中重新创建它。 此过程会造成中断，因为 Azure AD DS 托管域不可用，而您创建的任何自定义资源（如 Ou 或服务帐户）都将丢失。

1. 从目录中[删除 AZURE AD DS 托管域](delete-aadds.md)。
1. 若要更新虚拟网络 IP 地址范围，请在 Azure 门户中搜索并选择 "*虚拟网络*"。 为 Azure AD DS 选择不正确设置了公共 IP 地址范围的虚拟网络。
1. 在 "**设置**" 下，选择 "*地址空间*"。
1. 通过选择现有地址范围并对其进行编辑来更新地址范围，或添加其他地址范围。 请确保新的 IP 地址范围在专用 IP 范围内。 准备就绪后，**保存**所做的更改。
1. 在左侧导航栏中选择 "**子网**"。
1. 选择要编辑的子网，或创建其他子网。
1. 更新或指定专用 IP 地址范围，并**保存**所做的更改。
1. [创建 AZURE AD DS 托管域的替换](tutorial-create-instance.md)。 请确保选择具有专用 IP 地址范围的已更新虚拟网络子网。

Azure AD DS 托管域的运行状况在两小时内自动更新，并删除警报。

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: 找不到 Azure 订阅

### <a name="alert-message"></a>警报消息

*与托管域关联的 Azure 订阅已被删除。 Azure AD 域服务需要有效的订阅才能继续正常工作。*

### <a name="resolution"></a>分辨率

Azure AD DS 要求使用有效的订阅，不能将其移动到其他订阅。 如果删除了与 Azure AD DS 托管域关联的 Azure 订阅，则必须重新创建 Azure 订阅并 Azure AD DS 托管域。

1. [创建 Azure 订阅](../cost-management-billing/manage/create-subscription.md)。
1. 从现有的 Azure AD 目录中[删除 AZURE AD DS 托管域](delete-aadds.md)。
1. [创建 AZURE AD DS 托管域的替换](tutorial-create-instance.md)。

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Azure 订阅已禁用

### <a name="alert-message"></a>警报消息

*与托管域关联的 Azure 订阅未处于活动状态。 Azure AD 域服务需要有效的订阅才能继续正常工作。*

### <a name="resolution"></a>分辨率

Azure AD DS 要求使用有效的订阅。 如果 Azure AD DS 托管域关联的 Azure 订阅未处于活动状态，则必须续订该订阅以重新激活订阅。

1. [续订 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 续订订阅后，可以通过 Azure AD DS 通知重新启用托管域。

再次启用托管域时，Azure AD DS 托管域的运行状况将在两小时内自动更新，并删除警报。

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: 在目录中移动了订阅

### <a name="alert-message"></a>警报消息

*Azure AD 域服务使用的订阅已移到另一个目录。Azure AD 域服务需要在同一目录中具有活动订阅才能正常工作。*

### <a name="resolution"></a>分辨率

Azure AD DS 要求使用有效的订阅，不能将其移动到其他订阅。 如果移动了与 Azure AD DS 托管域关联的 Azure 订阅，则将订阅移回上一个目录，或从现有目录中[删除托管域](delete-aadds.md)，并[在所选订阅中创建替换 Azure AD DS 托管域](tutorial-create-instance.md)。

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: 找不到托管域的资源

### <a name="alert-message"></a>警报消息

*用于托管域的资源已被删除。此资源是 Azure AD 域服务正常运行所必需的。*

### <a name="resolution"></a>分辨率

Azure AD DS 会创建更多的资源以正常运行，例如公共 IP 地址、虚拟网络接口和负载均衡器。 如果删除这些资源中的任何一个，则托管域将处于不受支持的状态，并阻止对域进行管理。 有关这些资源的详细信息，请参阅[AZURE AD DS 使用的网络资源](network-considerations.md#network-resources-used-by-azure-ad-ds)。

删除其中一个所需资源时，将生成此警报。 如果删除资源的时间不到4小时之前，Azure 平台可能会自动重新创建已删除的资源。 以下步骤概述了如何检查运行状况状态和资源删除的时间戳：

1. 在 Azure 门户中，搜索并选择 "**域服务**"。 选择 Azure AD DS 托管域，如*aadds.contoso.com*。
1. 在左侧导航栏中，选择 "**运行状况**"。
1. 在 "运行状况" 页上，选择 ID 为*AADDS109*的警报。
1. 警报的时间戳是第一次找到它的时间。 如果时间戳不到4小时之前，Azure 平台可能会自动重新创建资源并自行解决警报。

    如果警报超过4小时，则 Azure AD DS 托管域处于不可恢复的状态。 [删除 AZURE AD DS 托管域](delete-aadds.md)，然后[创建一个替代托管域](tutorial-create-instance.md)。

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: 与托管域关联的子网已满

### <a name="alert-message"></a>警报消息

选择用于部署 Azure AD 域服务的子网已满，没有空间用于保留需要创建的其他域控制器。

### <a name="resolution"></a>分辨率

用于 Azure AD DS 的虚拟网络子网需要为自动创建的资源提供足够的 IP 地址。 此 IP 地址空间包括在出现维护事件时创建替换资源的需要。 若要最大程度地降低可用 IP 地址不足的风险，请不要将其他资源（如自己的 Vm）部署到与 Azure AD DS 相同的虚拟网络子网中。

此错误无法恢复。 若要解决此警报，请[删除现有 AZURE AD DS 托管域](delete-aadds.md)，然后重新创建它。 如果在删除 Azure AD DS 托管域时遇到问题，请[打开 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111：未授权的服务主体

### <a name="alert-message"></a>警报消息

*Azure AD 域服务用来为你的域服务的服务主体无权管理 Azure 订阅上的资源。服务主体需要获取服务托管域的权限。*

### <a name="resolution"></a>分辨率

某些自动生成的服务主体用于管理和创建 Azure AD DS 托管域的资源。 如果更改了其中一个服务主体的访问权限，则域无法正确管理资源。 以下步骤说明如何了解并向服务主体授予访问权限：

1. 了解[基于角色的访问控制以及如何向 Azure 门户中的应用程序授予访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。
2. 查看 ID 为*abba844e-bc0e-44b0-947a-dc74e5d09022*的服务主体所具有的访问权限，并授予之前拒绝的访问权限。

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: 托管域中没有足够的 IP 地址

### <a name="alert-message"></a>警报消息

*我们确定此域中虚拟网络的子网可能没有足够的 IP 地址。Azure AD 域服务在启用它的子网中至少需要两个可用 IP 地址。建议在子网中至少具有3-5 个备用 IP 地址。如果在子网中部署了其他虚拟机，则可能会发生这种情况，从而耗尽可用 IP 地址的数目，或者如果对子网中可用 IP 地址的数量有限制。*

### <a name="resolution"></a>分辨率

用于 Azure AD DS 的虚拟网络子网需要为自动创建的资源提供足够的 IP 地址。 此 IP 地址空间包括在出现维护事件时创建替换资源的需要。 若要最大程度地降低可用 IP 地址不足的风险，请不要将其他资源（如自己的 Vm）部署到与 Azure AD DS 相同的虚拟网络子网中。

若要解决此警报，请删除现有 Azure AD DS 托管域，并在具有足够大 IP 地址范围的虚拟网络中重新创建它。 此过程会造成中断，因为 Azure AD DS 托管域不可用，而您创建的任何自定义资源（如 Ou 或服务帐户）都将丢失。

1. 从目录中[删除 AZURE AD DS 托管域](delete-aadds.md)。
1. 若要更新虚拟网络 IP 地址范围，请在 Azure 门户中搜索并选择 "*虚拟网络*"。 为具有小型 IP 地址范围 Azure AD DS 选择虚拟网络。
1. 在 "**设置**" 下，选择 "*地址空间*"。
1. 通过选择现有地址范围并对其进行编辑来更新地址范围，或添加其他地址范围。 请确保新的 IP 地址范围足以满足 Azure AD DS 子网范围。 准备就绪后，**保存**所做的更改。
1. 在左侧导航栏中选择 "**子网**"。
1. 选择要编辑的子网，或创建其他子网。
1. 更新或指定足够大的 IP 地址范围，并**保存**所做的更改。
1. [创建 AZURE AD DS 托管域的替换](tutorial-create-instance.md)。 请确保使用足够大的 IP 地址范围选取更新的虚拟网络子网。

Azure AD DS 托管域的运行状况在两小时内自动更新，并删除警报。

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: 资源不可恢复

### <a name="alert-message"></a>警报消息

检测到 Azure AD 域服务使用的资源处于意外状态，且无法恢复。

### <a name="resolution"></a>分辨率

此错误无法恢复。 若要解决此警报，请[删除现有 AZURE AD DS 托管域](delete-aadds.md)，然后重新创建它。 如果在删除 Azure AD DS 托管域时遇到问题，请[打开 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

## <a name="aadds114-subnet-invalid"></a>AADDS114: 子网无效

### <a name="alert-message"></a>警报消息

为 Azure AD 域服务部署选择的子网无效，且不可用。

### <a name="resolution"></a>分辨率

此错误无法恢复。 若要解决此警报，请[删除现有 AZURE AD DS 托管域](delete-aadds.md)，然后重新创建它。 如果在删除 Azure AD DS 托管域时遇到问题，请[打开 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

## <a name="aadds115-resources-are-locked"></a>AADDS115: 资源已锁定

### <a name="alert-message"></a>警报消息

由于目标范围已锁定，托管域使用的一个或多个网络资源无法运行。

### <a name="resolution"></a>分辨率

可以将资源锁应用于 Azure 资源以防止更改或删除。 由于 Azure AD DS 是一种托管服务，Azure 平台需要能够进行配置更改。 如果对某些 Azure AD DS 组件应用资源锁，Azure 平台将无法执行其管理任务。

若要检查 Azure AD DS 组件上的资源锁并将其删除，请完成以下步骤：

1. 对于资源组中的每个 Azure AD DS 网络组件，如虚拟网络、网络接口或公共 IP 地址，请检查 Azure 门户中的操作日志。 这些操作日志应指示操作失败的原因以及资源锁定的应用位置。
1. 选择应用了锁的资源，然后在 "**锁定**" 下，选择并删除锁。

## <a name="aadds116-resources-are-unusable"></a>AADDS116: 资源不可用

### <a name="alert-message"></a>警报消息

由于策略限制，托管域使用的一个或多个网络资源无法运行。

### <a name="resolution"></a>分辨率

策略适用于控制允许哪些配置操作的 Azure 资源和资源组。 由于 Azure AD DS 是一种托管服务，Azure 平台需要能够进行配置更改。 如果在某些 Azure AD DS 组件上应用了策略，则 Azure 平台可能无法执行其管理任务。

若要检查 Azure AD DS 组件上应用的策略并对其进行更新，请完成以下步骤：

1. 对于资源组中的每个 Azure AD DS 网络组件，如虚拟网络、NIC 或公共 IP 地址，请检查 Azure 门户中的操作日志。 这些操作日志应指示操作失败的原因以及限制策略的应用位置。
1. 选择应用策略的资源，然后在 "**策略**" 下选择并编辑该策略，这样就不会受到限制。

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500：同步在一段时间内未完成

### <a name="alert-message"></a>警报消息

*托管域上次与 [date] 上的 Azure AD 同步。用户可能无法登录到托管域或组成员身份可能与 Azure AD 不同步。*

### <a name="resolution"></a>分辨率

[检查 AZURE AD DS 运行状况](check-health.md)，以了解任何指示托管域配置问题的警报。 网络配置问题可能会阻止 Azure AD 的同步。 如果你能够解决指示配置问题的警报，请等待两个小时，然后返回以查看同步是否已成功完成。

以下常见原因导致同步停止在 Azure AD DS 托管域中：

* 所需的网络连接被阻止。 若要详细了解如何在 Azure 虚拟网络中检查问题和所需的内容，请参阅[排查网络安全组](alert-nsg.md)问题以及[Azure AD 域服务的网络要求](network-considerations.md)。
*  部署 Azure AD DS 托管域时，密码同步未设置或已成功完成。 你可以为[仅限云的用户](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)或[混合用户](tutorial-configure-password-hash-sync.md)设置密码同步本地。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501：已在一段时间内未执行备份

### <a name="alert-message"></a>警报消息

*托管域上次于 [date] 进行备份。*

### <a name="resolution"></a>分辨率

[查看 AZURE AD DS 运行状况](check-health.md)，以获取指出托管域配置问题的警报。 网络配置问题可能会阻止 Azure 平台成功地进行备份。 如果你能够解决指示配置问题的警报，请等待两个小时，然后返回以查看同步是否已成功完成。

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503：由于订阅禁用而暂停

### <a name="alert-message"></a>警报消息

*由于与域关联的 Azure 订阅未处于活动状态，托管域已暂停。*

### <a name="resolution"></a>分辨率

> [!WARNING]
> 如果 Azure AD DS 托管域长时间挂起，则会删除该域。 尽快解决挂起的原因。 有关详细信息，请参阅[了解 AZURE AD DS 的挂起状态](suspension.md)。

Azure AD DS 要求使用有效的订阅。 如果 Azure AD DS 托管域关联的 Azure 订阅未处于活动状态，则必须续订该订阅以重新激活订阅。

1. [续订 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 续订订阅后，可以通过 Azure AD DS 通知重新启用托管域。

再次启用托管域时，Azure AD DS 托管域的运行状况将在两小时内自动更新，并删除警报。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504：由于配置无效而暂停

### <a name="alert-message"></a>警报消息

*由于配置无效，托管域被挂起。服务无法长时间管理、修补或更新托管域的域控制器。*

### <a name="resolution"></a>分辨率

> [!WARNING]
> 如果 Azure AD DS 托管域长时间挂起，则会删除该域。 尽快解决挂起的原因。 有关详细信息，请参阅[了解 AZURE AD DS 的挂起状态](suspension.md)。

[查看 AZURE AD DS 运行状况](check-health.md)，以获取指出托管域配置问题的警报。 如果你能够解决指示配置问题的警报，请等待两个小时，然后返回以查看同步是否已完成。 准备就绪后，请[打开 Azure 支持请求][azure-support]，重新启用 Azure AD DS 托管域。

## <a name="next-steps"></a>后续步骤

如果仍有问题，请[提出 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
