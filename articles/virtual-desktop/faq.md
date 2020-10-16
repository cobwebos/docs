---
title: Windows 虚拟桌面常见问题解答-Azure
description: Windows 虚拟桌面的常见问题和最佳实践。
author: Heidilohr
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b915445b74e202f010c5505cc240b6f36e9da77c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108501"
---
# <a name="windows-virtual-desktop-faq"></a>Windows 虚拟桌面常见问题解答

本文回答了一些常见问题，并说明了 Windows 虚拟桌面的最佳实践。

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>管理对象所需的最少管理员权限是什么？

如果要创建主机池和其他对象，则必须在正在使用的订阅或资源组上分配 "参与者" 角色。

你必须为应用组分配 "用户访问管理员" 角色，才能向用户或用户组发布应用组。

若要将管理员限制为仅管理用户会话（如向用户发送消息、注销用户等），你可以创建自定义角色。 例如：

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Windows 虚拟桌面是否支持拆分 Azure Active Directory 模型？

将用户分配到应用组时，服务将执行简单的 Azure 角色分配。 因此，用户的 Azure Active Directory (AD) ，并且应用组的 Azure AD 必须位于同一位置。 所有服务对象，例如主机池、应用组和工作区，也必须与用户在同一 Azure AD 中。

你可以在不同的 Azure AD 中创建 (Vm) 的虚拟机，只要你将 Active Directory 与相同虚拟网络 Azure AD VNET (中的用户) 同步即可。

## <a name="what-are-location-restrictions"></a>什么是位置限制？

所有服务资源都具有与之关联的位置。 主机池的位置确定主机池的服务元数据存储在哪个地理位置。 在没有主机池的情况下，应用组不存在。 如果将应用添加到 RemoteApp 应用组，你还需要一个会话主机来确定 "开始" 菜单应用。 对于任何应用程序组操作，你还需要对主机池具有相关的数据访问权限。 若要确保数据不会在多个位置之间传输，应用组的位置应与主机池的位置相同。

工作区还必须位于其应用组所在的同一位置。 一旦工作区更新，相关应用组就会随之更新。 与应用组类似，服务要求所有工作区都与在同一位置创建的应用组相关联。

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>如何在 PowerShell 中展开对象属性？

运行 PowerShell cmdlet 时，你只会看到资源名称和位置。

例如：

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

若要查看资源的所有属性，请将 `format-list` 或添加 `fl` 到 cmdlet 的末尾。

例如：

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

若要查看特定属性，请在或后添加特定属性名称 `format-list` `fl` 。

例如：

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Windows 虚拟桌面是否支持来宾用户？

Windows 虚拟桌面不支持 Azure AD 来宾用户帐户。 例如，假设一组来宾用户具有 Microsoft 365 E3 每用户、Windows E3 每用户或在其自己的公司中使用了一种 WIN VDA 许可证，但却是不同公司 Azure AD 中的来宾用户。 另一家公司将在 Azure AD 和 Active Directory （如本地帐户）中管理来宾用户的用户对象。

对于第三方权益，不能使用自己的许可证。 此外，Windows 虚拟桌面目前不支持 (MSA) 的 Microsoft 帐户。

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>为什么我看不到 WVDConnections 表中的客户端 IP 地址？

目前尚不提供可靠的方法来收集 web 客户端的 IP 地址，因此，我们不会在表中包括该值。

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Windows 虚拟桌面如何处理备份？

Azure 中有多个选项可用于处理备份。 可以使用 Azure 备份、Site Recovery 和快照。

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Windows 虚拟桌面是否支持第三方协作应用？

Windows 虚拟桌面当前针对团队进行了优化。 Microsoft 目前不支持缩放等第三方协作应用。 第三方组织负责向其客户提供兼容性指导原则。 Windows 虚拟桌面也不支持 Skype for Business。

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>是否可以从共用池更改为个人主机池？

创建主机池后，无法更改其类型。 但是，你可以将注册到主机池的所有 Vm 移到其他类型的主机池。

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>FSLogix 可以处理的最大配置文件大小是多少？

FSLogix 中的限制或配额取决于用于存储用户配置文件 VHD (X) 文件的存储构造。

下表提供了 FSLogix 配置文件支持每个用户所需的任何资源的示例。 根据每个配置文件的用户、应用程序和活动，要求可能会有很大的差异。

| 资源 | 要求 |
|---|---|
| 稳定状态 IOPS | 10 |
| 登录/注销 IOPS | 50 |

此表中的示例是一个用户，但可用于估算环境中用户总数的要求。 例如，对于100用户，需要大约 1000 IOPS，在登录和注销期间大约需要 5000 IOPS。

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>在 Azure 门户中创建的主机池是否存在规模限制？

这些因素可能会影响主机池的规模限制：

- Azure 模板限制为800个对象。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits)。 每个 VM 还会创建大约六个对象，因此，每次运行该模板时，都可以围绕 132 Vm 创建。

- 对于每个区域和每个订阅可以创建的内核数有限制。 例如，如果你有企业协议订阅，则可以创建350核心。 你需要将350除以每个 VM 的默认核心数或你自己的核心限制，以确定每次运行模板时可以创建多少个 Vm。 了解更多 [虚拟机限制-Azure 资源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager)。

- VM 前缀名称和 Vm 数少于15个字符。 若要了解详细信息，请参阅 [Azure 资源的命名规则和限制](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute)。

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>能否通过 Azure Lighthouse 管理 Windows 虚拟桌面环境？

Azure Lighthouse 不完全支持管理 Windows 虚拟桌面环境。 由于 Lighthouse 目前不支持跨 Azure AD 租户用户管理，Lighthouse 客户仍需登录到客户用于管理用户的 Azure AD。

还不能将 CSP 沙盒订阅与 Windows 虚拟桌面服务一起使用。 若要了解详细信息，请参阅 [Integration 沙盒帐户](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account)。

最后，如果你从 CSP 所有者帐户启用了资源提供程序，则 CSP 客户帐户将不能修改资源提供程序。
