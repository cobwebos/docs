---
title: Windows 虚拟桌面中有哪些新增功能？ - Azure
description: Windows 虚拟桌面的新增功能和产品更新。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 7052e9203532320a7de5197e983d40cb0a34b50d
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374774"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows 虚拟桌面中有哪些新增功能？

Windows 虚拟桌面会定期更新。 本文介绍了以下内容：

- 最新更新
- 新增功能
- 对现有功能的改进
- Bug 修复

本文每月更新一次。 请务必时常查看本文，以及时了解最新更新。

## <a name="june-2020"></a>2020 年 6 月

上个月，我们介绍了 Windows 虚拟桌面 2020 年春季更新预览。 此更新有许多令人兴奋的新增功能，我们很乐意向你介绍。 下面是 2020 年春季更新的新增功能。

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>Windows 虚拟桌面现已与 Azure 资源管理器集成（预览版）

Windows 虚拟桌面现已集成到 Azure 资源管理器中。 在最新更新中，所有 Windows 虚拟桌面对象现均为 Azure 资源管理器资源。 此更新还集成了 Azure 基于角色的访问控制 (RBAC)。 请参阅[什么是 Azure 资源管理器？](../azure-resource-manager/management/overview.md)以了解详细信息。

下面是此更改的作用：

- Windows 虚拟桌面现已与 Azure 门户集成。 这意味着可以直接在门户中管理所有内容，而无需 PowerShell、Web 应用或第三方工具。 首先，请查看教程：[使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

- 在 2020 年春季更新之前，只能将 RemoteApp 和桌面应用发布到独立用户。 使用 Azure 资源管理器，现在可以将资源发布到 Azure Active Directory 组。

- Windows 虚拟桌面的早期版本具有四个内置管理员角色，你可以将其分配给租户或主机池。 这些角色现在受限于 Azure [基于角色的访问控制](../role-based-access-control/overview.md)。 可以将这些角色应用于每个 Windows 虚拟桌面 Azure资源管理器对象，从而拥有完整而丰富的委派模型。

- 在 2020 年春季更新中，不再需要重复运行 Azure 市场或 GitHub 模板来扩展主机池。 要扩展主机池，只需在 Azure 门户中转到该主机池，然后选择“+ 添加”以部署其他会话主机即可。

- 主机池部署现已与 [Azure 共享映像库](../virtual-machines/windows/shared-image-galleries.md)完全集成。 共享映像库是一项单独的 Azure 服务，用于存储虚拟机 (VM) 映像定义，包括映像版本控制。 还可以使用全球复制将映像复制并发送到其他 Azure 区域，以进行本地部署。

- 过去通过 PowerShell 或诊断服务 Web 应用完成的监视功能现已移至 Azure 门户中的 Log Analytics。 现在还可以通过两个选项可视化报表。 可以运行 Kusto 查询并使用工作簿创建视觉对象报表。

- 不再需要完成 Azure Active Directory (Azure AD) 同意即可使用 Windows 虚拟桌面。 在 2020 年春季更新中，Azure 订阅上的 Azure AD 租户会对用户进行身份验证，并为管理员提供 RBAC 控制权限。


### <a name="powershell-support"></a>PowerShell 支持

我们已在 2020 年春季更新中向 Azure PowerShell Az 模块添加了新的 AzWvd cmdlet。 在 .NET Core 上运行的 PowerShell Core 支持此新模块。

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
