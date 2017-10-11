---
title: "使用 AzCopy 将 VHD 文件上传到 Azure 开发测试实验室 | Microsoft Docs"
description: "使用 AzCopy 将 VHD 文件上传到实验室的存储帐户"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: a4f43354740d9f17570932b0b9c753f46d67dc33
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>使用 AzCopy 将 VHD 文件上传到实验室的存储帐户

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

在 Azure 开发测试实验室中，可使用 VHD 文件创建用于预配虚拟机的自定义映像。 以下步骤将引导使用 AzCopy 命令行实用工具将 VHD 文件上传到实验室的存储帐户。 上传 VHD 文件后，[后续步骤部分](#next-steps)将列出一些文章说明如何基于已上传的 VHD 文件创建自定义映像。 若要深入了解 Azure 中的磁盘和 VHD，请参阅[关于虚拟机的磁盘和 VHD](../virtual-machines/linux/about-disks-and-vhds.md)

> [!NOTE] 
>  
> AzCopy 是一个仅限 Windows 的命令行实用工具。

## <a name="step-by-step-instructions"></a>分步说明

以下步骤将引导使用 [AzCopy](http://aka.ms/downloadazcopy) 将 VHD 文件上传到 Azure 开发测试实验室。 

1. 使用 Azure 门户获取实验室的存储帐户名称：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“更多服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表中，选择所需的实验室。  

1. 在实验室的边栏选项卡，选择“配置”。 

1. 在实验室的“配置”边栏选项卡上，选择“自定义映像(VHD)”。

1. 在“自定义映像”边栏选项卡上，选择“+添加”。 

1. 在“自定义映像”边栏选项卡上，选择“VHD”。

1. 在“VHD”边栏选项卡上，选择“使用 PowerShell 上传 VHD”。

    ![使用 PowerShell 上传 VHD](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. “使用 PowerShell 上传映像”边栏选项卡显示对 **Add-AzureVhd** cmdlet 的调用。 第一个参数 (Destination) 包含采用以下格式的 blob 容器 (uploads) 的 URI：

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. 请记下完整的 URI，因为会在后续步骤中使用。

1. 使用 AzCopy 上传 VHD 文件：
 
1. [下载并安装最新版本的 AzCopy](http://aka.ms/downloadazcopy)。

1. 打开一个命令窗口，导航到 AzCopy 安装目录。 还可以选择将 AzCopy 安装位置添加到系统路径。 AzCopy 默认安装到以下目录：

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. 使用存储帐户密钥和 blob 容器 URI，在命令提示符处运行以下命令。 *VhdFileName* 值需要用引号引起来。 VHD 文件的上传过程可能耗时较长，具体取决于 VHD 文件的大小和连接速度。   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户，在 Azure 开发测试实验室中基于 VHD 文件创建自定义映像](devtest-lab-create-template.md)
- [通过 PowerShell，在 Azure 开发测试实验室中基于 VHD 文件创建自定义映像](devtest-lab-create-custom-image-from-vhd-using-powershell.md)