---
title: Windows 虚拟桌面 MSIX 应用附加 - Azure
description: 如何为 Windows 虚拟桌面设置 MSIX 应用附加。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128319"
---
# <a name="set-up-msix-app-attach"></a>设置 MSIX 应用附加

> [!IMPORTANT]
> MSIX 应用附加当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本主题将介绍如何在 Windows 虚拟桌面环境中设置 MSIX 应用附加。

## <a name="requirements"></a>要求

在开始之前，您需要配置 MSIX 应用附加功能：

- 访问 Windows 预览版门户以获取 Windows 10 版本，并支持 MSIX 应用附加 API。
- 正常运行的 Windows 虚拟桌面部署。 有关详细信息，请参阅[在 Windows 虚拟桌面中创建租户](tenant-setup-azure-active-directory.md)。
- MSIX 包装工具
- Windows 虚拟桌面部署中的网络共享，其中将存储 MSIX 包

## <a name="get-the-os-image"></a>获取操作系统映像

首先，您需要获取用于 MSIX 应用的操作系统映像。 要获取操作系统映像，

1. 打开[Windows 预览版门户](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0)并登录。

     >[!NOTE]
     >您必须是 Windows 预览体验程序的成员才能访问 Windows 预览版门户。 要了解有关 Windows 预览版计划的更多信息，请查看我们的[Windows 预览版文档](/windows-insider/at-home/)。

2. 向下滚动到 **"选择版本**"部分，然后选择**Windows 10 预览企业版 （FAST） – 生成 19035**或更高版本。

3. 选择 **"确认**"，然后选择要使用的语言，然后再次选择 **"确认**"。
    
     >[!NOTE]
     >目前，英语是唯一通过该功能测试的语言。 您可以选择其他语言，但它们可能无法按预期显示。
    
4. 生成下载链接时，选择**64 位下载**并将其保存到本地硬盘。

## <a name="prepare-the-vhd-image-for-azure"></a>为 Azure 准备 VHD 映像 

开始之前，您需要创建主 VHD 映像。 如果您尚未创建主 VHD 映像，请转到[准备和自定义主 VHD 映像](set-up-customize-master-image.md)，然后按照说明进行操作。 

创建主 VHD 映像后，必须禁用 MSIX 应用附加应用程序的自动更新。 要禁用自动更新，您需要在提升的命令提示符中运行以下命令：

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

接下来，为 Azure 准备 VM VHD，并将生成的 VHD 磁盘上载到 Azure。 要了解更多信息，请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md)。

将 VHD 上载到 Azure 后，请使用 Azure 应用商店教程按照"[创建主机池](create-host-pools-azure-marketplace.md)"中的说明创建基于此新映像的主机池。

## <a name="prepare-the-application-for-msix-app-attach"></a>准备 MSIX 应用程序附加的应用程序 

