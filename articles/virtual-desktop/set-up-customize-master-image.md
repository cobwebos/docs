---
title: 准备和自定义主 VHD 映像 - Azure
description: 如何准备、自定义 Windows 虚拟桌面主映像并将其上载到 Azure。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127727"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>准备和自定义主 VHD 映像

本文介绍如何准备用于上载到 Azure 的主虚拟硬盘 （VHD） 映像，包括如何创建虚拟机 （VM） 并在其中安装软件。 这些说明适用于可与组织的现有过程配合使用的特定于 Windows 虚拟桌面的配置。

## <a name="create-a-vm"></a>创建 VM

Windows 10 企业多会话在 Azure 映像库中可用。 自定义此映像有两个选项。

第一个选项是按照[从托管映像创建 VM](../virtual-machines/windows/create-vm-generalized-managed.md)中的说明在 Azure 中预配虚拟机 （VM），然后跳到[软件准备和安装](set-up-customize-master-image.md#software-preparation-and-installation)。

第二个选项是通过下载映像、预配 Hyper-V VM 和自定义映像以满足您的需要在本地创建映像，我们将在下一节中介绍。

### <a name="local-image-creation"></a>本地映像创建

将映像下载到本地位置后，打开**Hyper-V 管理器**以创建具有复制的 VHD 的 VM。 以下说明是一个简单的版本，但在[Hyper-V 中创建虚拟机中](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)可以找到更详细的说明。

要使用复制的 VHD 创建 VM，请：

1. 打开**新的虚拟机向导**。

2. 在"指定生成"页上，选择**第 1 代**。

    ![指定生成页的屏幕截图。 选择"第 1 代"选项。](media/a41174fd41302a181e46385e1e701975.png)

3. 在"检查点类型"下，通过取消选中复选框来禁用检查点。

    ![检查点页面的检查点类型部分的屏幕截图。](media/20c6dda51d7cafef33251188ae1c0c6a.png)

您还可以在 PowerShell 中运行以下 cmdlet 以禁用检查点。

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>固定磁盘

如果从现有 VHD 创建 VM，默认情况下会创建动态磁盘。 可以通过选择 **"编辑磁盘..."** 将其更改为固定磁盘， 如下图所示。 有关更详细的说明，请参阅准备[Windows VHD 或 VHDX 上载到 Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。

!["编辑磁盘"选项的屏幕截图。](media/35772414b5a0f81f06f54065561d1414.png)

您还可以运行以下 PowerShell cmdlet 以将磁盘更改为固定磁盘。

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>软件准备和安装

本节介绍如何准备和安装 FSLogix 和 Windows Defender，以及应用和映像注册表的一些基本配置选项。 

如果要在 VM 上安装 Office 365 ProPlus 和 OneDrive，请转到[主 VHD 映像上的安装 Office，](install-office-on-wvd-master-image.md)然后按照说明安装应用。 完成后，返回到本文。

如果用户需要访问某些 LOB 应用程序，我们建议您在完成本节说明后安装它们。

### <a name="set-up-user-profile-container-fslogix"></a>设置用户配置文件容器 （FSLogix）

要将 FSLogix 容器包含在映像中，请按照[使用文件共享 为主机池创建配置文件容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)的说明进行操作。 您可以使用[此快速入门](/fslogix/configure-cloud-cache-tutorial/)测试 FSLogix 容器的功能。

### <a name="configure-windows-defender"></a>配置 Windows 防御器

如果在 VM 中配置了 Windows Defender，请确保它在附件期间未扫描 VHD 和 VHDX 文件的全部内容。

此配置仅在附件期间删除 VHD 和 VHDX 文件的扫描，但不会影响实时扫描。

有关如何在 Windows 服务器上配置 Windows Defender 的更多详细信息，请参阅[在 Windows 服务器上配置 Windows Defender 防病毒排除](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)项 。

要了解有关如何配置 Windows Defender 以从扫描中排除某些文件，请参阅[根据文件扩展名和文件夹位置配置和验证排除项](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)。

### <a name="disable-automatic-updates"></a>禁用自动更新

要通过本地组策略禁用自动更新，请：

1. 打开**本地组策略编辑器\\管理模板\\Windows\\组件 Windows 更新**。
2. 右键单击 **"配置自动更新**"并将其设置为 **"已禁用**"。

您还可以在命令提示符上运行以下命令以禁用自动更新。

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>为 Windows 10 电脑指定"开始"布局（可选）

运行此命令以为 Windows 10 电脑指定"开始"布局。

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>设置时区重定向

可以在组策略级别强制执行时区重定向，因为主机池中的所有 VM 都是同一安全组的一部分。

要重定向时区：

1. 在活动目录服务器上，打开**组策略管理控制台**。
2. 展开域和组策略对象。
3. 右键单击为组策略设置创建的**组策略对象**，然后选择"**编辑**"。
4. 在**组策略管理编辑器**中，导航到**计算机配置** > **策略** > **管理模板** > **Windows 组件** > **远程桌面服务** > **远程桌面会话主机** > **设备和资源重定向**。
5. 启用 **"允许时区重定向**"设置。

您还可以在主映像上运行此命令以重定向时区：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>禁用存储感知

对于使用 Windows 10 企业版或 Windows 10 企业多会话的 Windows 虚拟桌面会话主机，我们建议禁用存储感知。 您可以在 **"存储**"下的"设置"菜单中禁用"存储感知"，如下图所示：

!["设置"下的存储菜单的屏幕截图。 "存储感知"选项已关闭。](media/storagesense.png)

还可以通过运行以下命令来更改注册表设置：

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>包括其他语言支持

本文不介绍如何配置语言和区域支持。 有关详细信息，请参阅以下文章：

- [将语言添加到 Windows 映像](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [按需功能](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [按需语言和区域功能 （FOD）](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>其他应用程序和注册表配置

本节介绍应用程序和操作系统配置。 本节中的所有配置都通过注册表项完成，注册表项可通过命令行和注册工具执行。

>[!NOTE]
>您可以使用组策略对象 （GPO） 或注册表导入实现配置中的最佳做法。 管理员可以根据组织的要求选择任一选项。

有关 Windows 10 企业多会话上遥测数据的反馈中心集合，运行此命令：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

运行以下命令以修复 Watson 崩溃：

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

在注册表编辑器中输入以下命令以修复 5k 分辨率支持。 必须先运行命令，然后才能启用并行堆栈。

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>准备映像以上载到 Azure

完成配置并安装所有应用程序后，请按照[准备 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md)中的说明上载到 Azure 以准备映像。

准备要上载的图像后，请确保 VM 保持关闭或交易位置状态。

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>将主映像上载到 Azure 中的存储帐户

此部分仅适用于在本地创建主映像时。

以下说明将告诉您如何将主映像上载到 Azure 存储帐户。 如果还没有 Azure 存储帐户，请按照[本文](/azure/javascript/tutorial-vscode-static-website-node-03)中的说明创建一个帐户。

1. 如果尚未修复，则将 VM 映像 （VHD） 转换为"已修复"。 如果不将图像转换为"已修复"，则无法成功创建图像。

2. 将 VHD 上载到存储帐户中的 Blob 容器。 您可以使用[存储资源管理器工具](https://azure.microsoft.com/features/storage-explorer/)快速上传。 要了解有关存储资源管理器工具的更多信息，请参阅[本文](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

    ![微软 Azure 存储资源管理器工具的搜索窗口的屏幕截图。 选中"将 .vhd 或 vhdx 文件作为页面 blob（推荐）"复选框选中。](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 接下来，转到浏览器中的 Azure 门户并搜索"图像"。 您的搜索应引导您访问 **"创建图像**"页，如以下屏幕截图所示：

    ![Azure 门户的"创建图像"页的屏幕截图，其中填充了图像的示例值。](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 创建映像后，您应该会看到如下屏幕截图中的通知：

    !["已成功创建的图像"通知的屏幕截图。](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>后续步骤

现在，您有了映像，就可以创建或更新主机池。 要了解有关如何创建和更新主机池的详细信息，请参阅以下文章：

- [使用 Azure 资源管理器模板创建主机池](create-host-pools-arm-template.md)
- [教程：使用 Azure 应用商店创建主机池](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 创建主机池](create-host-pools-powershell.md)
- [使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)
- [配置 Windows 虚拟桌面负载均衡方法](configure-host-pool-load-balancing.md)
