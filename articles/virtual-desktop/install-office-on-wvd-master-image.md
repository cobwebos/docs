---
title: 在主 VHD 映像上安装 Office - Azure
description: 如何在 Windows 虚拟桌面主映像上安装和自定义 Office 到 Azure。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127850"
---
# <a name="install-office-on-a-master-vhd-image"></a>在主 VHD 映像中安装 Office

本文介绍如何在主虚拟硬盘 （VHD） 映像上安装 Office 365 ProPlus、OneDrive 和其他常见应用程序，以便上载到 Azure。 如果用户需要访问某些业务线 （LOB） 应用程序，我们建议您在完成本文中的说明后安装它们。

本文假定您已经创建了虚拟机 （VM）。 如果没有，请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md#create-a-vm)

本文还假定您在 VM 上具有提升访问权限，无论是 Azure 还是 Hyper-V 管理器中预配。 如果没有，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../role-based-access-control/elevate-access-global-admin.md)。

>[!NOTE]
>这些说明适用于可与组织的现有过程配合使用的特定于 Windows 虚拟桌面的配置。

## <a name="install-office-in-shared-computer-activation-mode"></a>以共享计算机激活模式安装 Office

通过共享计算机激活，您可以将 Office 365 ProPlus 部署到组织中由多个用户访问的计算机。 有关共享计算机激活的详细信息，请参阅[Office 365 ProPlus 的共享计算机激活概述](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/)。

使用[Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)安装 Office。 Windows 10 企业多会话仅支持以下版本的 Office：
- Office 365 ProPlus
- 微软 365 业务订阅附带的 Office 365 业务

Office 部署工具需要配置文件 XML 文件。 要自定义以下示例，请参阅[Office 部署工具的配置选项](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)。

我们提供的此示例配置 XML 将执行以下操作：

- 从每月频道安装 Office，并在执行时从每月频道提供更新。
- 使用 x64 体系结构。
- 禁用自动更新。
- 删除 Office 的任何现有安装并迁移其设置。
- 启用共享计算机激活。

>[!NOTE]
>Visio 的模具搜索功能可能无法在 Windows 虚拟桌面中按预期工作。

下面是此示例配置 XML 不会执行的内容：

- 安装 Skype 业务
- 以每个用户模式安装 OneDrive。 要了解更多信息，请参阅[在每台计算机模式下安装 OneDrive。](#install-onedrive-in-per-machine-mode)

>[!NOTE]
>可通过组策略对象 （GPO） 或注册表设置设置共享计算机激活。 GPO 位于**计算机\\配置策略\\管理模板\\Microsoft Office 2016 （机器\\） 许可设置**

"办公室部署工具"包含 setup.exe。 要安装 Office，请在命令行中运行以下命令：

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>示例配置.xml

以下 XML 示例将安装每月版本。

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office 团队建议对**OfficeClientEdition**参数使用 64 位安装。

安装 Office 后，可以更新默认的 Office 行为。 单独或在批处理文件中运行以下命令以更新行为。

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>在每台计算机模式下安装 OneDrive

OneDrive 通常按用户安装。 在此环境中，应按计算机安装它。

以下是在每台计算机模式下安装 OneDrive 的方式：

1. 首先，创建一个位置来暂达 OneDrive 安装程序。 本地磁盘文件夹或 [\\\\unc] （file://unc） 位置正常。

2. 使用此链接将 OneDriveSetup.exe 下载到您的暂存位置：<https://aka.ms/OneDriveWVD-Installer>

3. 如果您通过省略**\<排除应用 ID="OneDrive"/\>** 使用 OneDrive 安装办公室，请通过运行以下命令从提升的命令提示符卸载任何现有的 OneDrive 每个用户安装：
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 从提升的命令提示符运行此命令以设置**AllUser 安装**注册表值：

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 运行此命令以在每台计算机模式下安装 OneDrive：

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 运行此命令可配置 OneDrive 以在所有用户登录时开始：

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 通过运行以下命令**启用静默配置用户帐户**。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 通过运行以下命令将 Windows 已知文件夹重定向并将其移动到 OneDrive。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>团队和 Skype

Windows 虚拟桌面不支持 Skype 业务和团队。

## <a name="next-steps"></a>后续步骤

现在，您已将 Office 添加到映像中，可以继续自定义主 VHD 映像。 请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md)。
