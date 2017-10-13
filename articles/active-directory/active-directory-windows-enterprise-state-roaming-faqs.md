---
title: "设置和数据漫游常见问题 | Microsoft Docs"
description: "就 IT 管理员可能会遇到的一些设置和应用数据同步问题提供解答。"
services: active-directory
keywords: "企业状态漫游设置, Windows 云, 企业状态漫游的常见问题解答"
documentationcenter: 
author: tanning
manager: swadhwa
editor: curtand
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 9968d9fa1ebbc92b5647a23c75e75fb819f5d5ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="settings-and-data-roaming-faq"></a>设置和数据漫游常见问题
本主题将解答 IT 管理员可能会遇到的一些设置和应用数据同步问题。

## <a name="what-data-roams"></a>什么样的数据可以漫游？
**Windows 设置**：内置于 Windows 操作系统的电脑设置。 通常，这些是对电脑进行个性化的设置，包括以下三大类：

* 主题，包括桌面主题和任务栏设置等功能。
* Internet Explorer 设置，包括最近打开的选项卡和收藏夹。
* Microsoft Edge 浏览器设置，如收藏夹和读取列表。
* 密码，包括 Internet 密码、Wi-fi 配置文件等。
* 语言首选项，包括键盘布局、系统语言、日期和时间等设置。
* 轻松访问功能，如高对比度主题、讲述人和放大镜。
* 其他 Windows 设置，如命令提示符设置和应用程序列表。

**应用程序数据**：通用 Windows 应用可将设置数据写入漫游文件夹，并且会自动同步写入到此文件夹中的任何数据。 各应用开发人员可根据需要设计应用，以利用此功能。 有关如何开发使用漫游的通用 Windows 应用的详细信息，请参阅[应用数据存储 API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) 和 [Windows 8 应用数据漫游开发人员博客](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)。

## <a name="what-account-is-used-for-settings-sync"></a>哪些帐户可用于设置同步？
在 Windows 8 和 Windows 8.1 中，设置同步始终使用使用者 Microsoft 帐户。 企业用户可以将 Microsoft 帐户连接到其 Active Directory 域帐户，以便获取访问设置同步的权限。在 Windows 10 中，主/辅助帐户框架取代了这一连接的 Microsoft 帐户功能。

主帐户是指用于登录 Windows 的帐户。 可以是 Microsoft 帐户、Azure Active Directory (Azure AD) 帐户、本地 Active Directory 帐户或本地帐户。 除了主帐户外，Windows 10 用户可向设备添加一个或多个辅助云帐户。 辅助帐户通常是 Microsoft 帐户、Azure AD 帐户或某些其他帐户（如 Gmail 或 Facebook）。 这些辅助帐户提供访问其他服务（如单一登录和 Windows 应用商店）的权限，但无法支持设置同步。

在 Windows 10 中，只有设备的主帐户可用于设置同步（请参阅 [如何从 Windows 8 中的 Microsoft 帐户设置同步升级到 Windows 10 中的 Azure AD 设置同步？](active-directory-windows-enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)。

设备上不同用户帐户之间的数据永远不会混合。 设置同步有两个规则：

* Windows 设置始终通过主帐户漫游。
* 通过用于获取应用的帐户来标记应用数据。 仅同步通过主帐户标记的应用。当通过 Windows 应用商店或移动设备管理 (MDM) 边载应用时，会确定应用所有权标记。

如果无法标识应用的所有者，它将通过主帐户漫游。 如果设备从 Windows 8 或 Windows 8.1 升级到 Windows 10，所有应用会被标记为由 Microsoft 帐户获取。 这是因为大多数用户通过 Windows 应用商店获取应用，而在 Windows 10 之前，未对 Azure AD 帐户提供 Windows 应用商店支持。 如果通过离线许可证安装应用，则应用会被标记为在设备上使用主帐户。

> [!NOTE]
> 企业拥有且连接到 Azure AD 的 Windows 10 设备不能再将其 Microsoft 帐户连接到域帐户。 将从已加入连接的 Active Directory 或 Azure AD 环境中的 Windows 10 设备中，删除将 Microsoft 帐户连接到域帐户并将所有用户数据同步到 Microsoft 帐户（即，通过已连接的 Microsoft 帐户和 Active Directory 功能进行的 Microsoft 帐户漫游）这一功能。
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>如何从 Windows 8 中的 Microsoft 帐户设置同步升级到 Windows 10 中的 Azure AD 设置同步？
如果已通过连接的 Microsoft 帐户加入到运行 Windows 8 或 Windows 8.1 的 Active Directory 域，则将通过 Microsoft 帐户来同步设置。 升级到 Windows 10 后，只要是已加入域的用户且 Active Directory 域未与 Azure AD 连接，就将继续通过 Microsoft 帐户来同步用户设置。

