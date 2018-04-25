---
title: 使用 Azure 门户管理 Azure 文件共享
description: 了解如何使用 Azure 门户管理 Azure 文件。
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 9b8a7988be2c3a2622dd45a264abcb674b7d169e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>使用 Azure 门户管理 Azure 文件共享 
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以在 Windows、Linux 和 macOS 中装载 Azure 文件共享。 本指南介绍通过 [Azure 门户](https://portal.azure.com/)来使用 Azure 文件共享的基本知识。 了解如何：

> [!div class="checklist"]
> * 创建资源组和存储帐户
> * 创建 Azure 文件共享 
> * 创建目录
> * 上传文件 
> * 下载文件
> * 创建和使用共享快照

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-storage-account"></a>创建存储帐户
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>创建文件共享
若要创建文件共享，请执行以下操作：

1. 从仪表板中选择存储帐户。
2. 在存储帐户页的“服务”部分，选择“文件”。
    ![存储帐户的服务部分的屏幕截图；选择“文件”服务](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. 在“文件服务”页面顶部的菜单中，单击“+ 文件共享”。 “新建文件共享”页此时会向下拉开。
4. 在“名称”中，键入 *myshare*。
5. 单击“确定”以创建 Azure 文件共享。

## <a name="work-with-the-contents-of-the-azure-file-share"></a>使用 Azure 文件共享的内容
创建 Azure 文件共享以后，即可使用 SMB 在 [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md) 或 [macOS](storage-how-to-use-files-mac.md) 上装载该文件共享。 也可通过 Azure 门户使用 Azure 文件共享。 所有通过 Azure 门户发出的请求都是通过文件 REST API 发出的，因此不需 SMB 访问权限即可在客户端创建、修改和删除文件和目录。

### <a name="create-a-directory"></a>创建目录
若要在 Azure 文件共享的根目录中创建名为 *myDirectory* 的新目录，请执行以下操作：

1. 在“文件服务”页面中，选择“myshare”文件共享。 文件共享的页面此时会打开。
2. 在页面顶部的菜单中选择“+ 添加目录”。 “新建目录”页此时会向下拉开。
3. 键入 *myDirectory*，然后单击“确定”。

### <a name="upload-a-file"></a>上传文件 
若要演示如何上传文件，首先需创建或选择一个需要上传的文件。 可以根据需要采用任何方式来这样做。 选定要上传的文件以后，请执行以下操作：

1. 单击 **myDirectory** 目录。 此时会打开 **myDirectory** 面板。
2. 在顶部菜单中，单击“上传”。 “上传文件”面板此时会打开。  
    ![“上传文件”面板的屏幕截图](media/storage-how-to-use-files-portal/upload-file-1.png)

3. 单击文件夹图标，打开一个用来浏览本地文件的窗口。 
4. 选择一个文件，然后单击“打开”。 
5. 在“上传文件”页中验证文件名，然后单击“上传”。
6. 完成后，文件会显示在 **myDirectory** 页的列表中。

### <a name="download-a-file"></a>下载文件
若要下载已上传文件的副本，可以右键单击该文件。 单击下载按钮后的具体体验取决于所使用的操作系统和浏览器。

## <a name="create-and-modify-share-snapshots"></a>创建和修改共享快照
可以通过 Azure 文件共享执行的另一项有用的任务是创建共享快照。 快照保存的是 Azure 文件共享的某个时间点。 共享快照类似于你可能已经熟悉的操作系统技术，例如：
- 适用于 Windows 文件系统（例如 NTFS 和 ReFS）的[卷影复制服务 (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636)
- 适用于 Linux 系统的[逻辑卷管理器 (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 快照。
- 适用于 macOS 的 [Apple 文件系统 (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 快照。 

若要创建共享快照，请执行以下操作：

1. 若要打开文件共享的页面，请通过仪表板 >“文件” > “myshare”打开存储帐户。 
2. 在文件共享的页面上，单击页面顶部菜单上的“快照”按钮，然后选择“创建快照”。  
    ![演示如何查找“创建快照”按钮的屏幕截图](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>列出并浏览共享快照
创建快照以后，即可再次单击“快照”，然后选择“查看快照”，以便列出共享的快照。 出现的窗格会显示此共享的快照。 单击共享快照即可对其进行浏览。

### <a name="restore-from-a-share-snapshot"></a>从共享快照还原
若要演示如何从共享快照还原某个文件，需先将文件从实时 Azure 文件共享中删除。 导航到 *myDirectory* 文件夹中，右键单击已上传的文件，然后单击“删除”。 然后，若要从共享快照还原该文件，请执行以下操作：

1. 单击顶部菜单中的“快照”，然后选择“查看快照”。 
2. 单击此前创建的快照，然后就会在新页面中打开内容。 
3. 单击快照中的“myDirectory”，此时会看到已删除的文件。 
4. 右键单击删除的文件并选择“还原”。
5. 此时会显示一个弹出窗口，让你选择是以副本方式还原文件，还是覆盖原始文件。 由于已删除原始文件，因此可以选择“覆盖原始文件”，以便将文件还原为删除前的样子。 单击“确定”，将文件还原到 Azure 文件共享。  
    ![“还原文件”对话框的屏幕截图](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. 还原文件以后，关闭快照页面，返回到 **myshare** > **myDirectory**，此时会看到该文件回到了原始位置。

### <a name="delete-a-share-snapshot"></a>删除共享快照
若要删除共享快照，请[导航到共享快照的列表](#list-and-browse-a-share-snapshot)。 单击共享快照名称旁边的复选框，然后选择“删除”按钮。

![删除共享快照的屏幕截图](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>清理资源
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>后续步骤
- [使用 Azure PowerShell 管理文件共享](storage-how-to-use-files-powershell.md)
- [使用 Azure CLI 管理文件共享](storage-how-to-use-files-cli.md)
- [使用 Azure 存储资源管理器管理文件共享](storage-how-to-use-files-storage-explorer.md)
- [规划 Azure 文件部署](storage-files-planning.md)