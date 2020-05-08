---
title: 将 Windows 更新设置配置为使用 Azure 更新管理
description: 本文介绍配置为使用 Azure 更新管理的 Windows 更新设置。
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: b9b5f2b19b29eae0132ec01a9f3fb7e8355361f5
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779444"
---
# <a name="configure-windows-update-settings-for-update-management"></a>为更新管理配置 Windows 更新设置

Azure 更新管理依赖于[Windows 更新客户端](https://docs.microsoft.com//windows/deployment/update/windows-update-overview)下载和安装 Windows 更新。 Windows 更新客户端在连接到 Windows Server Update Services （WSUS）或 Windows 更新时使用特定设置。 其中的许多设置可以通过来管理：

- 本地组策略编辑器
- 组策略
- PowerShell
- 直接编辑注册表

更新管理规定了许多指定用于控制 Windows 更新客户端的设置。 如果使用设置来启用非 Windows 更新，更新管理还将管理这些更新。 如果你想要在更新部署发生之前允许下载更新，则更新部署可能会更快、更高效，且不太可能超过维护时段。

若要详细了解如何在 Azure 订阅中设置 WSUS 并安全地使 Windows 虚拟机保持最新状态，请参阅[计划在 azure 中使用 WSUS 更新 windows 虚拟机的部署](https://docs.microsoft.com/azure/architecture/example-scenario/wsus/)。

## <a name="pre-download-updates"></a>下载前更新

若要配置自动下载更新，但不自动安装更新，可以使用组策略将[配置自动更新设置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)为**3**。 此设置允许在后台下载所需更新，并通知你更新已准备好安装。 这样，更新管理仍可控制计划，但可以在更新管理维护时段之外下载更新。 此行为可防止**维护时段超出**更新管理的错误。

可以通过运行以下命令，使用 PowerShell 启用此设置设置：

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>配置重新启动设置

[通过编辑](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)[用于管理重启](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的注册表和注册表项来配置自动更新中列出的注册表项可能会导致计算机重新启动，即使在**更新部署**设置中指定了 "**从不重新启动**" 也是如此。 配置这些注册表项以最适合您的环境。

## <a name="enable-updates-for-other-microsoft-products"></a>启用其他 Microsoft 产品的更新

默认情况下，Windows 更新客户端配置为仅提供 Windows 更新。 如果在 "更新 Windows" 设置后启用 "**向我提供其他 Microsoft 产品的更新**" 设置，则还会收到其他产品的更新，包括 Microsoft SQL Server 和其他 Microsoft 软件的安全修补程序。 如果已下载并复制适用于 Windows 2016 和更高版本的最新[管理模板文件](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)，则可以配置此选项。

如果运行的是 Windows Server 2012 R2，则组策略无法配置此设置。 在这些计算机上运行以下 PowerShell 命令。 更新管理符合此设置。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 配置设置

更新管理支持 WSUS 设置。 下面列出了可以配置的用于处理更新管理的 WSUS 设置。

### <a name="intranet-microsoft-update-service-location"></a>Intranet Microsoft 更新服务位置

你可以在 "[指定 intranet Microsoft 更新服务位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)" 下指定用于扫描和下载更新的源。 默认情况下，Windows 更新客户端配置为从 Windows 更新下载更新。 当你将 WSUS 服务器指定为计算机的源时，如果更新未在 WSUS 中获得批准，则更新部署将失败。 

若要将计算机限制为仅限内部更新服务，请将配置为[不连接到任何 Windows 更新 Internet 位置](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)。 

## <a name="next-steps"></a>后续步骤

配置 Windows 更新设置后，可以按照[管理 Azure vm 的更新和修补程序](automation-tutorial-update-management.md)中的说明来计划更新部署。
