---
title: 将 Windows 更新设置配置为使用 Azure 更新管理
description: 本文介绍配置为使用 Azure 更新管理的 Windows 更新设置。
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 804f42121293e142cf77ad73c4aab36e62e3242d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850408"
---
# <a name="configure-windows-update-settings-for-update-management"></a>为更新管理配置 Windows 更新设置

Azure 更新管理依赖于 Windows 更新下载和安装 Windows 更新。 因此，更新管理会考虑 Windows 更新使用的许多设置。 如果使用设置来启用非 Windows 更新，更新管理还将管理这些更新。 如果你想要在更新部署发生之前允许下载更新，则更新部署可能会更快、更高效，且不太可能超过维护时段。

## <a name="pre-download-updates"></a>下载前更新

若要在组策略中配置自动下载更新，请将[配置自动更新设置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)为**3**。 此设置允许在后台下载所需的更新，但不安装它们。 这样，更新管理仍可控制计划，但可以在更新管理维护时段之外下载更新。 此行为可防止更新管理中出现 "维护时段超出" 错误。

还可以通过在要配置自动下载更新的系统上运行以下 PowerShell 命令来启用此设置：

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>禁用自动安装

默认情况下，会在 Azure 虚拟机（Vm）上启用更新的自动安装。 这可能会导致在计划安装之前安装更新，更新管理。 您可以通过将 `NoAutoUpdate` 的注册表项设置为 `1`来禁用此行为。 以下 PowerShell 代码片段显示了如何执行此操作：

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>配置重新启动设置

[通过编辑](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)[用于管理重启](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的注册表和注册表项来配置自动更新中列出的注册表项可能会导致计算机重新启动，即使在**更新部署**设置中指定了 "**从不重新启动**" 也是如此。 应将这些注册表项配置为最适合您的环境。

## <a name="enable-updates-for-other-microsoft-products"></a>启用其他 Microsoft 产品的更新

默认情况下，Windows 更新仅为 Windows 提供更新。 如果在 "更新 Windows" 设置后启用 "**向我提供其他 Microsoft 产品的更新**" 设置，则还会收到其他产品的更新，包括 Microsoft SQL Server 和其他 Microsoft 软件的安全修补程序。 不能通过组策略配置此选项。 在要启用其他 Microsoft 更新的系统上运行以下 PowerShell 命令。 更新管理将符合此设置。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 配置设置

更新管理符合 Windows Server Update Services （WSUS）设置。 下面列出了可以配置的用于处理更新管理的 WSUS 设置。

### <a name="intranet-microsoft-update-service-location"></a>Intranet Microsoft 更新服务位置

你可以在 "[指定 intranet Microsoft 更新服务位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)" 下指定用于扫描和下载更新的源。

## <a name="next-steps"></a>后续步骤

配置 Windows 更新设置后，可以按照[管理 Azure vm 的更新和修补程序](automation-tutorial-update-management.md)中的说明来计划更新部署。
