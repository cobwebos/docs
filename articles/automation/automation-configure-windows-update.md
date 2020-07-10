---
title: 为 Azure 自动化更新管理配置 Windows 更新设置
description: 本文介绍了如何配置 Windows 更新设置来与 Azure 自动化更新管理配合使用。
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: e30d1606a3928f421d2155e2d1abac0c8a6872aa
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186633"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>为 Azure 自动化更新管理配置 Windows 更新设置

Azure 自动化更新管理依赖 [Windows 更新客户端](/windows/deployment/update/windows-update-overview)来下载和安装 Windows 更新。 有特定的设置，可供 Windows 更新客户端在连接到 Windows Server Update Services (WSUS) 或 Windows 更新时使用。 其中许多设置可以通过以下方式来管理：

- 本地组策略编辑器
- 组策略
- PowerShell
- 直接编辑注册表

更新管理遵循为了控制 Windows 更新客户端而指定的许多设置。 如果你使用设置来启用非 Windows 更新，更新管理也会管理这些更新。 若要在更新部署发生前启用下载更新，更新部署可能会更快、更高效，且不太可能会超出维护时段。

有关在 Azure 订阅中设置 WSUS 并安全地不断更新 Windows 虚拟机的其他建议，请查阅[计划部署以使用 WSUS 在 Azure 中更新 Windows 虚拟机](/azure/architecture/example-scenario/wsus/)。

## <a name="pre-download-updates"></a>预下载更新

若要配置自动下载（而不自动安装）更新，可以使用组策略[将“自动更新”设置配置为](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)“3”。 使用此设置，可以在后台下载所需更新，并通知你更新可供安装。 这样，更新管理仍可控制计划，但允许在更新管理维护时段之外下载更新。 此行为可防止更新管理中出现 `Maintenance window exceeded` 错误。

可以在 PowerShell 中启用此设置：

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>配置重新启动设置

[通过编辑注册表配置自动更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)和[用于管理重启的注册表项](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)中列出的注册表项可能会导致计算机重新启动，即使你在“更新部署”设置中指定了“永不重新启动”，也不例外。 配置这些最适合你环境的注册表项。

## <a name="enable-updates-for-other-microsoft-products"></a>启用其他 Microsoft 产品的更新

默认情况下，Windows 更新客户端配置为，只为 Windows 提供更新。 如果启用“更新 Windows 时提供其他 Microsoft 产品的更新”设置，你还将收到其他产品的更新，包括 Microsoft SQL Server 或其他 Microsoft 软件的安全修补程序。 如果已下载并复制了适用于 Windows 2016 及更高版本的最新[管理模板文件](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)，则可以配置此选项。

如果计算机运行的是 Windows Server 2012 R2，则无法通过组策略来配置此设置。 在这些计算机上，运行以下 PowerShell 命令：

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>进行 WSUS 配置设置

更新管理支持 WSUS 设置。 可以按照[指定 Intranet Microsoft 更新服务位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)中的说明操作，指定用于扫描和下载更新的源。 默认情况下，Windows 更新客户端配置为，从 Windows 更新下载更新。 如果你将 WSUS 服务器指定为计算机的源，但 WSUS 中没有批准更新，则更新部署失败。 

若要将计算机限制为使用内部更新服务，请设置[“不要连接任何 Windows 更新 Internet 位置”](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)。 

## <a name="next-steps"></a>后续步骤

按照[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)中的说明操作来计划更新部署。
