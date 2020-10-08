---
title: Windows 虚拟桌面 MSIX 应用附加 - Azure
description: 如何为 Windows 虚拟桌面设置 MSIX 应用附加。
author: Heidilohr
ms.topic: how-to
ms.date: 06/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3b02be8f35ff33f758aebe03c89287c51c9ffef7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816326"
---
# <a name="set-up-msix-app-attach"></a>设置 MSIX 应用附加

> [!IMPORTANT]
> .MSIX 应用附加当前为公共预览版。
> 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本主题将引导你了解如何在 Windows 虚拟桌面环境中设置 MSIX 应用附加。

## <a name="requirements"></a>要求

在开始之前，需要配置 MSIX 应用附加：

- 访问 Windows 预览体验门户以获取支持 MSIX 应用附加 API 的 Windows 10 版本。
- 正常运行的 Windows 虚拟桌面部署。 若要了解如何部署 Windows 虚拟桌面 (经典) ，请参阅 [在 Windows 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。 若要了解如何使用 Azure 资源管理器集成部署 Windows 虚拟桌面，请参阅 [使用 Azure 门户创建主机池](./create-host-pools-azure-marketplace.md)。
- .MSIX 打包工具。
- Windows 虚拟桌面部署中将存储 .MSIX 包的网络共享。

## <a name="get-the-os-image"></a>获取 OS 映像

首先，需要获取 OS 映像。 可以通过 Azure 门户获取 OS 映像。 但是，如果您是 Windows 预览体验计划的成员，则可以选择使用 Windows 预览体验门户。

### <a name="get-the-os-image-from-the-azure-portal"></a>获取 Azure 门户中的 OS 映像

若要从 Azure 门户获取 OS 映像：

1. 打开 [Azure 门户](https://portal.azure.com) 并登录。

2. 请参阅 **创建虚拟机**。

3. 在 " **基本** " 选项卡中，选择 **"Windows 10 企业多会话，版本 2004"**。

4. 按照说明的其余部分操作来完成虚拟机的创建。

     >[!NOTE]
     >可以使用此 VM 直接测试 .MSIX 应用附加。 若要了解详细信息，请跳到 [生成用于 .msix 的 VHD 或 VHDX 包](#generate-a-vhd-or-vhdx-package-for-msix)。 否则，请继续阅读本部分。

### <a name="get-the-os-image-from-the-windows-insider-portal"></a>从 Windows 预览体验门户获取 OS 映像

若要从 Windows 预览体验门户获取 OS 映像，请执行以下操作：

1. 打开 [Windows 预览体验成员门户](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0)并登录。

     >[!NOTE]
     >你必须是 Windows 预览体验成员计划的成员才能访问 Windows 预览体验成员门户。 若要了解有关 Windows 预览成员体验计划的更多信息，请查看 [Windows 预览体验成员文档](/windows-insider/at-home/)。

2. 向下滚动到“选择版本”部分，然后选择“Windows 10 预览体验预览企业(快速) - 版本 19041”或更高版本 。

3. 选择“确认”，然后选择要使用的语言，然后再次选择“确认” 。

     >[!NOTE]
     >目前仅对该项功能执行了英文测试。 你可以选择其他语言，但是其他语言的显示情况可能与预期不符。

4. 生成下载链接后，选择“64 位下载”并将其保存到本地硬盘。

## <a name="prepare-the-vhd-image-for-azure"></a>准备 Azure 的 VHD 映像

接下来，需要创建一个主 VHD 映像。 如果尚未创建主 VHD 映像，请转到[准备和自定义主 VHD 映像](set-up-customize-master-image.md)，然后按照此处的说明进行操作。

创建主 VHD 映像后，必须禁用 MSIX 应用附加应用程序的自动更新。 若要禁用自动更新，需要在提升的命令提示符中运行以下命令：

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

禁用自动更新后，你必须启用 Hyper-v，因为你将使用装入 VHD 命令来暂存和卸载 VHD 到 "转储"。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
>[!NOTE]
>此更改将需要你重新启动虚拟机。

接下来，为 Azure 准备 VM VHD 并将生成的 VHD 磁盘上载到 Azure。 若要了解更多详细信息，请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md)。

将 VHD 上传到 Azure 后，请按照[通过使用 Azure 市场创建主机池](create-host-pools-azure-marketplace.md)教程中的说明创建基于此新映像的主机池。

## <a name="prepare-the-application-for-msix-app-attach"></a>准备 MSIX 应用附加的应用程序

如果已经有 MSIX 包，请跳到[配置 Windows 虚拟桌面基础结构](#configure-windows-virtual-desktop-infrastructure)。 如果要测试旧版应用程序，请按照[通过 VM 上的桌面安装程序创建 MSIX 包](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的说明将旧版应用程序转换为 MSIX 包。

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>为 MSIX 生成 VHD 或 VHDX 包

包采用 VHD 或 VHDX 格式以优化性能。 MSIX 需要使用 VHD 或 VHDX 包才能正常工作。

为 MSIX 生成 VHD 或 VHDX 包：

1. [下载 msixmgr 工具](https://aka.ms/msixmgr)，并将 .zip 文件夹保存到会话主机 VM 中的文件夹中。

2. 解压缩 msixmgr 工具 .zip 文件夹。

3. 将源 MSIX 包放入解压缩的 msixmgr 工具的同一文件夹中。

4. 在 PowerShell 中运行以下 cmdlet 以创建 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >确保 VHD 的大小足够容纳扩展的 MSIX。*

5. 运行以下 cmdlet 以装载新创建的 VHD：

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. 运行此 cmdlet 初始化 VHD：

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. 运行此 cmdlet 可创建新分区：

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. 运行此 cmdlet 可设置分区的格式：

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. 在装载的 VHD 上创建父文件夹。 此步骤是必需的，因为 MSIX 应用附加需要父文件夹。 你可以按照自己的喜好为父文件夹命名。

### <a name="expand-msix"></a>展开 MSIX

在那之后，你需要通过解压缩包来“扩展”MSIX 映像。 解压缩 MSIX 映像：

1. 以管理员身份打开命令提示符并导航到下载并解压缩 msixmgr 工具的文件夹。

2. 运行以下 cmdlet 将 MSIX 解压到上一部分中创建并装载的 VHD 中。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    解包完成后，应显示以下消息：

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 若通过自己的网络或未连接到 Internet 的设备使用适用于企业（或教育）的 Microsoft Store，则需要从 Store 获取包许可证并安装它们才能成功运行应用。 请参阅[脱机使用包](#use-packages-offline)。

3. 导航到已装载的 VHD 并打开应用文件夹，确认存在包内容。

4. 卸载 VHD。

## <a name="configure-windows-virtual-desktop-infrastructure"></a>配置 Windows 虚拟桌面基础结构

从设计上讲，可以在多个会话主机 VM 之间共享单个 MSIX 扩展包（在上一节中创建的 VHD），因为 VHD 是以只读模式附加的。

开始之前，请确保自己的网络共享满足以下要求：

- 共享与 SMB 兼容。
- 作为会话主机池一部分，VM 对共享具有 NTFS 权限。

### <a name="set-up-an-msix-app-attach-share"></a>设置 MSIX 应用附加共享

在 Windows 虚拟桌面环境中，创建网络共享并将包移到该处。

>[!NOTE]
> 创建 MSIX 网络共享的最佳做法是设置具有 NTFS 只读权限的网络共享。

## <a name="install-certificates"></a>安装证书

如果你的应用使用的证书不是受公共信任的或是自签名的，请按以下方法安装证书：

1. 右键单击该包，选择“属性”。
2. 在出现的窗口中，选择“数字签名”选项卡。选项卡上的列表中应该只有一个项，如下图所示。 选择该项以突出显示该项，然后选择“详细信息”。
3. 当 "数字签名详细信息" 窗口出现时，选择 " **常规** " 选项卡，然后选择 " **查看证书**"，然后选择 " **安装证书**"。
4. 当安装程序打开时，选择“本地计算机”作为存储位置，然后选择“下一步” 。
5. 如果安装程序询问你是否允许应用对设备进行更改，请选择“是”。
6. 选择“将所有证书放入以下存储区”，然后选择“浏览” 。
7. 当“选择证书存储”窗口出现时，选择“受信任的人员”，然后选择“确定” 。
8. 选择 " **下一步** " 和 " **完成**"。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>为 MSIX 应用附加准备 PowerShell 脚本

MSIX 应用附加有四个不同的阶段，必须按以下顺序执行：

1. 阶段
2. 注册
3. 取消注册
4. 转储

每个阶段都会创建一个 PowerShell 脚本。 可在[此处](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)查看适用于每个阶段的示例脚本。

### <a name="stage-powershell-script"></a>暂存 PowerShell 脚本

在更新 PowerShell 脚本之前，请确保在 VHD 中具有卷的卷 GUID。 获取卷 GUID：

1.  打开在运行脚本的 VM 中 VHD 所在的网络共享。

2.  右键单击 VHD 并选择“装载”。 这会将 VHD 装载到驱动器号。

3.  装载 VHD 后，“文件资源管理器”窗口将打开。 找到父文件夹并更新“$parentFolder”变量

    >[!NOTE]
    >若找不到父文件夹，则表示 MSIX 未正确展开。 重复上一部分中的步骤，然后重试。

4.  打开父文件夹。 如果正确展开，将看到与包同名的文件夹。 更新“$packageName”变量以匹配此文件夹的名称。

    例如，`VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe` 。

5.  打开命令提示并输入“mountvol”。 此命令将显示卷及其 GUID 的列表。 复制驱动器号与在步骤 2 中装载 VHD 的驱动器匹配的卷的 GUID。

    例如，在此 mountvol 命令的输出示例中，如果将 VHD 安装到驱动器 C，则需要复制上面的值 `C:\`：

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  用刚才复制的卷 GUID 更新“$volumeGuid”变量。

7. 打开管理员 PowerShell 提示符并使用应用于环境的变量更新以下 PowerShell 脚本。

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
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>注册 PowerShell 脚本

若要运行注册表脚本，请运行以下 PowerShell cmdlet，将其中占位符值替换为应用于环境的值。

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

对于此脚本，将“$packageName”的占位符替换为要测试的包的名称。

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

对于此脚本，将“$packageName”的占位符替换为要测试的包的名称。 在生产部署中，最好是在关闭时运行它。

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>为 MSIX 应用附加代理设置模拟脚本

创建脚本之后，用户可以手动运行它们，或者将其设置为自动运行的启动、登录、注销和关闭脚本。 若要了解有关这些类型脚本的详细信息，请参阅[在组策略中使用启动、关闭、登录和注销脚本](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)。

每个自动脚本都运行应用附加脚本的一个阶段：

- 启动脚本运行暂存脚本。
- 登录脚本运行注册脚本。
- 注销脚本运行取消注册脚本。
- 关闭脚本运行转储脚本。

## <a name="use-packages-offline"></a>脱机使用包

如果通过网络中或未连接到 Internet 的设备使用[适用于企业的 Microsoft Store](https://businessstore.microsoft.com/) 或[适用于教育的 Microsoft Store](https://educationstore.microsoft.com/) 中的包，则需要从 Microsoft Store 获取包许可证并将其安装到设备上，才能成功运行该应用。 如果设备处于联机状态并且可以连接到适用于企业的 Microsoft Store，就会自动下载所需的许可证，但如果处于脱机状态，则需要手动设置许可证。

若要安装许可证文件，需要使用 PowerShell 脚本来调用 WMI Bridge 提供程序中的 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 类。

下面介绍如何设置脱机使用的许可证：

1. 从适用于企业的 Microsoft Store 下载应用包、许可证和必需的框架。 你需要编码和未编码的许可证文件。 可在[此处](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)找到详细的下载说明。
2. 在步骤 3 的脚本中更新以下变量：
      1. `$contentID` 是未编码的许可证文件 (.xml) 中的 ContentID 值。 可以在所选的文本编辑器中打开许可证文件。
      2. `$licenseBlob` 是已编码的许可证文件 (.bin) 中许可证 blob 的整个字符串。 可以在所选的文本编辑器中打开已编码的许可证文件。
3. 在管理员 PowerShell 提示符中运行以下脚本。 建议在[暂存脚本](#stage-powershell-script)的末尾执行许可证安装，该脚本也需要在管理员提示符中运行。

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

当前不支持此功能，但可以在 [Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 处向社区提问。

还可以在 [Windows 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Windows 虚拟桌面的反馈。
