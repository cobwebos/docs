---
title: Azure AD 域服务中的常见警报和解决方法 |微软文档
description: 了解如何解决作为 Azure 活动目录域服务运行状况的一部分生成的常见警报
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: ed791ea10c072308c16baac9fa469a46b19ec648
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654498"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>已知问题：Azure 活动目录域服务中的常见警报和解决方法

作为应用程序标识和身份验证的核心部分，Azure 活动目录域服务 （Azure AD DS） 有时存在问题。 如果遇到问题，有一些常见的警报和相关故障排除步骤，以帮助您重新运行操作。 您可以随时[打开 Azure 支持请求][azure-support]，以获取其他故障排除帮助。

本文提供有关 Azure AD DS 中常见警报的故障排除信息。

## <a name="aadds100-missing-directory"></a>AADDS100：缺少目录

### <a name="alert-message"></a>警报消息

*与托管域关联的 Azure AD 目录可能已被删除。托管域不再处于受支持的配置中。Microsoft 无法监视、管理、修补和同步托管域。*

### <a name="resolution"></a>解决方法

当 Azure 订阅移动到新的 Azure AD 目录并删除与 Azure AD DS 关联的旧 Azure AD 目录时，通常会出现此错误。

此错误无法恢复。 要解决警报，请[删除现有的 Azure AD DS 托管域](delete-aadds.md)，并在新目录中重新创建它。 如果在删除 Azure AD DS 托管域时遇到问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101：Azure AD B2C 在此目录中运行

### <a name="alert-message"></a>警报消息

** 无法在 Azure AD B2C 目录中启用 Azure AD 域服务。

### <a name="resolution"></a>解决方法

Azure AD DS 会自动与 Azure AD 目录同步。 如果 Azure AD 目录配置为 B2C，则无法部署和同步 Azure AD DS。

要使用 Azure AD DS，必须使用以下步骤在非 Azure AD B2C 目录中重新创建托管域：

1. 从现有 Azure AD 目录[中删除 Azure AD DS 托管域](delete-aadds.md)。
1. 创建新的 Azure AD 目录，该目录不是 Azure AD B2C 目录。
1. [创建替换 Azure AD DS 托管域](tutorial-create-instance.md)。

Azure AD DS 托管域的运行状况会在两小时内自动更新自身并删除警报。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103：地址在公共 IP 范围内

### <a name="alert-message"></a>警报消息

*启用 Azure AD 域服务的虚拟网络的 IP 地址范围在公共 IP 范围内。必须在具有专用 IP 地址范围的虚拟网络中启用 Azure AD 域服务。此配置会影响 Microsoft 监视、管理、修补和同步托管域的能力。*

### <a name="resolution"></a>解决方法

开始之前，请确保您了解专用 IP [v4 地址空间](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)。

在虚拟网络中，VM 可以向 Azure 资源发出与为子网配置的 IP 地址范围相同的请求。 如果为子网配置公共 IP 地址范围，则虚拟网络中路由的请求可能无法到达预期的 Web 资源。 此配置可能导致 Azure AD DS 的不可预知错误。

> [!NOTE]
> 如果你在 Internet 中拥有为虚拟网络配置的 IP 地址范围，则可以忽略此警报。 但是，Azure AD 域服务无法在此配置下提交到[SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)*，因为它可能导致不可预知的错误。

要解决此警报，请删除现有的 Azure AD DS 托管域，并在具有专用 IP 地址范围的虚拟网络中重新创建该域。 此过程具有破坏性，因为 Azure AD DS 托管域不可用，并且您创建的任何自定义资源（如 O 或服务帐户）都丢失。

1. 从目录中[删除 Azure AD DS 托管域](delete-aadds.md)。
1. 要更新虚拟网络 IP 地址范围，请在 Azure 门户中搜索并选择*虚拟网络*。 为 Azure AD DS 选择不正确的公共 IP 地址范围集的虚拟网络。
1. 在 **"设置"** 下，选择 *"地址空间*"。
1. 通过选择现有地址范围并编辑地址范围或添加其他地址范围来更新地址范围。 确保新的 IP 地址范围在专用 IP 范围内。 准备就绪后，**保存**更改。
1. 在左侧导航中选择**子网**。
1. 选择要编辑的子网，或创建其他子网。
1. 更新或指定专用 IP 地址范围，然后**保存**更改。
1. [创建替换 Azure AD DS 托管域](tutorial-create-instance.md)。 请确保选择具有专用 IP 地址范围的更新虚拟网络子网。

