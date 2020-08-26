---
title: Windows 虚拟桌面中有哪些新增功能？ - Azure
description: Windows 虚拟桌面的新增功能和产品更新。
author: Heidilohr
ms.topic: overview
ms.date: 08/07/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 8663fc3f016fadcd4f4c99acd800cd0ccf8844f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003528"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows 虚拟桌面中有哪些新增功能？

Windows 虚拟桌面会定期更新。 本文介绍了以下内容：

- 最新更新
- 新增功能
- 对现有功能的改进
- Bug 修复

本文每月更新一次。 请务必时常查看本文，以及时了解最新更新。

## <a name="july-2020"></a>2020 年 7 月  

7 月，Windows 虚拟桌面与 Azure 资源管理的集成正式发布。

以下是此新版本中所做的更改： 

- “2019 年秋季版本”现在称为“Windows 虚拟桌面（经典版）”，而“2020 年春季版本”现在称为“Windows 虚拟桌面”。 有关详细信息，请查看[此博客文章](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)。 

要详细了解新功能，请查看[此博客文章](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)。 

### <a name="autoscaling-tool-update"></a>自动缩放工具更新

自动缩放工具的最新版本（之前处于预览状态）现已正式发布。 此工具使用 Azure 自动化帐户和 Azure 逻辑应用来自动关闭和重新启动主机池中的会话主机虚拟机 (VM)，从而降低基础结构成本。 有关详细信息，请参阅[使用 Azure 自动化来改变会话主机规模](set-up-scaling-script.md)。

### <a name="azure-portal"></a>Azure 门户

现在，你可以在 Windows 虚拟桌面中使用 Azure 门户执行以下操作： 

- 直接将用户分配给个人桌面会话主机  
- 更改主机池的验证环境设置 

### <a name="diagnostics"></a>诊断

我们为 Log Analytics 工作区发布了一些新的预构建查询。 要访问这些查询，请转到“类别”下的“日志”，选择“Windows 虚拟桌面”  。 有关详细信息，请参阅[将 Log Analytics 用于诊断功能](diagnostics-log-analytics.md)。

### <a name="update-for-remote-desktop-client-for-android"></a>适用于 Android 的远程桌面客户端更新

[适用于 Android 的远程桌面客户端](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)现支持 Windows 虚拟桌面连接。 从版本 10.0.7 开始，Android 客户端采用新的 UI，可改进用户体验。 客户端还与 Android 设备上的 Microsoft Authenticator 集成，以在订阅 Windows 虚拟桌面工作区时启用条件访问。  

早期版本的远程桌面客户端现在称为“远程桌面 8”。 客户端早期版本中的任何现有连接都将无缝转移到新客户端。 新客户端已重写为使用与 iOS 和 macOS 客户端相同的基础 RDP 核心引擎，可跨所有平台更快地发布新功能。 

### <a name="teams-update"></a>Teams 更新

我们对适用于 Windows 虚拟桌面的 Microsoft Teams 进行了改进。 最重要的是，Windows 虚拟桌面现在支持适用于 Windows 桌面客户端的音频和视频优化。 用户使用音频或视频进行通话和会议时，重定向可在用户之间创建直接路径，从而改善延迟。 距离越短意味着跃点越少，因此通话的视频和音频都会更流畅。 有关详细信息，请参阅[在 Windows 虚拟桌面上使用 Teams](teams-on-wvd.md)。

## <a name="june-2020"></a>2020 年 6 月

在上一个月，我们引入了带 Azure 资源管理器集成的 Windows 虚拟桌面预览版。 此更新有许多令人兴奋的新增功能，我们很乐意向你介绍。 下面是此 Windows 虚拟桌面版本的新增功能。

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows 虚拟桌面现已与 Azure 资源管理器集成

Windows 虚拟桌面现已集成到 Azure 资源管理器中。 在最新更新中，所有 Windows 虚拟桌面对象现均为 Azure 资源管理器资源。 此更新还集成了 Azure 基于角色的访问控制 (Azure RBAC)。 请参阅[什么是 Azure 资源管理器？](../azure-resource-manager/management/overview.md)以了解详细信息。

下面是此更改的作用：

- Windows 虚拟桌面现已与 Azure 门户集成。 这意味着可以直接在门户中管理所有内容，而无需 PowerShell、Web 应用或第三方工具。 首先，请查看教程：[使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

- 在进行此更新之前，只能将 RemoteApp 和桌面应用发布到独立用户。 使用 Azure 资源管理器，现在可以将资源发布到 Azure Active Directory 组。

- Windows 虚拟桌面的早期版本具有四个内置管理员角色，你可以将其分配给租户或主机池。 这些角色现在受限于 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。 可以将这些角色应用于每个 Windows 虚拟桌面 Azure资源管理器对象，从而拥有完整而丰富的委派模型。

- 在此更新中，不再需要重复运行 Azure 市场或 GitHub 模板来扩展主机池。 要扩展主机池，只需在 Azure 门户中转到该主机池，然后选择“+ 添加”以部署其他会话主机即可。

- 主机池部署现已与 [Azure 共享映像库](../virtual-machines/windows/shared-image-galleries.md)完全集成。 共享映像库是一项单独的 Azure 服务，用于存储虚拟机 (VM) 映像定义，包括映像版本控制。 还可以使用全球复制将映像复制并发送到其他 Azure 区域，以进行本地部署。

- 过去通过 PowerShell 或诊断服务 Web 应用完成的监视功能现已移至 Azure 门户中的 Log Analytics。 现在还可以通过两个选项可视化报表。 可以运行 Kusto 查询并使用工作簿创建视觉对象报表。

- 不再需要完成 Azure Active Directory (Azure AD) 同意即可使用 Windows 虚拟桌面。 在此更新中，Azure 订阅上的 Azure AD 租户会对用户进行身份验证，并为管理员提供 Azure RBAC 控制权限。


### <a name="powershell-support"></a>PowerShell 支持

我们已在此更新中向 Azure PowerShell Az 模块添加了新的 AzWvd cmdlet。 在 .NET Core 上运行的 PowerShell Core 支持此新模块。

要安装该模块，请按照[为 Windows 虚拟桌面设置 PowerShell 模块](powershell-module.md)中的说明操作。

还可以在 [AzWvd PowerShell 参考](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization)处查看可用命令的列表。

有关新增功能的详细信息，请参阅[我们的博客文章](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)。

### <a name="additional-gateways"></a>其他网关

我们在南非添加了新的网关群集，以降低连接延迟。

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Windows 虚拟桌面上的 Microsoft Teams（预览版）

我们对适用于 Windows 虚拟桌面的 Microsoft Teams 进行了一些改进。 最重要的是，Windows 虚拟桌面现在支持通话音频和视觉对象重定向。 用户使用音频或视频进行通话时，重定向可在用户之间创建直接路径，从而改善延迟。 距离越短意味着跃点越少，因此通话的视频和音频都会更流畅。

要了解详细信息，请参阅[我们的博客文章](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)。

## <a name="next-steps"></a>后续步骤

请在 [Microsoft 365 Windows 虚拟桌面路线图](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)了解未来的计划。

要了解 Windows 虚拟桌面和远程桌面服务的客户端更新，请参阅以下文章：

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
