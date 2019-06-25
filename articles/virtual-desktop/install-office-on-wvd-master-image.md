---
title: 在主 VHD 映像-Azure 上安装 Office
description: 如何安装和到 Azure 的 Windows 虚拟桌面预览主映像上自定义 Office。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: cb9edbb508ddd993dcefbf69eb06b4f0d4156485
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742551"
---
# <a name="install-office-on-a-master-vhd-image"></a>在主 VHD 映像中安装 Office

本文介绍如何在上传到 Azure 的主虚拟硬盘 (VHD) 映像上安装 Office 365 ProPlus、 OneDrive 和其他常见的应用程序。 如果你的用户需要访问某些业务线 (LOB) 应用程序，我们建议在完成本文中的说明进行操作后安装。

本文假定你已创建虚拟机 (VM)。 如果没有，请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md#create-a-vm)

本文还假定您拥有提升访问权限的 VM 上，无论它在 Azure 或 Hyper-v 管理器预配。 如果没有，请参阅[提升访问权限来管理所有 Azure 订阅和管理组](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)。

>[!NOTE]
>这些说明适用于可以与组织的现有进程使用的特定于 Windows 的虚拟桌面预览版的配置。

## <a name="install-office-in-shared-computer-activation-mode"></a>在共享的计算机激活模式下安装 Office

共享的计算机激活可让你将 Office 365 专业增强版部署到多个用户访问你组织中的计算机。 有关共享的计算机激活的详细信息，请参阅[的 Office 365 专业增强版的共享的计算机激活概述](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus)。

使用[Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)来安装 Office。 Windows 10 企业版多会话仅支持以下 Office 版本：
- Office 365 ProPlus
- Microsoft 365 商业版订阅随附的 office 365 企业版

Office 部署工具需要配置 XML 文件。 若要自定义下面的示例，请参阅[Office 部署工具的配置选项](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool)。

此示例配置 XML，我们提供了将执行以下操作：

- 从预览体验成员频道安装 Office，并将更新从预览体验成员通道传输时就会执行它们。
- 使用 x64 体系结构。
- 禁用自动更新。
- 安装 Visio 和项目。
- 删除任何现有的 Office 安装，并将其设置迁移。
- 启用共享的计算机激活。

>[!NOTE]
>预览配置期间，在 Visio 模具搜索功能不会运行 Windows 虚拟桌面。

下面是此示例配置 XML 不会执行的操作：

- 安装 Skype for Business
- 在每个用户模式下安装 OneDrive。 若要了解详细信息，请参阅[每个计算机模式下安装 OneDrive](#install-onedrive-in-per-machine-mode)。

>[!NOTE]
>通过组策略对象 (Gpo) 或注册表设置，可以设置共享的计算机激活。 GPO 位于**计算机配置\\策略\\管理模板\\Microsoft Office 2016 （计算机）\\授权设置**

Office 部署工具包含 setup.exe。 若要安装 Office，请在命令行中运行以下命令：

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>示例 configuration.xml

下面的 XML 示例将安装预览体验成员版本。

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Office 团队建议使用 64 位安装程序**OfficeClientEdition**参数。

安装 Office 之后, 可以更新默认 Office 行为。 单独或批处理文件，若要更新的行为，请运行以下命令。

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

OneDrive 是通常情况下已安装的每个用户。 在此环境中，它应安装每台计算机。

下面介绍了如何在每台计算机模式下安装 OneDrive:

1. 首先，创建用于暂存 OneDrive 安装程序的位置。 本地磁盘文件夹或 [\\\\unc] (file://unc) 位置很好。

2. 到暂存位置与此链接下载 OneDriveSetup.exe: <https://aka.ms/OneDriveWVD-Installer>

3. 如果使用 OneDrive 中省略安装 office  **\<ExcludeApp ID ="OneDrive"/\>** ，通过运行以下命令，从提升的命令提示符卸载任何现有的 OneDrive 每用户安装命令：
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 从提升的命令提示符设置运行此命令**AllUsersInstall**注册表值：

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 运行以下命令在每台计算机模式下安装 OneDrive:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 运行以下命令来配置 OneDrive 的所有用户登录时启动：

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 启用**以无提示方式将用户帐户配置**通过运行以下命令。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 重定向和移动 Windows 通过运行以下命令的已知文件夹复制到 OneDrive。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>团队和 Skype

Windows 虚拟桌面不支持 Skype for Business 和团队。

## <a name="next-steps"></a>后续步骤

现在，已添加到映像的 Office，您可以继续自定义主 VHD 映像。 请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md)。
