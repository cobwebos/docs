---
title: Azure 实验室服务-将自定义映像上传到共享映像库
description: 描述如何将自定义映像上传到共享映像库。 大学 IT 部门将发现导入图像特别有用。
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: cd701215eb375b7f9b867ba05082afc7ed348ff7
ms.sourcegitcommit: 5b69ba21787c07547edfbfd5254eaf34315cfadd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712385"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>将自定义映像上传到共享映像库

共享映像库可用于导入自己的自定义映像，以便在 Azure 实验室服务中创建实验室。 大学 IT 部门会发现导入映像的操作特别有用，原因如下： 

* 无需使用实验室模板 VM 来手动创建映像。
* 你可以上载使用其他工具（如 SCCM、终结点管理器等）创建的映像。

本文介绍了在 Azure 实验室服务中使用自定义映像并将其使用的步骤。 

> [!IMPORTANT]
> 将映像从物理实验室环境移到 Az Labs 时，需要将其重新构建为 appropriatly。 不要只是从物理实验室重复使用现有映像。 <br/>有关详细信息，请参阅 [从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) 博客文章。

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>从物理实验室环境引入自定义映像

以下步骤说明如何导入从物理实验室环境启动的自定义映像。 然后，将从此环境创建 VHD 并将其导入到 Azure 中的共享映像库，以便在 Azure 实验室服务中使用。

有许多选项用于从物理实验室环境创建 VHD。 以下步骤演示了如何从 Windows Hyper-v VM 创建 VHD：

1. 从你的映像中创建的物理实验室环境中的 Hyper-v VM 开始。
    1. VM 必须创建为第1代 VM。
    1. VM 必须使用固定磁盘大小。 你还可以在此窗口中指定磁盘的大小。 磁盘大小不能超过 128 GB。<br/>    
    Azure 实验室服务不支持磁盘大小为 128 GB > 映像。 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="连接虚拟硬盘&quot;:::   
    1. 像往常一样为 VM 映像。
1. [连接到 VM 并为 Azure 做好准备](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)。
    1. [设置 Azure 的 Windows 配置](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure)
    1. [检查至少需要确保 VM 连接的 Windows 服务](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [更新远程桌面注册表设置](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [配置 Windows 防火墙规则](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. 安装 Windows 更新
    1. [安装 Azure VM 代理和其他配置，如下所示](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    以上步骤会创建专用映像。 如果创建通用化映像，还需要运行 [SysPrep](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep)。 <br/>
        如果要维护用户目录 (包含映像中包含的软件所需的文件、用户帐户信息等 ) ，则应创建专用映像。
1. 由于 **hyper-v** 默认创建 **VHDX** 文件，因此需要将其转换为 VHD 文件。
    1. 导航到 &quot; **hyper-v 管理器**  ->  **操作**&quot; "  ->  **编辑磁盘**"。
    1. 在这里，你可以选择将磁盘从 VHDX **转换** 为 VHD
    1. 尝试扩展磁盘大小时，请确保不要超过 128 GB。        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="连接虚拟硬盘&quot;:::   
    1. 像往常一样为 VM 映像。
1. [连接到 VM 并为 Azure 做好准备](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)。
    1. [设置 Azure 的 Windows 配置](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure)
    1. [检查至少需要确保 VM 连接的 Windows 服务](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [更新远程桌面注册表设置](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [配置 Windows 防火墙规则](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. 安装 Windows 更新
    1. [安装 Azure VM 代理和其他配置，如下所示](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    以上步骤会创建专用映像。 如果创建通用化映像，还需要运行 [SysPrep](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep)。 <br/>
        如果要维护用户目录 (包含映像中包含的软件所需的文件、用户帐户信息等 ) ，则应创建专用映像。
1. 由于 **hyper-v** 默认创建 **VHDX** 文件，因此需要将其转换为 VHD 文件。
    1. 导航到 &quot; **hyper-v 管理器**  ->  **操作**&quot; " 选项卡选择您的磁盘大小。 如前所述，大小必须不 > 128 GB。
1. 拍摄托管磁盘的快照。
    这可以通过 PowerShell、使用 Azure 门户或存储资源管理器中完成，如 [使用门户或 PowerShell 创建快照](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)中所述。
1. 在共享映像库中，创建映像定义和版本：
    1. [创建映像定义](https://docs.microsoft.com/azure/virtual-machines/windows/shared-images-portal#create-an-image-definition)。
    1. 还需要在此处指定是否创建专用或通用化映像。
1. 在 Azure 实验室服务中创建实验室，并从共享映像库中选择自定义映像。

    如果在原始 Hyper-v VM 上安装操作系统后扩展了磁盘，则还需要在 Windows 中扩展 C 驱动器才能使用未分配的磁盘空间。 为此，请在创建实验室后登录到模板 VM，然后执行与 [扩展基本卷](https://docs.microsoft.com/windows-server/storage/disk-management/extend-a-basic-volume)中所示内容类似的步骤。 可以通过 UI 以及使用 PowerShell 来执行此操作。

## <a name="next-steps"></a>后续步骤

* [共享映像库概述](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
* [如何使用共享映像库](how-to-use-shared-image-gallery.md)
