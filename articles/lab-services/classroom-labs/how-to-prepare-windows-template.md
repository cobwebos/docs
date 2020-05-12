---
title: 设置 Windows 模板计算机指南 |Microsoft Docs
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
ms.openlocfilehash: 7ed2a506fc4446f78685c6cd6ae9dec2b65e1743
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119291"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>在 Azure 实验室服务中设置 Windows 模板计算机的指南

如果要为 Azure 实验室服务设置 Windows 10 模板计算机，以下是一些最佳做法和注意事项。 以下配置步骤是可选的。  不过，这些准备步骤可以帮助您的学生提高工作效率，最大限度地减少类时间中断，并确保他们使用最新的技术。

>[!IMPORTANT]
>本文包含用于简化计算机模板修改过程的 PowerShell 代码段。  对于所有显示的 PowerShell 脚本，你需要在 Windows PowerShell 中以管理员权限运行它们。 在 Windows 10 中，执行此操作的一种快速方法是右键单击 "开始" 菜单，然后选择 "Windows PowerShell （Admin）"。

## <a name="install-and-configure-onedrive"></a>安装和配置 OneDrive

若要防止在虚拟机重置时学生数据丢失，我们建议学生将其数据备份到云。  Microsoft OneDrive 可以帮助学生保护他们的数据。  

### <a name="install-onedrive"></a>安装 OneDrive

