---
title: 将 Windows 更新设置配置为使用 Azure 更新管理
description: 本文介绍配置为使用 Azure 更新管理的 Windows 更新设置。
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279021"
---
# <a name="configure-windows-update-settings-for-update-management"></a>为更新管理配置 Windows 更新设置

Azure 更新管理依赖于[Windows 更新客户端](https://docs.microsoft.com//windows/deployment/update/windows-update-overview)来下载和安装 Windows 更新。 Windows 更新客户端在连接到 Windows 服务器更新服务 （WSUS） 或 Windows 更新时使用的特定设置。 许多这些设置可以使用以下方式进行管理：

- 本地组策略编辑器
- 组策略
- PowerShell
- 直接编辑注册表

更新管理尊重为控制 Windows 更新客户端而指定的许多设置。 如果使用设置启用非 Windows 更新，更新管理也将管理这些更新。 如果要在更新部署发生之前启用更新下载，更新部署可以更快、更高效，并且不太可能超过维护窗口。

## <a name="pre-download-updates"></a>预下载更新

要配置自动下载更新但不自动安装更新，可以使用组策略将["配置自动更新"设置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)设置为**3**。 此设置允许在后台下载所需的更新，并通知您更新已准备好安装。 这样，更新管理仍控制着计划，但更新可以在更新管理维护窗口之外下载。 此行为可防止更新管理中**维护窗口超过**错误。

您可以通过运行以下命令，使用 PowerShell 启用此设置：

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>配置重新启动设置

通过编辑[用于管理重新启动的注册表和注册表项](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)[在"配置自动更新"](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中列出的注册表项可能会导致计算机重新启动，即使您在 **"更新部署"** 设置中指定 **"从不重新启动**"。 配置这些注册表项以最适合您的环境。

## <a name="enable-updates-for-other-microsoft-products"></a>启用其他 Microsoft 产品的更新

默认情况下，Windows 更新客户端配置为仅为 Windows 提供更新。 如果在更新 Windows 设置**时启用其他 Microsoft 产品的"给我更新**"，您还会收到其他产品的更新，包括 Microsoft SQL Server 和其他 Microsoft 软件的安全修补程序。 如果您已下载并复制适用于 Windows 2016 及更高版本的最新[管理模板文件](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)，则可以配置此选项。

如果您正在运行 Windows Server 2012 R2，则组策略无法配置此设置。 在这些计算机上运行以下 PowerShell 命令。 更新管理符合此设置。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 配置设置

更新管理支持 WSUS 设置。 下面列出了可用于使用更新管理可以配置的 WSUS 设置。

### <a name="intranet-microsoft-update-service-location"></a>内联网微软更新服务位置

您可以在["指定内联网微软更新服务位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)"下指定用于扫描和下载更新的源。 默认情况下，Windows 更新客户端配置为从 Windows 更新下载更新。 当您指定 WSUS 服务器作为计算机的源时，如果更新在 WSUS 中未批准，则更新部署将失败。 

要将计算机限制为仅该内部更新服务，请配置["不要连接到任何 Windows 更新 Internet 位置](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)"。 

## <a name="next-steps"></a>后续步骤

配置 Windows 更新设置后，可以按照[Azure VM 的管理更新和修补程序](automation-tutorial-update-management.md)中的说明来安排更新部署。
