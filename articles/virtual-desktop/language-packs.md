---
title: 在 windows 虚拟桌面中的 Windows 10 虚拟机上安装语言包-Azure
description: 如何在 Windows 虚拟桌面中安装适用于 Windows 10 多会话 Vm 的语言包。
author: Heidilohr
ms.topic: how-to
ms.date: 08/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fbc2aba21212a83bd73d5664f4fe288017954c0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90084203"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>将语言包添加到 Windows 10 多会话映像

Windows 虚拟桌面是你的用户可以随时随地部署的一种服务。 这就是你的用户可以自定义其 Windows 10 企业多会话图像显示的语言很重要的原因。

可以通过两种方式来适应用户的语言需求：

- 使用每种语言的自定义映像构建专用的主机池。
- 在同一主机池中具有不同语言和本地化要求的用户，但可以自定义其图像，以确保他们可以选择所需的任何语言。

后一种方法更高效且更具成本效益。 不过，您决定哪种方法最适合您的需求。 本文将介绍如何自定义映像的语言。

## <a name="prerequisites"></a>必备条件

你需要执行以下操作来自定义 Windows 10 企业多会话映像以添加多种语言：

- 使用 Windows 10 企业多会话版本1903或更高版本的 Azure 虚拟机 (VM) 

- 语言 ISO、按需功能 (FOD) Disk 1 和收件箱应用使用映像所使用的 OS 版本的 ISO。 可以在此处下载：
     
     - 语言 ISO：
        - [Windows 10 版本1903或1909语言包 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10 版本2004语言包 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD Disk 1 ISO：
        - [Windows 10 版本1903或 1909 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10，版本 2004 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - 收件箱应用 ISO：
        - [Windows 10 版本1903或1909收件箱应用 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10 2004 版收件箱应用 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)

- Windows 文件服务器虚拟机上的 Azure 文件共享或文件共享

>[!NOTE]
>必须可以从你计划用于创建自定义映像的 Azure VM 访问) 文件共享 (存储库。

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>根据需要创建语言包和功能的内容存储库

若要为 FODs 和收件箱应用包创建内容存储库，请执行以下操作：

1. 在 Azure VM 上，从 [必备组件](#prerequisites)中的链接下载 Windows 10 多语言 ISO、FODs 和收件箱应用，用于 Windows 10 企业多会话版本1903/1909 和2004映像。

2. 打开并在 VM 上装载 ISO 文件。

3. 请访问语言包 ISO 并复制 **LocalExperiencePacks** 和 **x64 \\ langpacks** 文件夹中的内容，然后将内容粘贴到文件共享中。

4. 请访问 **FOD ISO 文件**，复制其所有内容，并将其粘贴到文件共享中。
5. 中转到收件箱应用 ISO 上的 **amd64fre** 文件夹，并将存储库中的内容复制到已准备的收件箱应用。

     >[!NOTE]
     > 如果使用的是有限存储，只需复制你知道的用户所需语言的文件。 您可以通过查看文件名称中的语言代码来区分这些文件。 例如，法语文件的名称中包含代码 "fr-fr"。 有关所有可用语言的完整语言代码列表，请参阅 [适用于 Windows 的可用语言包](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)。

     >[!IMPORTANT]
     > 某些语言需要附属包中包含的其他字体，这些字体遵循不同的命名约定。 例如，日语字体文件名包括 "Jpan"。
     >
     > [!div class="mx-imgBorder"]
     > ![日语语言包的示例，其文件名中包含 "Jpan" 语言标记。](media/language-pack-example.png)

6. 设置对语言内容存储库共享的权限，以便您拥有用于构建自定义映像的 VM 的读取访问权限。

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>手动创建自定义 Windows 10 企业多会话映像

手动创建自定义 Windows 10 企业多会话映像：

1. 部署 Azure VM，然后在 Azure 库中选择要使用的 Windows 10 企业多会话的当前版本。
2. 部署 VM 后，请使用 RDP 作为本地管理员连接到该 VM。
3. 请确保 VM 具有所有最新的 Windows 更新。 下载更新，并在必要时重新启动 VM。
4. 连接到语言包、FOD 和收件箱应用文件共享存储库，并将其安装到驱动器 E)  (例如驱动器 E）。

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>自动创建自定义 Windows 10 企业多会话映像

如果你希望通过自动化过程安装语言，则可以在 PowerShell 中设置脚本。 你可以使用以下脚本示例，安装适用于 Windows 10 企业多会话版本2004的西班牙语 (西班牙) 、法语 (法国) 和中文 (PRC) 语言包和附属包。 该脚本将语言界面包和所有必要的附属包集成到映像中。 但是，您还可以修改此脚本以安装其他语言。 只需确保从已提升权限的 PowerShell 会话中运行该脚本，否则它将不起作用。

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

>[!IMPORTANT]
>Windows 10 Enterprise 版本1903和1909不需要 `Microsoft-Windows-Client-Language-Pack_x64_<language-code>.cab` 包文件。

该脚本可能需要一段时间，具体取决于安装所需的语言数量。

脚本运行完毕后，请通过转到 "**开始**  >  **设置**" "  >  **& 语言**" 来确保正确安装了语言包  >  **Language**。 如果语言文件在那里，则一切都已设置完毕。

向 Windows 映像添加其他语言后，还需要更新收件箱应用以支持添加的语言。 这可以通过使用收件箱应用 ISO 中的内容刷新预安装的应用来完成。 若要在断开连接的环境中执行此刷新 (无法从 VM) Internet 访问，可以使用以下 PowerShell 脚本示例来自动执行此过程。

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>ISO 中包含的收件箱应用不是预安装的 Windows 应用的最新版本。 若要获取所有应用的最新版本，需要使用 Windows 应用商店应用程序更新应用程序，并在安装了其他语言之后执行手动搜索更新。

完成后，请确保断开共享的连接。

## <a name="finish-customizing-your-image"></a>完成自定义映像

安装语言包之后，可以安装要添加到自定义映像的任何其他软件。

完成自定义映像后，需 (sysprep) 运行系统准备工具。

运行 sysprep：

1. 打开提升的命令提示符，并运行以下命令来通用化映像：  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. 按照在 [Azure 中创建通用 VM 的托管映像](../virtual-machines/windows/capture-image-resource.md)中的说明，停止 VM，然后将其捕获到托管映像中。

3. 你现在可以使用自定义映像来部署 Windows 虚拟机主机池。 若要了解如何部署主机池，请参阅 [教程：使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

## <a name="enable-languages-in-windows-settings-app"></a>在 Windows 设置应用中启用语言

最后，在部署主机池之后，你需要将该语言添加到每个用户的语言列表中，以便他们可以在 "设置" 菜单中选择其首选语言。

若要确保你的用户可以选择你安装的语言，请以用户身份登录，然后运行以下 PowerShell cmdlet 以将安装的语言包添加到 "语言" 菜单。 你还可以将此脚本设置为自动任务或在用户登录到其会话时激活的登录脚本。

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

用户更改其语言设置后，他们将需要注销其 Windows 虚拟桌面会话，并再次登录以使更改生效。 

## <a name="next-steps"></a>后续步骤

如果对语言包的已知问题感到好奇，请参阅 [在 Windows 10 版本1803及更高版本中添加语言包：已知问题](/windows-hardware/manufacture/desktop/language-packs-known-issue)。

如果你有关于 Windows 10 企业多会话的任何其他问题，请查看我们的 [常见问题解答](windows-10-multisession-faq.md)。
