---
title: 设置 Windows 模板计算机的指南 |微软文档
description: 在实验室服务中准备 Windows 模板计算机的通用步骤。  这些步骤包括设置 Windows 更新计划、安装 OneDrive 和安装 Office。
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c52a1212d160adce3a0a0638164833bc2907a856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514997"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>在 Azure 实验室服务中设置 Windows 模板计算机的指南

如果要为 Azure 实验室服务设置 Windows 10 模板计算机，下面是一些要考虑的最佳做法和提示。 以下配置步骤都是可选的。  但是，这些准备步骤可以帮助提高学生的效率，最大限度地减少上课时间中断，并确保他们使用最新的技术。

>[!IMPORTANT]
>本文包含 PowerShell 代码段，以简化机器模板修改过程。  对于显示的所有 PowerShell 脚本，您需要在 Windows PowerShell 中具有管理员权限来运行它们。 在 Windows 10 中，快速方法是右键单击"开始"菜单并选择"Windows PowerShell（管理员）"。

## <a name="install-and-configure-onedrive"></a>安装和配置 OneDrive

为了保护学生数据在重置虚拟机时丢失，我们建议学生将数据备份到云中。  Microsoft OneDrive 可以帮助学生保护他们的数据。  

### <a name="install-onedrive"></a>安装 OneDrive