如果本地 Active Directory 域与 Azure AD 连接，设备将尝试使用连接的 Azure AD 帐户来同步设置。 如果 Azure AD 管理员没有启用企业状态漫游，则连接的 Azure AD 帐户将停止同步设置。 如果是 Windows 10 用户并且使用 Azure AD 标识登录，则管理员通过 Azure AD 启用设置同步时，就能立即启动 Windows 设置。

如果在公司设备上存储了任何个人数据，应注意 Windows OS 和应用程序数据将开始同步到 Azure AD。 这具有以下含义：

* 个人 Microsoft 帐户设置将与 Azure AD 工作或学校帐户设置存在偏差。 这是因为 Microsoft 帐户和 Azure AD 设置同步现在使用单独的帐户。
* 以前通过连接的 Microsoft 帐户进行同步的个人数据（如 Wi-fi 密码、Web 凭据和 Internet Explorer 收藏夹）将通过 Azure AD 同步。

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Microsoft 帐户和 Azure AD 企业状态漫游互操作性如何工作？
在 Windows 10 的 2015 年 11 月版本或更高版本中，一次仅对一个帐户支持企业状态漫游。 如果使用 Azure AD 工作或学校帐户登录到 Windows，则所有数据通过 Azure AD 同步。 如果使用个人 Microsoft 帐户登录到 Windows，则所有数据将通过 Microsoft 帐户同步。 通用应用数据仅通过设备上的主登录帐户进行漫游，且仅当该主帐户拥有应用许可证时才能漫游。 不会对任何辅助帐户拥有的应用的通用应用数据进行同步。

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>是否对来自多个租户的 Azure AD 帐户进行设置同步？
当同一设备上有来自不同 Azure AD 租户的多个 Azure AD 帐户时，必须更新设备的注册表，才可与每个 Azure AD 租户的 Azure 权限管理 (Azure RMS) 进行通信。  