若要手动下载和安装 OneDrive，请参阅[onedrive](https://onedrive.live.com/about/download/)或[onedrive for](https://onedrive.live.com/about/business/) business 下载页。

你还可以使用以下 PowerShell 脚本。  它将自动下载并安装最新版本的 OneDrive。  安装 OneDrive 客户端后，运行安装程序。  在我们的示例中，我们使用 `/allUsers` 开关为计算机上的所有用户安装 OneDrive。 我们还使用 `/silent` 开关以无提示方式安装 OneDrive。

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

### <a name="onedrive-customizations"></a>OneDrive 自定义

有很多[可以对 OneDrive 进行的自定义](https://docs.microsoft.com/onedrive/use-group-policy)。 我们介绍一些更常见的自定义。

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>以无提示方式将 Windows 已知文件夹移至 OneDrive

文档、下载和图片等文件夹通常用于存储学生文件。 若要确保将这些文件夹备份到 OneDrive，我们建议将这些文件夹移至 OneDrive。

如果你在不使用 Active Directory 的计算机上，则用户在向 OneDrive 进行身份验证后，可以手动将这些文件夹移动到 OneDrive。

1. 打开文件资源管理器
2. 右键单击 "文档"、"下载" 或 "图片" 文件夹。
3. > 位置 "中转到" 属性 "。  将文件夹移动到 OneDrive 目录上的新文件夹。

如果虚拟机已连接到 Active Directory，则可以将模板计算机设置为自动提示你的学生将已知文件夹移至 OneDrive。  

需要首先检索 Office 租户 ID。  有关更多说明，请参阅[查找 Office 365 租户 ID](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)。  还可以使用以下 PowerShell 获取 Office 365 租户 ID。

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

获得 Office 365 租户 ID 后，请使用以下 PowerShell 将 OneDrive 设置为提示将已知文件夹移至 OneDrive。

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

学生可以在其 OneDrive 帐户中包含多个文件。 为了帮助节省计算机上的空间并缩短下载时间，我们建议你根据需要将所有文件存储在学生的 OneDrive 帐户中。  仅当用户访问文件后，按需文件才会下载。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>以无提示方式登录用户到 OneDrive

可以将 OneDrive 设置为自动登录到已登录用户的 Windows 凭据。  对于学生用其 Office 365 学校凭据登录的类，自动登录是非常有用的。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>禁用在 OneDrive 安装程序结束时显示的教程

此设置使你可以阻止教程在 OneDrive 安装结束时在 web 浏览器中启动。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>设置要自动下载的文件的最大大小

此设置与在未启用 "按需 OneDrive 文件" 的设备上以无提示方式登录用户的 Windows 凭据一起使用。 如果任何用户的 OneDrive 大于指定的阈值（以 MB 为单位），则在 OneDrive 同步客户端（OneDrive）下载文件之前，系统将提示其选择要同步的文件夹。  在我们的示例中，"1111-2222-3333-4444" 是 Office 365 租户 ID，0005000将阈值设置为 5 GB。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>安装和配置 Microsoft Office 365

### <a name="install-microsoft-office-365"></a>安装 Microsoft Office 365

如果你的模板计算机需要 Office，则建议通过[Office 部署工具（ODT）](https://www.microsoft.com/download/details.aspx?id=49117 )安装 office。 你将需要使用[office 365 客户端配置服务](https://config.office.com/)创建一个可重用的配置文件，以选择哪些体系结构、需要从 Office 了解哪些功能以及更新的频率。

1. 请参阅[Office 365 客户端配置服务](https://config.office.com/)并下载你自己的配置文件。
2. 下载[Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)。  下载的文件将是 `setup.exe` 。
3. 运行 `setup.exe /download configuration.xml` 下载 Office 组件。
4. 运行 `setup.exe /configure configuration.xml` 以安装 Office 组件。

### <a name="change-the-microsoft-office-365-update-channel"></a>更改 Microsoft Office 365 更新通道

使用 Office 配置工具可以设置 Office 接收更新的频率。 但是，如果你需要修改 Office 在安装后接收更新的频率，你可以更改更新通道 URL。 可以在[为组织中的设备更改 Office 365 ProPlus 更新通道中](https://docs.microsoft.com/deployoffice/change-update-channels)找到更新通道 URL 地址。 下面的示例演示如何将 Office 365 设置为使用月度更新通道。

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>安装和配置更新

### <a name="install-the-latest-windows-updates"></a>安装最新的 Windows 更新

建议在发布模板 VM 之前，在模板计算机上安装最新的 Microsoft 更新，以确保安全。  当更新在意外的时间运行时，它还可能会避免将学生的工作中断。

1. 从 "开始" 菜单启动**设置**
2. 单击 "**更新**" & 安全性
3. 单击 "**检查更新**"
4. 更新将下载并安装。

你还可以使用 PowerShell 来更新模板计算机。

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>某些更新可能需要重新启动计算机。  如果需要重新启动，则系统会提示您。

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>为 Microsoft Store 应用安装最新更新

建议将所有 Microsoft Store 应用更新到最新版本。  下面是有关从 Microsoft Store 中手动更新应用程序的说明。  

1. 启动**Microsoft Store**应用程序。
2. 单击应用程序右上角的 "用户照片" 旁边的省略号（...）。
3. 从下拉菜单中选择 "**下载**和更新"。
4. 单击 "**获取更新**" 按钮。

你还可以使用 PowerShell 来更新已安装 Microsoft Store 应用程序。

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>停止自动 Windows 更新

将 Windows 更新到最新版本后，你可能会考虑停止 Windows 更新。  自动更新可能会干扰计划的类时间。  如果你的课程运行时间较长，请考虑询问学生手动检查更新或在计划的类时间之外的时间设置自动更新。  有关 Windows 更新的自定义选项的详细信息，请参阅[管理其他 Windows 更新设置](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)。

可以使用以下 PowerShell 脚本停止自动 Windows 更新。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>安装外语包

如果需要在虚拟机上安装其他语言，可以通过 Microsoft Store 添加它们。

1. 启动 Microsoft Store
2. 搜索 "语言包"
3. 选择要安装的语言

如果你已登录到模板 VM，请使用 "安装语言包" 快捷方式（ `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` ）直接前往适当的设置页面。

## <a name="remove-unneeded-built-in-apps"></a>删除不需要的内置应用

Windows 10 附带了许多内置的应用程序，这些应用程序可能不是您的特定类所必需的。 若要简化学生的计算机映像，可能需要从模板计算机中卸载某些应用程序。  若要查看已安装应用程序的列表，请使用 PowerShell `Get-AppxPackage` cmdlet。  下面的示例显示了可以删除的所有已安装的应用程序。

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

若要删除应用程序，请使用 Remove-Appx cmdlet。  下面的示例演示如何删除与 XBox 相关的所有内容。

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>安装与教学相关的常见应用程序

安装通常用于通过 Windows 应用商店应用进行教学的其他应用。 建议包括应用程序，如[Microsoft 白板应用](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)、 [Microsoft 团队](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)和[Minecraft 教育版](https://education.minecraft.net/)。 必须通过 Windows 应用商店或模板 VM 上各自的网站手动安装这些应用程序。

## <a name="conclusion"></a>结论

本文介绍了为有效类准备 Windows 模板 VM 的可选步骤。  步骤包括安装 OneDrive 和安装 Office 365、安装 Windows 更新和安装 Microsoft Store 应用的更新。  还介绍了如何设置最适合你的类的计划的更新。  

## <a name="next-steps"></a>后续步骤
请参阅如何控制 Windows 关机行为以帮助管理成本的文章：[控制 windows 关闭行为指南](how-to-windows-shutdown.md)
