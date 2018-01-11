---
title: "使用 Microsoft Azure 存储资源管理器将 VHD 文件上传到 Azure 开发测试实验室 | Microsoft Docs"
description: "使用 Microsoft Azure 存储资源管理器将 VHD 文件上传到实验室的存储帐户"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: 25675aae77fbe2610fe416210de9a306c1c09f3d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>使用 Microsoft Azure 存储资源管理器将 VHD 文件上传到实验室的存储帐户

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

在 Azure 开发测试实验室中，可使用 VHD 文件创建用于预配虚拟机的自定义映像。 本文演示如何使用 [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)将 VHD 文件上传到实验室的存储帐户。 上传 VHD 文件后，[后续步骤部分](#next-steps)将列出一些文章说明如何基于已上传的 VHD 文件创建自定义映像。 有关 Azure 中的磁盘和 VHD 的详细信息，请参阅 [About disks and VHDs for virtual machines](../virtual-machines/linux/about-disks-and-vhds.md)（关于虚拟机的磁盘和 VHD）

## <a name="step-by-step-instructions"></a>分步说明

以下步骤将引导完成使用 [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)将 VHD 文件上传到开发测试实验室。

1. [下载并安装最新版本的 Microsoft Azure 存储资源管理器](http://www.storageexplorer.com)。

1. 使用 Azure 门户获取实验室的存储帐户名称：

    1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
    
    1. 选择“更多服务”，并从列表中选择“开发测试实验室”。
    
    1. 从实验室列表，选择所需的实验室。  
    
    1. 在实验室的边栏选项卡，选择“配置”。 
    
    1. 在实验室的“配置”边栏选项卡上，选择“自定义映像(VHD)”。
    
    1. 在“自定义映像”边栏选项卡上，选择“+添加”。 
    
    1. 在“自定义映像”边栏选项卡上，选择“VHD”。
    
    1. 在“VHD”边栏选项卡上，选择“使用 PowerShell 上传 VHD”。
    
        ![使用 PowerShell 上传 VHD][0]
    
    1. “使用 PowerShell 上传映像”边栏选项卡显示对 Add-AzureVhd cmdlet 的调用。 第一个参数 (*Destination*) 包含采用以下格式的实验室存储帐户名称：
    
        https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/... 

    1. 请记下存储帐户名称，因为会在后续步骤中使用它。
    
1. 使用存储资源管理器连接到 Azure 订阅帐户。

    > [!TIP] 
    > 
    > 存储资源管理器支持多个连接选项。 本部分说明如何连接到与 Azure 订阅关联的存储帐户。 若要查看存储资源管理器支持的其他连接选项，请参阅[存储资源管理器入门](../vs-azure-tools-storage-manage-with-storage-explorer.md)一文。
 
    1. 打开存储资源管理器。
    
    1. 在存储资源管理器中，选择“Azure 帐户设置”。 
    
        ![Azure 帐户设置][1]
    
    1. 左窗格会显示已登录的 Microsoft 帐户。 要连接到另一个帐户，请选择“添加帐户”，并按对话框中的说明，使用至少与一个活动 Azure 订阅相关联的 Microsoft 帐户登录。
    
        ![添加帐户][2]
    
    1. 使用 Microsoft 帐户成功登录后，左窗格将填充与该帐户关联的 Azure 订阅。 选择要使用的 Azure 订阅，并选择“应用”。 （选择“所有订阅”会切换选择所有列出的 Azure 订阅，或者一个都不选。）
    
        ![选择 Azure 订阅][3]
    
    1. 左窗格会显示与所选 Azure 订阅关联的存储帐户。
    
        ![选择的 Azure 订阅][4]

1. 找到实验室的存储帐户：

    1. 在存储资源管理器的左窗格中，找到并展开拥有实验室的 Azure 订阅的节点。
    
    1. 在订阅的节点下展开“存储帐户”。

    1. 展开实验室的存储帐户节点，以显示“Blob 容器”、“文件共享”、“队列”和“表”的节点。
    
    1. 展开“Blob 容器”节点。
    
    1. 选择“上传 blob 容器”，在右窗格中显示其内容。
        
        ![“上传”目录][5]

1. 使用存储资源管理器上传 VHD 文件：

    1. 在存储资源管理器的右窗格中，应看到实验室存储帐户的“上 载”blob 容器中的 blob 列表。 在 blob 编辑器工具栏上，选择“上传” 
        
        ![“上传”按钮][6]
    
    1. 在“上传”下拉菜单上，选择“上传文件...”.
    
    1. 在“上传文件”对话框中，选择省略号。
        
        ![选择文件][8]  

    1. 在“选择要上载的文件”对话框中，浏览到所需的 VHD 文件，选择它，并选择“打开”。
    
    1. 返回到“上传文件”对话框时，将“Blob 类型”更改为“页 Blob”。
    
    1. 选择“上传”。

        ![选择文件][9]  
    
    1. 存储资源管理器的“活动日志”窗格会显示下载状态（以及用于取消上传的链接）。 上传 VHD 文件的过程可能耗时较长，具体取决于 VHD 文件的大小和连接速度。 

        ![上传文件状态][10]  

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户，在 Azure 开发测试实验室中基于 VHD 文件创建自定义映像](devtest-lab-create-template.md)
- [通过 PowerShell，在 Azure 开发测试实验室中基于 VHD 文件创建自定义映像](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
