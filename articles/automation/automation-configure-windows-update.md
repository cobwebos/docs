---
title: 将 Windows 更新设置配置为使用 Azure 更新管理
description: 本文介绍你配置为使用的 Windows 更新设置更新管理
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377559"
---
# <a name="configure-windows-update-settings-for-update-management"></a>为更新管理配置 Windows 更新设置

更新管理依赖于 Windows 更新来下载和安装 Windows 更新。 因此，我们会遵循 Windows 更新使用的许多设置。 如果使用设置来启用非 Windows 更新，更新管理也将管理这些更新。 如果你想要在更新部署发生之前启用下载更新，更新部署会更快且不太可能会超过维护时段。

## <a name="pre-download-updates"></a>预下载更新

若要在组策略中自动配置下载更新，可以将“[配置自动更新设置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)”设置为 **3**。 将在后台下载所需的更新，但不会安装它们。 这让更新管理可以始终控制计划，但允许在更新管理维护时段外下载更新。 这可阻止更新管理中出现“已超过维护时段”错误。

此外，还可以使用 PowerShell 进行相关设置，在要自动下载更新的系统上运行 PowerShell。

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>禁用自动安装

默认情况下，Azure Vm 已启用自动安装的更新。 这可能会导致在计划更新更新管理之前安装更新。 您可以通过将 `NoAutoUpdate` 注册表项设置为 @no__t 来禁用此行为。 以下 PowerShell 代码片段显示了执行此操作的一种方法。

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>配置重新启动设置

通过编辑[用于管理重启](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的注册表和注册表项在 "[配置自动更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry)" 下列出的注册表项可能会导致计算机重新启动，即使您在 "更新部署" 设置中指定了 "**从不重新启动**". 应根据环境需要配置这些注册表项。

## <a name="enable-updates-for-other-microsoft-products"></a>启用其他 Microsoft 产品的更新

默认情况下，Windows 更新仅为 Windows 提供更新。 如果在**更新 Windows 时为其他 Microsoft 产品启用了 "向我提供更新**"，则会向你提供其他产品的更新，包括 SQL Server 或其他第一方软件的安全修补程序。 不能通过组策略配置此选项。 在你想要启用其他第一方修补程序的系统上运行以下 PowerShell，更新管理将支持此设置。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 配置设置

**更新管理**遵循 WSUS 配置设置。 下面列出了可以配置用于更新管理的 WSUS 设置的列表。

### <a name="intranet-microsoft-update-service-location"></a>Intranet Microsoft 更新服务位置

可以指定在[Intranet Microsoft 更新服务位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)下扫描和下载更新的源。

## <a name="next-steps"></a>后续步骤

配置 Windows 更新设置后，可以按照[管理 Azure vm 的更新和修补程序](automation-tutorial-update-management.md)中的说明来计划更新部署。