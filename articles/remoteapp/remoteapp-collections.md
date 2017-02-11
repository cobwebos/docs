---
title: "Azure RemoteApp 需要哪种集合？ | Microsoft 文档"
description: "了解适用于 Azure RemoteApp 的集合的类型。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c13ec78d-07e9-4646-8194-cf3efafc1760
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c526faeef2418cfbbca7948004691e127327020


---
# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>Azure RemoteApp 需要哪种集合？
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

借助 Azure RemoteApp，可以与任何设备上的用户共享应用和资源。 为此，我们创建集合来保存应用和资源，然后你与用户共享这些集合。 有 2 个不同的集合选项，具有不同的网络和身份验证选项 - 哪个适合你？

让我们看一下不同的注意事项和需要进行的选择以充分利用你的 Azure RemoteApp 集合。 

## <a name="quick-differences-between-the-collection-types"></a>集合类型之间的快速差异
|  | 云 | 混合 |
| --- | --- | --- |
| 使用现有的 VNET |是 |是 |
| 需要连接 AD 的帐户 (DirSync) |否 |是 |
| 需要域加入 |否 |是 |
| 需要域控制器对于 VNET 可以访问 |否 |是 |

## <a name="cloud-collections"></a>云集合
* 快速创建 - 快速设置集合，这意味着用户可以更快地获取你的应用。
* 提供你自己的应用或共享我们的应用。 可以使用自定义映像（从 Azure VM 中生成的）或你的订阅附带的映像之一。
* 你不需要配置你的集合与你的本地域之间的连接。
* 但是，你可以选择使用你自己的 Azure VNET 提供本地环境中的数据共享访问或使用资源（如 SQL Server）中的非 Windows 身份验证（使用数据库身份验证）。

那么如何创建一个呢？

* 仅云？ 在门户中使用“**快速创建**”选项进行创建。
* 云 + VNET？ 通过“**使用 VNET 创建**”选项进行创建，但不选择加入域。

## <a name="hybrid-collections"></a>混合集合
* 提供对本地网络和 Azure VNET 的完全访问权限。
* 包括应用和数据的域加入访问权限。 远程应用程序可以针对本地 Active Directory 进行身份验证，然后，它们可以访问你的域中的资源。
* 使用现有 System Center 解决方案和 Windows 组策略（通过在 Windows Server 2012 R2 上构建的自定义映像）启用高级监视和管理
* 支持 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 将你的 Azure VNET 连接到你的本地 VNET。

通过“**使用 VNET 创建**”选项进行创建，并选择加入域。

## <a name="authentication-options"></a>身份验证选项
Azure RemoteApp 支持 Microsoft 帐户和 Azure Active Directory 帐户，但并非所有集合都支持所有方法。 

| 帐户类型 |  | 云 | 云 + VNET | 混合 |
| --- | --- | --- | --- | --- |
| Microsoft 帐户 | |是 |是 |否 |
| Azure Active Directory (Azure AD) | | | | |
| 仅 Azure AD |是 |是 |否 | |
| 带密码同步的 AD Connect |是 |是 |是 | |
| 不带密码同步的 AD Connect |是 |是 |否 | |
| 带 AD FS 的 AD Connect |是 |是 |是 | |
| 支持 Azure 的第三方标识提供者（例如 Ping） |是 |是 |是 | |
| Multi-Factor Authentication | |是 |是 |是 |

### <a name="cloud-and-cloud--vnet"></a>云和云 + VNET
通过云集合，可以使用 Microsoft 帐户、Azure AD 帐户或两者的混合形式。 使用最适合你的用户的帐户。

没有使用 Microsoft 帐户的特定要求。 

如果你想要使用 Azure AD 帐户，则需要确保你的 Azure AD 租户与你的订阅相匹配。 创建 Azure RemoteApp 订阅后，所使用的 Azure AD 租户自动与你的订阅相关联。 被授予权限的任何 Azure AD 用户必须是同一租户。 如果需要，你可以[更改 Azure AD 租户](remoteapp-changetenant.md)（该租户与你的订阅相关联）。

### <a name="hybrid-or-cloud--azure-ad--ad"></a>混合（或云 + Azure AD + AD）
使用 Azure AD + 本地 Active Directory 是混合集合的先决条件。 你需要使用 AD Connect 将这两个目录集成。 但是，你可以选择如何配置 AD Connect。 

有 2 种 AD Connect 方案 - 使用密码同步或使用 AD 联合。 请查看 [AD Connect 信息](../active-directory/active-directory-aadconnect.md)以找出最适合你的方案。

还可以将 Azure AD + AD 与云集合结合使用。 请确保遵循相同的设置步骤。

请查看 [Azure RemoteApp 的 Azure AD + Active Directory 要求](remoteapp-ad.md)，了解配置 Azure AD 和 Active Directory 所需的步骤。

## <a name="go-create-your-collection"></a>创建你的集合吧！
好了，我认为我们现在已经都弄明白了，因此，只剩一件事要做 - 创建你的第一个 Azure RemoteApp 集合。

[创建云集合](remoteapp-create-cloud-deployment.md)或[创建混合集合](remoteapp-create-hybrid-deployment.md) - 开始创建吧！




<!--HONumber=Nov16_HO3-->


