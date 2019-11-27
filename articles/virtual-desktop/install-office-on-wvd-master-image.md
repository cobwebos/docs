---
title: 在主 VHD 映像上安装 Office-Azure
description: 如何在 Windows 虚拟桌面版映像到 Azure 上安装和自定义 Office。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
ms.openlocfilehash: 059748f6f08b1c73d56aa3a127aa785f55eb63ee
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539137"
---
# <a name="install-office-on-a-master-vhd-image"></a>在主 VHD 映像中安装 Office

本文介绍如何在用于上传到 Azure 的主虚拟硬盘（VHD）映像上安装 Office 365 ProPlus、OneDrive 和其他常用应用程序。 如果用户需要访问特定业务线（LOB）应用程序，我们建议在完成本文中的说明后安装这些应用程序。

本文假设你已创建虚拟机（VM）。 否则，请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md#create-a-vm)

本文还假定你在 VM 上拥有提升的访问权限，无论是在 Azure 中预配还是 Hyper-v 管理器。 否则，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)。

>[!NOTE]
>这些说明适用于可与组织的现有过程配合使用的特定于 Windows 虚拟桌面的配置。

## <a name="install-office-in-shared-computer-activation-mode"></a>在共享计算机激活模式下安装 Office

共享计算机激活使你能够将 Office 365 ProPlus 部署到你组织中由多个用户访问的计算机。 有关共享计算机激活的详细信息，请参阅[Office 365 的共享计算机激活概述 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus)。

使用[Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)安装 office。 Windows 10 企业多会话仅支持以下版本的 Office：
- Office 365 ProPlus
- Microsoft 365 商业版订阅随附的 Office 365 业务

Office 部署工具需要一个配置 XML 文件。 若要自定义下面的示例，请参阅[Office 部署工具的配置选项](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool)。

我们提供的此示例配置 XML 将执行以下操作：

- 从每月频道安装 Office，并在执行时从每月频道中提供更新。
- 使用 x64 体系结构。
- 禁用自动更新。
- 删除 Office 的任何现有安装并迁移其设置。
- 启用共享计算机激活。

>[!NOTE]
>在 Windows 虚拟桌面中，Visio 的模具搜索功能可能无法按预期方式工作。

下面是此示例配置 XML 不会执行的操作：

- 安装 Skype for Business
- 在每用户模式下安装 OneDrive。 若要了解详细信息，请参阅[在每个计算机模式下安装 OneDrive](#install-onedrive-in-per-machine-mode)。

>[!NOTE]
>共享计算机激活可通过组策略对象（Gpo）或注册表设置进行设置。 GPO 位于 "**计算机配置"\\策略中\\管理模板\\Microsoft Office 2016 （计算机）\\授权设置**

Office 部署工具包含 setup.exe。 若要安装 Office，请在命令行中运行以下命令：

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>示例配置 .xml

下面的 XML 示例将安装每月发布。

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
>Office 团队建议为**OfficeClientEdition**参数使用64位安装。

安装 Office 后，您可以更新默认 Office 行为。 单独运行以下命令，或在批处理文件中运行以更新行为。

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

## <a name="install-onedrive-in-per-machine-mode"></a>在每个计算机模式下安装 OneDrive

OneDrive 通常按用户安装。 在此环境中，应将其安装在每台计算机上。

下面介绍如何在每个计算机模式下安装 OneDrive：

1. 首先，创建一个用于暂存 OneDrive 安装程序的位置。 本地磁盘文件夹或 [\\\\unc] （file://unc）位置正常。

2. 通过以下链接将 OneDriveSetup 下载到暂存位置： <https://aka.ms/OneDriveWVD-Installer>

3. 如果通过省略 **\<EXCLUDEAPP ID = "OneDrive"/\>** 来安装 Office with onedrive，请运行以下命令，从提升的命令提示符中卸载任何现有的 OneDrive 每用户安装：
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 在提升的命令提示符下运行以下命令，设置**AllUsersInstall**注册表值：

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 运行以下命令以在每个计算机模式下安装 OneDrive：

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 运行以下命令以将 OneDrive 配置为在所有用户登录时启动：

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 通过运行以下命令启用**无提示配置用户帐户**。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 通过运行以下命令，将 Windows 已知文件夹重定向并移至 OneDrive。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>团队和 Skype

Windows 虚拟桌面不支持 Skype for business 和团队。

## <a name="next-steps"></a>后续步骤

现在，你已将 Office 添加到映像，接下来可以继续自定义你的主要 VHD 映像。 请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md)。