如果您已经拥有 MSIX 包，请跳到[配置 Windows 虚拟桌面基础结构](#configure-windows-virtual-desktop-infrastructure)。 如果要测试旧版应用程序，请按照[VM 上的桌面安装程序中的 MSIX 程序包](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的说明将旧应用程序转换为 MSIX 包。

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>为 MSIX 生成 VHD 或 VHDX 封装

包采用 VHD 或 VHDX 格式，以优化性能。 MSIX 需要 VHD 或 VHDX 软件包才能正常工作。

要为 MSIX 生成 VHD 或 VHDX 包，：

1. [下载 msixmgr 工具](https://aka.ms/msixmgr)并将 .zip 文件夹保存到会话主机 VM 中的文件夹。

2. 解压 msixmgr 工具 .zip 文件夹。

3. 将源 MSIX 包放入同一文件夹中，在其中解压缩 msixmgr 工具。

4. 在 PowerShell 中运行以下 cmdlet 以创建 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >确保 VHD 的大小足够大，可以容纳扩展的 MSIX。

5. 运行以下 cmdlet 以装载新创建的 VHD：

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. 运行此 cmdlet 以初始化 VHD：

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. 运行此 cmdlet 以创建新分区：

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. 运行此 cmdlet 以设置分区的格式：

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. 在装载的 VHD 上创建父文件夹。 此步骤是必需的，因为 MSIX 应用附加需要父文件夹。 您可以随心所欲地命名父文件夹。

### <a name="expand-msix"></a>展开 MSIX

之后，您需要通过解压缩来"展开"MSIX 映像。 要解压缩 MSIX 图像，

1. 以管理员身份打开命令提示符，然后导航到下载并解压缩 msixmgr 工具的文件夹。

2. 运行以下 cmdlet 以解压缩 MSIX 到您在上一节中创建和装载的 VHD 中。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    解包完成后，应显示以下消息：

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 如果使用网络中的 Microsoft 商业商店（或教育）中的包，或在未连接到 Internet 的设备上使用包，则需要从应用商店获取包许可证并安装它们才能成功运行应用。 请参阅[脱机使用包](#use-packages-offline)。

3. 导航到装载的 VHD 并打开应用文件夹并确认包内容存在。

4. 卸载 VHD。

## <a name="configure-windows-virtual-desktop-infrastructure"></a>配置 Windows 虚拟桌面基础结构

根据设计，单个 MSIX 扩展包（您在上一节中创建的 VHD）可以在多个会话主机 VM 之间共享，因为 VHD 以只读模式连接。

在开始之前，请确保您的网络共享满足以下要求：

- 共享与 SMB 兼容。
- 属于会话主机池的 VM 对共享具有 NTFS 权限。

### <a name="set-up-an-msix-app-attach-share"></a>设置 MSIX 应用附加共享 

在 Windows 虚拟桌面环境中，创建网络共享并将包移到其中。

>[!NOTE]
> 创建 MSIX 网络共享的最佳做法是使用 NTFS 只读权限设置网络共享。

## <a name="install-certificates"></a>安装证书

如果应用使用的证书不是公共信任的或已自签名的，请介绍安装方式：

1. 右键单击包并选择**属性**。
2. 在显示的窗口中，选择 **"数字签名"** 选项卡。选项卡上的列表中应只有一个项目，如下图所示。 选择该项目以突出显示该项目，然后选择 **"详细信息**"。
3. 当出现数字签名详细信息窗口时，选择 **"常规**"选项卡，然后选择 **"安装证书**"。
4. 安装程序打开时，选择**本地计算机**作为存储位置，然后选择 **"下一步**"。
5. 如果安装程序询问您是否要允许应用对设备进行更改，请选择"**是**"。
6. 选择**将所有证书放在以下存储中**，然后选择 **"浏览**"。
7. 当出现选择证书存储窗口时，选择 **"受信任的人员**"，然后选择 **"确定**"。
8. 选择“完成”****。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>为 MSIX 应用附加准备 PowerShell 脚本

MSIX 应用附加有四个不同的阶段，必须按以下顺序执行：

1. 阶段
2. 注册
3. 注销
4. 舞台

每个阶段创建一个 PowerShell 脚本。 [此处](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)提供了每个阶段的示例脚本。

### <a name="stage-the-powershell-script"></a>暂行电源外壳脚本

在更新 PowerShell 脚本之前，请确保在 VHD 中具有卷 GUID。 要获取卷 GUID：

1.  打开 VHD 位于 VM 中的网络共享，您将在 VM 中运行脚本。

2.  右键单击 VHD 并选择 **"安装**"。 这将将 VHD 安装到驱动器号上。

3.  装载 VHD 后，**文件资源管理器**窗口将打开。 捕获父文件夹并更新 **$parentFolder**变量

    >[!NOTE]
    >如果未看到父文件夹，则表示 MSIX 未正确展开。 请重试上一节。

4.  打开父文件夹。 如果扩展正确，您将看到一个与包同名的文件夹。 更新 **$packageName**变量以匹配此文件夹的名称。

    例如，`VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe` 。

5.  打开命令提示符并进入**安装vol**。 此命令将显示卷及其 GUID 的列表。 复制驱动器号与安装 VHD 的驱动器匹配的卷的 GUID，以执行步骤 2。

    例如，在此示例中，对于 mountvol 命令的输出，如果将 VHD 装载到驱动器 C，则需要复制上面`C:\`的值：

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  使用刚刚复制的卷 GUID 更新 **$volumeGuid**变量。

7. 打开管理 PowerShell 提示符，并使用适用于您的环境的变量更新以下 PowerShell 脚本。

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

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

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

要运行寄存器脚本，请运行以下 PowerShell cmdlet，将占位符值替换为适用于您的环境的值。

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

### <a name="destage-powershell-script"></a>去舞台电源外壳脚本

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>为 MSIX 应用附加代理设置模拟脚本

创建脚本后，用户可以手动运行这些脚本或将它们设置为自动作为启动、登录、注销和关闭脚本运行。 要了解有关这些类型的脚本的更多信息，请参阅[在组策略 中使用启动、关闭、登录和注销脚本](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)。

每个自动脚本都运行应用附加脚本的一个阶段：

- 启动脚本运行阶段脚本。
- 登录脚本运行寄存器脚本。
- 注销脚本运行注销脚本。
- 关闭脚本运行去阶段脚本。

## <a name="use-packages-offline"></a>脱机使用包

如果您使用的是[来自 Microsoft 商业应用商店](https://businessstore.microsoft.com/)或网络中的[Microsoft 教育应用商店](https://educationstore.microsoft.com/)的软件包，或者使用未连接到 Internet 的设备，则需要从 Microsoft 应用商店获取包许可证并将其安装到您的设备上才能成功运行应用。 如果您的设备处于联机状态，并且可以连接到 Microsoft 商业应用商店，则所需的许可证应自动下载，但如果您处于脱机状态，则需要手动设置许可证。 

要安装许可证文件，您需要使用在 WMI 桥接提供程序中调用 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 类的 PowerShell 脚本。  

以下是设置许可证以进行脱机使用的方式： 

1. 从适用于商业的 Microsoft 应用商店下载应用包、许可证和所需框架。 您需要编码和未编码的许可证文件。 详细的下载说明可以[在这里](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)找到。
2. 更新步骤 3 的脚本中的以下变量：
      1. `$contentID`是未编码许可证文件 （.xml） 中的 ContentID 值。 您可以在您选择的文本编辑器中打开许可证文件。
      2. `$licenseBlob`是编码许可证文件 （.bin） 中许可证 Blob 的整个字符串。 您可以在您选择的文本编辑器中打开编码的许可证文件。 
3. 从管理员 PowerShell 提示符运行以下脚本。 执行许可证安装的好地方是在[过渡脚本](#stage-the-powershell-script)的末尾，该脚本也需要从管理员提示符运行。

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

此功能目前不受支持，但您可以在[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)向社区提问。

您还可以在[Windows 虚拟桌面反馈中心](https://aka.ms/MRSFeedbackHub)保留 Windows 虚拟桌面的反馈，或在[MSIX 应用附加反馈中心和](https://aka.ms/msixappattachfeedback) [MSIX 打包工具反馈中心](https://aka.ms/msixtoolfeedback)为 MSIX 应用和打包工具留下反馈。