1. 为每个 Azure AD 租户查找 GUID。 打开 Azure 经典门户并选择 Azure AD 租户。 租户的 GUID 位于浏览器地址栏的 URL 中。 例如： `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. 获取 GUID 后，需要添加注册表项 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>**。
   从“租户 ID GUID”键中，新建名为 **AllowedRMSServerUrls** 的多字符串值 (REG-MULTI-SZ)。 对于其数据，指定设备访问的其他 Azure 租户的授权分发点 URL。
3. 可以通过运行 **Get-AadrmConfiguration** cmdlet 找到授权分发点 URL。 如果 **LicensingIntranetDistributionPointUrl** 和 **LicensingExtranetDistributionPointUrl** 的值不同，则指定这两个值。 如果值相同，则指定该值一次。

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>现有 Windows 桌面应用程序的漫游设置选项有哪些？
漫游仅适用于通用 Windows 应用。 有两种选项可用于在现有 Windows 桌面应用程序上启用漫游：

* [桌面桥](http://aka.ms/desktopbridge)有助于将现有的 Windows 桌面应用引入到通用 Windows 平台。 在这里，只需要最少的代码更改，就可以利用 Azure AD 应用数据漫游。 桌面桥为应用提供应用标识，需要该标识才可为现有桌面应用启用应用数据漫游。
* [用户体验虚拟化 (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) 有助于为现有 Windows 桌面应用创建自定义设置模板，并为 Win32 应用启用漫游。 此选项不需要应用程发人员更改应用代码。 UE-V 仅限于已购买 Microsoft Desktop Optimization Pack 的客户的本地 Active Directory 漫游。

通过 [UE-V 组策略](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)更改 Windows OS 设置和通用应用程序数据的漫游，管理员可以配置 UE-V 来漫游 Windows 桌面应用。组策略包括：

* “漫游 Windows 设置”组策略
* “不同步 Windows 应用”组策略
* 应用程序部分中的 Internet Explorer 漫游

将来，Microsoft 可能会探索如何将 UE-V 深度集成到 Windows，以及如何通过 Azure AD 云将 UE-V 扩展为漫游设置。

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>是否可以本地存储已同步的设置和数据？
企业状态漫游将所有已同步的数据存储在 Azure 云中。 UE-V 提供本地漫游解决方案。

## <a name="who-owns-the-data-thats-being-roamed"></a>谁拥有正在进行漫游的数据？
企业拥有通过企业状态漫游进行漫游的数据。 数据存储在 Azure 数据中心。 使用 Azure RMS 对所有正在传输中和云中静态的用户数据进行加密。 与基于 Microsoft 帐户的设置同步（仅在离开设备之前加密某些敏感数据，如用户凭据）相比，这是一个进步。

Microsoft 致力于保护客户数据。 企业用户的设置数据离开 Windows 10 设备前，会自动由 Azure RMS 进行加密，从而让其他用户无法读取此数据。 如果组织拥有 Azure RMS 的付费订阅，则可以使用其他 Azure RMS 功能，例如跟踪和撤销文档、自动保护包含敏感信息的电子邮件以及管理自己的密钥（“自带密钥”解决方案，也称为 BYOK）。 有关这些功能和 Azure RMS 工作原理的详细信息，请参阅 [Azure 权限管理是什么](https://technet.microsoft.com/jj585026.aspx)。

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>是否可以管理特定应用或设置的同步？
在 Windows 10 中，没有用于禁用单个应用程序漫游的 MDM 或组策略设置。 租户管理员可以在托管设备上禁用所有应用的应用数据同步，但在每个应用或应用内级别没有更精细的控制。

## <a name="how-can-i-enable-or-disable-roaming"></a>如何启用或禁用漫游？
在“设置”应用中，转到“帐户” > “同步设置”。 可在此页看到正在使用哪个帐户漫游设置，并可以启用或禁用要进行漫游的各设置组。

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>对于在 Windows 10 中启用漫游，Microsoft 有何建议？
Microsoft 提供几种不同的设置漫游解决方案，包括漫游用户配置文件、UE-V 和企业状态漫游。  Microsoft 致力于在未来版本的 Windows 中投资企业状态漫游。 如果组织尚未准备就绪或不想将数据移到云中，那么我们建议使用 UE-V 作为主漫游技术。 如果组织需要针对现有 Windows 桌面应用程序的漫游支持，但又很想移动到云中，我们建议同时使用企业状态漫游和 UE-V。 尽管 UE-V 和企业状态漫游是非常相似的技术，但它们并不互斥。 它们相辅相成，帮助组织提供用户所需的漫游服务。  

同时使用企业状态漫游和 UE-V 时，以下规则适用：

* 企业状态漫游是设备上的主漫游代理。 UE-V 用于填补“Win32 差距”。
* 当使用 UE-V 组策略时，应对 Windows 设置和现代 UWP 应用数据禁用 UE-V 漫游。 企业状态漫游已涵盖了这些漫游。

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>企业状态漫游如何支持虚拟桌面基础结构 (VDI)？
Windows 10 客户端 SKU 支持企业状态漫游，但服务器 SKU 不支持。 如果客户端 VM 托管在虚拟机监控程序计算机上，并远程登录到虚拟机，数据将漫游。 如果多个用户共享相同的操作系统，且用户远程登录到服务器以获取完整的桌面体验，漫游可能无法工作。 不正式支持后一种基于会话的方案。

## <a name="what-happens-when-my-organization-purchases-azure-rms-after-using-roaming"></a>组织在使用漫游后购买 Azure RMS 会发生什么情况？
如果组织已在 Windows 10 中使用漫游（通过 Azure RMS 使用受限的免费订阅），购买付费 Azure RMS 订阅不会对漫游功能产生任何影响，且无需 IT 管理员对配置进行任何更改。

## <a name="known-issues"></a>已知问题
请参阅[疑难解答](active-directory-windows-enterprise-state-roaming-troubleshooting.md)一节中的文档，查看已知问题列表。 

## <a name="related-topics"></a>相关主题
* [企业状态漫游概述](active-directory-windows-enterprise-state-roaming-overview.md)
* [在 Azure Active Directory 中启用企业状态漫游](active-directory-windows-enterprise-state-roaming-enable.md)
* [设置同步的组策略和 MDM 设置](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 漫游设置参考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [故障排除](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
