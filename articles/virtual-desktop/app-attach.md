---
title: Windows 虚拟桌面 .MSIX 应用附加-Azure
description: 如何为 Windows 虚拟桌面设置 .MSIX app attach。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 94ec85ae658ca6012cd1f1594b431d12bb73013d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121059"
---
# <a name="set-up-msix-app-attach"></a>设置 MSIX 应用附加

> [!IMPORTANT]
> .MSIX 应用附加当前为公共预览版。
> 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本主题将指导你完成如何在 Windows 虚拟桌面环境中设置 .MSIX app attach。

## <a name="requirements"></a>要求

在开始之前，你需要配置 .MSIX 应用附加：

- 访问 Windows 有问必答门户以获取 Windows 10 版本，并支持 .MSIX 应用附加 Api。
- 正常运行的 Windows 虚拟桌面部署。 有关信息，请参阅[在 Windows 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。
- .MSIX 打包工具
- Windows 虚拟桌面部署中将存储 .MSIX 包的网络共享

## <a name="get-the-os-image"></a>获取 OS 映像

首先，需要获取要用于 .MSIX 应用的操作系统映像。 获取 OS 映像：

1. 打开[Windows 预览体验门户](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0)并登录。

     >[!NOTE]
     >你必须是 Windows 预览体验计划的成员才能访问 Windows 预览体验门户。 若要了解有关 Windows 预览体验计划的详细信息，请查看我们的[Windows 预览体验文档](/windows-insider/at-home/)。

2. 向下滚动到 "**选择版本**" 部分，并选择 " **Windows 10 预览体验版（快速）-生成 19041** " 或更高版本。

3. 选择 "**确认**"，然后选择要使用的语言，然后再次选择 "**确认**"。
    
     >[!NOTE]
     >目前，英语是通过此功能测试的唯一语言。 您可以选择其他语言，但它们可能不会按预期显示。
    
4. 生成下载链接时，选择 " **64 位下载**" 并将其保存到本地硬盘。

## <a name="prepare-the-vhd-image-for-azure"></a>准备 Azure 的 VHD 映像 

在开始之前，需要创建一个主 VHD 映像。 如果尚未创建主 VHD 映像，请参阅[准备和自定义主 vhd 映像](set-up-customize-master-image.md)，并按照此处的说明进行操作。 

创建主 VHD 映像后，必须对 .MSIX 应用附加应用程序禁用自动更新。 若要禁用自动更新，需要在提升的命令提示符下运行以下命令：

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

禁用自动更新后，你必须启用 Hyper-v，因为你将使用球场命令来暂存和卸载 VHD 到 "转储"。 

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
```
>[!NOTE]
>此更改将需要你重新启动虚拟机。

接下来，准备 Azure 的 VM VHD，并将生成的 VHD 磁盘上传到 Azure。 若要了解详细信息，请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md)。

将 VHD 上传到 Azure 之后，请按照[使用 Azure Marketplace 创建主机池](create-host-pools-azure-marketplace.md)教程中的说明，创建基于此新映像的主机池。

## <a name="prepare-the-application-for-msix-app-attach"></a>准备应用程序以进行 .MSIX 应用附加 

如果你已有 .MSIX 包，请直接跳到[配置 Windows 虚拟桌面基础结构](#configure-windows-virtual-desktop-infrastructure)。 如果要测试旧应用程序，请按照[从 VM 上的桌面安装程序创建 .msix 包](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的说明，将旧应用程序转换为 .msix 包。

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>为 .MSIX 生成 VHD 或 VHDX 包

包采用 VHD 或 VHDX 格式来优化性能。 .MSIX 需要 VHD 或 VHDX 包才能正常工作。

若要为 .MSIX 生成 VHD 或 VHDX 包：

1. [下载 msixmgr 工具](https://aka.ms/msixmgr)，并将 .zip 文件夹保存到会话主机 VM 内的文件夹中。

2. 解压缩 msixmgr 文件夹。

3. 将源 .MSIX 包放置在已解压缩 msixmgr 工具的同一文件夹中。

4. 在 PowerShell 中运行以下 cmdlet 以创建 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >请确保 VHD 大小足以容纳扩展的 .MSIX。 *

5. 运行以下 cmdlet 以装载新创建的 VHD：

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. 运行此 cmdlet 可初始化 VHD：

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. 运行此 cmdlet 可创建新的分区：

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. 运行此 cmdlet 可设置分区的格式：

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. 在装载的 VHD 上创建父文件夹。 此步骤是必需的，因为 .MSIX 应用附加需要父文件夹。 你可以为父文件夹命名，无论你喜欢什么。

### <a name="expand-msix"></a>展开 .MSIX

然后，需要通过将 .MSIX 映像解压缩来 "扩展"。 若要解压缩 .MSIX 映像：

1. 以管理员身份打开命令提示符，然后导航到下载并解压缩 msixmgr 工具的文件夹。

2. 运行以下 cmdlet，将 .MSIX 解压缩到在上一部分中创建和安装的 VHD。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    解压缩完成后，应显示以下消息：

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 如果在网络或未连接到 internet 的设备上使用来自 Microsoft Store for Business （或教育版）的包或未连接到 internet 的设备，则需要从应用商店获取包许可证，然后将其安装为成功运行应用。 请参阅[脱机使用包](#use-packages-offline)。

3. 导航到已装载的 VHD 并打开应用文件夹并确认包内容存在。

4. 卸载 VHD。

## <a name="configure-windows-virtual-desktop-infrastructure"></a>配置 Windows 虚拟桌面基础结构

按照设计，单个 .MSIX 扩展包（在上一节中创建的 VHD）可以在多个会话主机 Vm 之间共享，因为 Vhd 在只读模式下附加。

在开始之前，请确保网络共享满足以下要求：

- 共享与 SMB 兼容。
- 作为会话主机池一部分的 Vm 具有对共享的 NTFS 权限。

### <a name="set-up-an-msix-app-attach-share"></a>设置 .MSIX 应用附加共享 

在你的 Windows 虚拟桌面环境中，创建一个网络共享并将包移到该处。

>[!NOTE]
> 创建 .MSIX 网络共享的最佳做法是设置具有 NTFS 只读权限的网络共享。

## <a name="install-certificates"></a>安装证书

如果你的应用使用不受信任的证书或自签名证书，请参阅下面的安装方法：

1. 右键单击包，然后选择 "**属性**"。
2. 在出现的窗口中，选择 "**数字签名**" 选项卡。选项卡上的列表中只能有一个项目，如下图所示。 选择该项以突出显示该项，然后选择 "**详细信息**"。
3. 当 "数字签名详细信息" 窗口出现时，选择 "**常规**" 选项卡，然后选择 "**安装证书**"。
4. 当安装程序打开时，选择 "**本地计算机**" 作为存储位置，然后选择 "**下一步**"。
5. 如果安装程序询问你是否允许应用对设备进行更改，请选择 **"是"**。
6. 选择 **"将所有证书放入下列存储**"，然后选择 "**浏览**"。
7. 当 "选择证书存储" 窗口出现时，选择 "**受信任人**"，然后选择 **"确定"**。
8. 选择“完成”  。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>准备 PowerShell 脚本以进行 .MSIX 应用附加

.MSIX 应用附加具有四个必须按以下顺序执行的不同阶段：

1. 阶段
2. 注册
3. 解除
4. 转储

每个阶段都创建 PowerShell 脚本。 [此处](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)提供了每个阶段的示例脚本。

### <a name="stage-the-powershell-script"></a>暂存 PowerShell 脚本

在更新 PowerShell 脚本之前，请确保在 VHD 中具有卷的卷 GUID。 获取卷 GUID：

1.  在要运行脚本的 VM 内打开 VHD 所在的网络共享。

2.  右键单击 VHD，然后选择 "**装载**"。 这会将 VHD 装载到驱动器号。

3.  装入 VHD 后，将打开 "**文件资源管理器**" 窗口。 捕获父文件夹并更新 **$parentFolder**变量

    >[!NOTE]
    >如果看不到父文件夹，这意味着 .MSIX 未正确扩展。 重做上一部分，然后重试。

4.  打开父文件夹。 如果正确扩展，则会看到一个与包同名的文件夹。 更新 **$packageName**变量以匹配此文件夹的名称。

    例如，`VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`。

5.  打开命令提示符并输入**mountvol**。 此命令将显示卷及其 Guid 的列表。 将驱动器号与安装了 VHD 的驱动器的 GUID 复制到步骤2中。

    例如，在此 mountvol 命令的示例输出中，如果已将 VHD 安装到驱动器 C，则需要复制上述值 `C:\` ：

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  用刚才复制的卷 GUID 更新 **$volumeGuid**变量。

7. 打开管理员 PowerShell 提示符并使用适用于你的环境的变量更新以下 PowerShell 脚本。

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-VHD -Path $vhdSrc -NoDriveLetter -ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>注册 PowerShell 脚本

若要运行注册脚本，请运行以下 PowerShell cmdlet，并将占位符值替换为适用于你的环境的值。

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>取消注册 PowerShell 脚本

对于此脚本，请将 **$packageName**的占位符替换为要测试的包的名称。

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>转储 PowerShell 脚本

对于此脚本，请将 **$packageName**的占位符替换为要测试的包的名称。

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>设置 .MSIX 应用附加代理的模拟脚本

创建脚本后，用户可以手动运行这些脚本，或将其设置为以启动、登录、注销和关闭脚本的形式自动运行。 若要了解有关这些类型的脚本的详细信息，请参阅[在组策略中使用启动、关机、登录和注销脚本](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)。

其中每个自动脚本都运行一个应用附加脚本阶段：

- 启动脚本运行阶段脚本。
- 登录脚本运行注册脚本。
- 注销脚本运行取消注册脚本。
- Shutdown 脚本运行 "转储" 脚本。

## <a name="use-packages-offline"></a>脱机使用包

如果你使用的是来自[Microsoft Store For Business](https://businessstore.microsoft.com/)的包，或者你的网络中或未连接到 internet 的设备上的[教育 Microsoft Store](https://educationstore.microsoft.com/) ，则需要从 Microsoft Store 获取包许可证，并将其安装在设备上才能成功运行该应用。 如果设备处于联机状态并且可以连接到 Microsoft Store for Business，则所需的许可证应自动下载，但如果脱机，则需要手动设置许可证。 

若要安装许可证文件，你将需要使用一个 PowerShell 脚本，该脚本在 WMI 桥提供程序中调用 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 类。  

下面介绍如何设置脱机使用的许可证： 

1. 从适用于企业的 Microsoft Store 下载应用程序包、许可证和所需框架。 你需要编码的和未编码的许可证文件。 可在[此处](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)找到详细的下载说明。
2. 请在步骤3：
      1. `$contentID`来自未编码的许可证文件（.xml）的 Id 为值。 可以在所选的文本编辑器中打开许可证文件。
      2. `$licenseBlob`编码的许可证文件（bin）中的许可证 blob 的整个字符串。 可以在所选的文本编辑器中打开编码的许可证文件。 
3. 在管理员 PowerShell 提示符下运行以下脚本。 执行许可证安装的良好位置是在需要从管理员提示符下运行的[过渡脚本](#stage-the-powershell-script)的末尾。

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>后续步骤

目前尚不支持此功能，但你可以向[Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)的社区询问问题。

你还可以在[Windows 虚拟桌面反馈中心](https://aka.ms/MRSFeedbackHub)为 Windows 虚拟桌面提供反馈，或在[.msix 应用附加反馈中心](https://aka.ms/msixappattachfeedback)和[.msix 打包工具反馈中心](https://aka.ms/msixtoolfeedback)为 .msix 应用和打包工具留下反馈。