Azure AD DS 托管域的运行状况会在两小时内自动更新自身并删除警报。

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: 找不到 Azure 订阅

### <a name="alert-message"></a>警报消息

*与托管域关联的 Azure 订阅已被删除。 Azure AD 域服务需要活动订阅才能继续正常运行。*

### <a name="resolution"></a>解决方法

Azure AD DS 需要活动订阅，不能移动到其他订阅。 如果删除与 Azure AD DS 托管域关联的 Azure 订阅，则必须重新创建 Azure 订阅和 Azure AD DS 托管域。

1. [创建 Azure 订阅](../cost-management-billing/manage/create-subscription.md)。
1. 从现有 Azure AD 目录[中删除 Azure AD DS 托管域](delete-aadds.md)。
1. [创建替换 Azure AD DS 托管域](tutorial-create-instance.md)。

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Azure 订阅已禁用

### <a name="alert-message"></a>警报消息

*与托管域关联的 Azure 订阅不处于活动状态。 Azure AD 域服务需要活动订阅才能继续正常运行。*

### <a name="resolution"></a>解决方法

Azure AD DS 需要活动订阅。 如果 Azure AD DS 托管域关联的 Azure 订阅未处于活动状态，则必须续订该订阅以重新激活订阅。

1. [续订 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 续订订阅后，Azure AD DS 通知允许您重新启用托管域。

再次启用托管域后，Azure AD DS 托管域的运行状况会在两小时内自动更新并删除警报。

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: 在目录中移动了订阅

### <a name="alert-message"></a>警报消息

*Azure AD 域服务使用的订阅已移动到另一个目录。Azure AD 域服务需要在同一目录中具有活动订阅才能正常运行。*

### <a name="resolution"></a>解决方法

Azure AD DS 需要活动订阅，不能移动到其他订阅。 如果移动与 Azure AD DS 托管域关联的 Azure 订阅，请将订阅移回以前的目录，或从现有目录[中删除托管域](delete-aadds.md)，并在[所选订阅中创建替换的 Azure AD DS 托管域](tutorial-create-instance.md)。

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: 找不到托管域的资源

### <a name="alert-message"></a>警报消息

*用于托管域的资源已被删除。Azure AD 域服务需要此资源才能正常运行。*

### <a name="resolution"></a>解决方法

Azure AD DS 会创建其他资源以正常运行，例如公共 IP 地址、虚拟网络接口和负载均衡器。 如果删除这些资源中的任何一个，则托管域处于不受支持的状态，并阻止管理域。 有关这些资源的详细信息，请参阅 Azure [AD DS 使用的网络资源](network-considerations.md#network-resources-used-by-azure-ad-ds)。

删除这些必需资源之一时，将生成此警报。 如果资源在不到 4 小时前被删除，Azure 平台可能会自动重新创建已删除的资源。 以下步骤概述了如何检查运行状况和时间戳以进行资源删除：

1. 在 Azure 门户中，搜索并选择**域服务**。 选择 Azure AD DS 托管域，如*aaddscontoso.com*。
1. 在左侧导航中，选择 **"运行状况**"。
1. 在运行状况页中，选择 ID *AADDS109*的警报。
1. 警报具有首次发现警报的时间戳。 如果时间戳小于 4 小时，Azure 平台可能能够自动重新创建资源并自行解决警报。

    如果警报超过 4 小时，则 Azure AD DS 托管域处于不可恢复状态。 [删除 Azure AD DS 托管域](delete-aadds.md)，然后[创建替换托管域](tutorial-create-instance.md)。

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: 与托管域关联的子网已满

### <a name="alert-message"></a>警报消息

选择用于部署 Azure AD 域服务的子网已满，没有空间用于保留需要创建的其他域控制器。**

### <a name="resolution"></a>解决方法

Azure AD DS 的虚拟网络子网需要自动创建的资源的足够 IP 地址。 此 IP 地址空间包括在发生维护事件时需要创建替换资源。 为了将可用 IP 地址耗尽的风险降至最低，请不要将其他资源（如您自己的 VM）部署到与 Azure AD DS 相同的虚拟网络子网中。

此错误无法恢复。 要解决警报，[请删除现有的 Azure AD DS 托管域](delete-aadds.md)并重新创建它。 如果在删除 Azure AD DS 托管域时遇到问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111： 服务主体未经授权

### <a name="alert-message"></a>警报消息

*Azure AD 域服务用于为域提供服务的服务主体无权管理 Azure 订阅上的资源。服务主体需要获得服务托管域的权限。*

### <a name="resolution"></a>解决方法

某些自动生成的服务主体用于管理和创建 Azure AD DS 托管域的资源。 如果更改了这些服务主体之一的访问权限，则域无法正确管理资源。 以下步骤演示如何理解服务主体，然后授予服务主体的访问权限：

1. 阅读有关[基于角色的访问控制以及如何授予 Azure 门户中应用程序访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。
2. 查看具有 ID *abba844e-bc0e-44b0-947a-dc74e5d09022*的服务主体具有的访问，并授予较早日期被拒绝的访问。

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: 托管域中没有足够的 IP 地址

### <a name="alert-message"></a>警报消息

*我们已确定此域中虚拟网络的子网可能没有足够的 IP 地址。Azure AD 域服务在在其启用的子网中至少需要两个可用的 IP 地址。我们建议在子网中至少拥有 3-5 个备用 IP 地址。如果其他虚拟机部署在子网中，从而耗尽可用 IP 地址的数量，或者子网中的可用 IP 地址数量受到限制，则可能发生这种情况。*

### <a name="resolution"></a>解决方法

Azure AD DS 的虚拟网络子网需要足够的 IP 地址来用于自动创建的资源。 此 IP 地址空间包括在发生维护事件时需要创建替换资源。 为了将可用 IP 地址耗尽的风险降至最低，请不要将其他资源（如您自己的 VM）部署到与 Azure AD DS 相同的虚拟网络子网中。

要解决此警报，请删除现有的 Azure AD DS 托管域，并在具有足够大 IP 地址范围的虚拟网络中重新创建它。 此过程具有破坏性，因为 Azure AD DS 托管域不可用，并且您创建的任何自定义资源（如 O 或服务帐户）都丢失。

1. 从目录中[删除 Azure AD DS 托管域](delete-aadds.md)。
1. 要更新虚拟网络 IP 地址范围，请在 Azure 门户中搜索并选择*虚拟网络*。 为具有小 IP 地址范围的 Azure AD DS 选择虚拟网络。
1. 在 **"设置"** 下，选择 *"地址空间*"。
1. 通过选择现有地址范围并编辑地址范围或添加其他地址范围来更新地址范围。 确保新的 IP 地址范围足够大，适合 Azure AD DS 子网范围。 准备就绪后，**保存**更改。
1. 在左侧导航中选择**子网**。
1. 选择要编辑的子网，或创建其他子网。
1. 更新或指定足够大的 IP 地址范围，然后**保存**更改。
1. [创建替换 Azure AD DS 托管域](tutorial-create-instance.md)。 请确保选择具有足够大 IP 地址范围的更新虚拟网络子网。

Azure AD DS 托管域的运行状况会在两小时内自动更新自身并删除警报。

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: 资源不可恢复

### <a name="alert-message"></a>警报消息

检测到 Azure AD 域服务使用的资源处于意外状态，且无法恢复。**

### <a name="resolution"></a>解决方法

此错误无法恢复。 要解决警报，[请删除现有的 Azure AD DS 托管域](delete-aadds.md)并重新创建它。 如果在删除 Azure AD DS 托管域时遇到问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

## <a name="aadds114-subnet-invalid"></a>AADDS114: 子网无效

### <a name="alert-message"></a>警报消息

*为部署 Azure AD 域服务而选择的子网无效，无法使用。*

### <a name="resolution"></a>解决方法

此错误无法恢复。 要解决警报，[请删除现有的 Azure AD DS 托管域](delete-aadds.md)并重新创建它。 如果在删除 Azure AD DS 托管域时遇到问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

## <a name="aadds115-resources-are-locked"></a>AADDS115: 资源已锁定

### <a name="alert-message"></a>警报消息

由于目标范围已锁定，托管域使用的一个或多个网络资源无法运行。**

### <a name="resolution"></a>解决方法

资源锁可以应用于 Azure 资源，以防止更改或删除。 由于 Azure AD DS 是托管服务，Azure 平台需要进行配置更改的能力。 如果在某些 Azure AD DS 组件上应用了资源锁，则 Azure 平台无法执行其管理任务。

要检查 Azure AD DS 组件上的资源锁并删除它们，请完成以下步骤：

1. 对于资源组中的每个 Azure AD DS 网络组件（如虚拟网络、网络接口或公共 IP 地址），请检查 Azure 门户中的操作日志。 这些操作日志应指示操作失败的原因以及应用资源锁的位置。
1. 选择应用锁的资源，然后在 **"锁"** 下选择并删除锁。

## <a name="aadds116-resources-are-unusable"></a>AADDS116: 资源不可用

### <a name="alert-message"></a>警报消息

由于策略限制，托管域使用的一个或多个网络资源无法运行。**

### <a name="resolution"></a>解决方法

策略应用于 Azure 资源和资源组，这些资源和资源组控制允许的配置操作。 由于 Azure AD DS 是托管服务，Azure 平台需要进行配置更改的能力。 如果在某些 Azure AD DS 组件上应用了策略，则 Azure 平台可能无法执行其管理任务。

要检查 Azure AD DS 组件上应用的策略并更新它们，请完成以下步骤：

1. 对于资源组中的每个 Azure AD DS 网络组件（如虚拟网络、NIC 或公共 IP 地址），请检查 Azure 门户中的操作日志。 这些操作日志应指示操作失败的原因以及应用限制性策略的位置。
1. 选择应用策略的资源，然后在 **"策略"** 下选择和编辑策略，使其限制更少。

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500：同步在一段时间内未完成

### <a name="alert-message"></a>警报消息

*托管域上次与 Azure AD 同步是在 [日期]。用户可能无法在托管域上登录，或者组成员身份可能与 Azure AD 不同步。*

### <a name="resolution"></a>解决方法

[检查 Azure AD DS 运行状况](check-health.md)中是否有任何警报指示托管域配置中的问题。 网络配置问题可能会阻止 Azure AD 的同步。 如果能够解决指示配置问题的警报，请等待两个小时并返回以查看同步是否成功完成。

以下常见原因会导致同步在 Azure AD DS 托管域中停止：

* 所需的网络连接被阻止。 要了解有关如何检查 Azure 虚拟网络的问题以及所需内容的更多信息，请参阅[对网络安全组](alert-nsg.md)和[Azure AD 域服务的网络要求](network-considerations.md)进行故障排除。
*  部署 Azure AD DS 托管域时，未设置或成功完成密码同步。 您可以为[仅云用户](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)或来自[onprem 的混合用户](tutorial-configure-password-hash-sync.md)设置密码同步。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501：已在一段时间内未执行备份

### <a name="alert-message"></a>警报消息

*托管域上次于 [date] 进行备份。*

### <a name="resolution"></a>解决方法

[检查 Azure AD DS 运行状况](check-health.md)中存在指示托管域配置中出现问题的警报。 网络配置问题可能会阻止 Azure 平台成功执行备份。 如果能够解决指示配置问题的警报，请等待两个小时并返回以查看同步是否成功完成。

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503：由于订阅禁用而暂停

### <a name="alert-message"></a>警报消息

*由于与域关联的 Azure 订阅未处于活动状态，托管域已暂停。*

### <a name="resolution"></a>解决方法

> [!WARNING]
> 如果 Azure AD DS 托管域被挂起很长时间，则有将其删除的危险。 尽快解决挂起的原因。 有关详细信息，请参阅了解[Azure AD DS 的挂起状态](suspension.md)。

Azure AD DS 需要活动订阅。 如果 Azure AD DS 托管域关联的 Azure 订阅未处于活动状态，则必须续订该订阅以重新激活订阅。

1. [续订 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 续订订阅后，Azure AD DS 通知允许您重新启用托管域。

再次启用托管域后，Azure AD DS 托管域的运行状况会在两小时内自动更新并删除警报。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504：由于配置无效而暂停

### <a name="alert-message"></a>警报消息

*托管域由于配置无效而挂起。长期以来，该服务无法管理、修补或更新托管域的域控制器。*

### <a name="resolution"></a>解决方法

> [!WARNING]
> 如果 Azure AD DS 托管域被挂起很长时间，则有将其删除的危险。 尽快解决挂起的原因。 有关详细信息，请参阅了解[Azure AD DS 的挂起状态](suspension.md)。

[检查 Azure AD DS 运行状况](check-health.md)中存在指示托管域配置中出现问题的警报。 如果能够解决指示配置问题的警报，请等待两个小时，然后检查以查看同步是否已完成。 准备就绪后，[打开 Azure 支持请求][azure-support]以重新启用 Azure AD DS 托管域。

## <a name="next-steps"></a>后续步骤

如果仍有问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