要手动下载和安装 OneDrive，请参阅[OneDrive](https://onedrive.live.com/about/download/)或[OneDrive 业务](https://onedrive.live.com/about/business/)下载页面。

您还可以使用以下 PowerShell 脚本。  它将自动下载并安装最新版本的 OneDrive。  安装 OneDrive 客户端后，请运行安装程序。  在我们的示例中，`/allUsers`我们使用开关为计算机上的所有用户安装 OneDrive。 我们还使用`/silent`该开关静默安装 OneDrive。

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive 定制

有许多[自定义项可以执行到 OneDrive](https://docs.microsoft.com/onedrive/use-group-policy)。 让我们介绍一些更常见的自定义项。

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>静默将 Windows 已知文件夹移动到 OneDrive

文档、下载和图片等文件夹通常用于存储学生文件。 为了确保这些文件夹已备份到 OneDrive 中，我们建议您将这些文件夹移动到 OneDrive。

如果您使用的是不使用 Active Directory 的计算机上，用户可以在将这些文件夹验证为 OneDrive 后手动将这些文件夹移动到 OneDrive。

1. 打开文件资源管理器
2. 右键单击"文档"、"下载"或"图片"文件夹。
3. 转到属性>位置。  将文件夹移动到 OneDrive 目录上的新文件夹。

如果您的虚拟机已连接到 Active Directory，则可以将模板计算机设置为自动提示学生将已知文件夹移动到 OneDrive。  

您需要首先检索 Office 租户 ID。  有关进一步说明，请参阅[查找 Office 365 租户 ID](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)。  您还可以使用以下 PowerShell 获取 Office 365 租户 ID。

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

获得 Office 365 租户 ID 后，将 OneDrive 设置为使用以下 PowerShell 提示将已知文件夹移动到 OneDrive。

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>按需使用 OneDrive 文件

学生在其 OneDrive 帐户中可能有许多文件。 为了帮助节省计算机上的空间并减少下载时间，我们建议使存储在学生 OneDrive 帐户中的所有文件都是按需访问的。  按需文件仅在用户访问该文件后下载。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>静默地将用户登录到 OneDrive

OneDrive 可以设置为使用登录用户的 Windows 凭据自动登录。  自动登录对于学生使用 Office 365 学校凭据登录的班级非常有用。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>禁用 OneDrive 设置末尾显示的教程

此设置允许您阻止在 OneDrive 安装程序结束时在 Web 浏览器中启动本教程。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>设置要自动下载的文件的最大大小

此设置与在未启用 OneDrive 文件按需状态的设备上使用 Windows 凭据的"静默登录用户"一起使用。 任何具有大于指定阈值（MB）的 OneDrive 的用户都将在 OneDrive 同步客户端 （OneDrive.exe） 下载文件之前选择要同步的文件夹。  在我们的示例中，"1111-2222-3333-4444"是 Office 365 租户 ID，0005000 设置阈值为 5 GB。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>安装和配置微软 Office 365

### <a name="install-microsoft-office-365"></a>安装微软办公室 365

如果您的模板计算机需要 Office，我们建议通过[Office 部署工具 （ODT）](https://www.microsoft.com/download/details.aspx?id=49117 )安装 Office。 您需要使用[Office 365 客户端配置服务](https://config.office.com/)创建可重用的配置文件，以选择需要 Office 的体系结构、哪些功能以及更新频率。

1. 转到[Office 365 客户端配置服务](https://config.office.com/)并下载您自己的配置文件。
2. 下载[办公室部署工具](https://www.microsoft.com/download/details.aspx?id=49117)。  下载的文件将是`setup.exe`。
3. 运行`setup.exe /download configuration.xml`以下载 Office 组件。
4. 运行`setup.exe /configure configuration.xml`以安装 Office 组件。

### <a name="change-the-microsoft-office-365-update-channel"></a>更改 Microsoft Office 365 更新通道

使用 Office 配置工具，您可以设置 Office 接收更新的频率。 但是，如果需要修改 Office 在安装后接收更新的频率，可以更改更新通道 URL。 更新通道 URL 地址可在[更改组织中的设备的 Office 365 ProPlus 更新通道](https://docs.microsoft.com/deployoffice/change-update-channels)中找到。 下面的示例演示如何将 Office 365 设置为使用每月更新通道。

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>安装和配置更新

### <a name="install-the-latest-windows-updates"></a>安装最新的 Windows 更新

我们建议您在发布模板 VM 之前在模板计算机上安装最新的 Microsoft 更新，以进行安全说明。  当更新在意外时间运行时，它还可能避免学生在工作中受到干扰。

1. 从"开始"菜单启动**设置**
2. 单击**更新**&安全性
3. 单击 **"检查更新"**
4. 更新将下载并安装。

您还可以使用 PowerShell 更新模板计算机。

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>某些更新可能需要重新启动计算机。  如果需要重新启动，系统将提示您。

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>安装 Microsoft 应用商店应用的最新更新

我们建议将所有 Microsoft 应用商店应用更新到最新版本。  以下是从 Microsoft 应用商店手动更新应用程序的说明。  

1. 启动**微软应用商店**应用程序。
2. 单击应用程序右上角的用户照片旁边的椭圆 （...）。
3. 从下拉菜单中选择 **"下载**并更新"。
4. 单击 **"获取更新**"按钮。

您还可以使用 Powershell 更新已安装的 Microsoft 应用商店应用程序。

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>停止自动 Windows 更新

将 Windows 更新到最新版本后，可以考虑停止 Windows 更新。  自动更新可能会干扰计划的类时间。  如果您的课程运行时间较长，请考虑要求学生手动检查更新或设置计划上课时间以外的时间的自动更新。  有关 Windows 更新的自定义选项的详细信息，请参阅[管理其他 Windows 更新设置](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)。

可以使用以下 PowerShell 脚本停止自动 Windows 更新。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>安装外语包

如果需要在虚拟机上安装其他语言，可以通过 Microsoft 应用商店添加这些语言。

1. 启动微软商店
2. 搜索"语言包"
3. 选择要安装的语言

如果您已登录到模板 VM，请使用["安装语言包"快捷方式](ms-settings:regionlanguage?activationSource=SMC-IA-4027670)直接转到相应的设置页。

## <a name="remove-unneeded-built-in-apps"></a>删除不需要的内置应用

Windows 10 附带了许多特定类可能不需要的内置应用程序。 为了简化学生的机器映像，您可能需要从模板计算机卸载某些应用程序。  要查看已安装应用程序的列表，请使用 PowerShell `Get-AppxPackage` cmdlet。  下面的示例显示了可以删除的所有已安装的应用程序。

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

要删除应用程序，请使用"删除-Appx cmdlet"。  下面的示例演示如何删除与 XBox 相关的一切。

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>安装常见的教学相关应用程序

安装通常用于通过 Windows 应用商店应用进行教学的其他应用。 建议包括应用程序，如[微软白板应用程序](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)，[微软团队](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)，和[我的世界教育版](https://education.minecraft.net/)。 这些应用程序必须通过 Windows 应用商店或模板 VM 上的相应网站手动安装。

## <a name="conclusion"></a>结束语

本文已为您提供可选步骤，用于为有效的类准备 Windows 模板 VM。  步骤包括安装 OneDrive 和安装 Office 365、安装 Windows 更新和安装 Microsoft 应用商店应用的更新。  我们还讨论了如何将更新设置为最适合您的类的计划。  
