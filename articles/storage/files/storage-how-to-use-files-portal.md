---
title: "如何在 Azure 门户中管理 Azure 文件存储 | Microsoft Docs"
description: "了解如何使用 Azure 门户管理 Azure 文件存储。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: d5ffa7cff0a31e36f5a96aaa4b2d477fa39333fb
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="how-to-use-azure-file-storage-from-the-azure-portal"></a>如何在 Azure 门户中使用 Azure 文件存储
[Azure 门户](https://portal.azure.com)提供用于管理 Azure 文件存储的用户界面。 可以从 Web 浏览器中执行以下操作：

* 创建文件共享
* 将文件上传到文件共享和从文件共享下载文件。
* 监视每个文件共享的实际使用情况。
* 调整文件共享大小配额。
* 复制装载命令，用于从 Windows 或 Linux 客户端装载文件共享。

## <a name="create-file-share"></a>创建文件共享
1. 登录到 Azure 门户。
2. 在导航菜单中，单击“存储帐户”或“存储帐户(经典)”。
    
    ![演示如何在门户中创建文件共享的屏幕快照](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. 选择存储帐户。

    ![演示如何在门户中创建文件共享的屏幕快照](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. 选择“文件”服务。

    ![演示如何在门户中创建文件共享的屏幕快照](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. 单击“文件共享”，并单击相应链接创建第一个文件共享。

    ![演示如何在门户中创建文件共享的屏幕快照](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. 填写文件共享名称以及文件共享的大小（最高 5120 GB），以便创建第一个文件共享。 创建文件共享之后，即可从任何支持 SMB 2.1 或 SMB 3.0 的文件系统装载它。 可以通过单击文件共享中的“配额”来更改文件大小，最大为 5120 GB。 若要估算使用 Azure 文件存储时的存储费用，请参阅 [Azure 价格计算器](https://azure.microsoft.com/pricing/calculator/)。

    ![演示如何在门户中创建文件共享的屏幕快照](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>上传和下载文件
1. 选择一个已创建的文件共享。

    ![演示如何从门户上传和下载文件的屏幕快照](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. 单击 **上传** 打开用于文件上传的用户界面。

    ![演示如何从门户上传文件的屏幕快照](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>连接到文件共享
-  单击“连接”获取从 Windows 和 Linux 装载文件共享的命令行。 对于 Linux 用户，你还可以参阅[如何将 Azure 文件存储与 Linux 配合使用](../storage-how-to-use-files-linux.md)，了解针对其他 Linux 发行版的更多装载说明。

    ![演示如何装载文件共享的屏幕快照](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  可以复制在 Windows 或 Linux 上装载文件共享的命令，然后在 Azure VM 或本地计算机中运行。

    ![屏幕截图，显示适用于 Windows 和 Linux 的装载命令](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

提示：  
若要查找用于装载的存储帐户访问密钥，请单击“连接”页底部的“查看此存储帐户的访问密钥”。

## <a name="see-also"></a>另请参阅
请参阅以下链接以获取有关 Azure 文件存储的更多信息。

* [常见问题](../storage-files-faq.md)
* [在 Windows 上进行故障排除](storage-troubleshoot-windows-file-connection-problems.md)      
* [在 Linux 上进行故障排除](storage-troubleshoot-linux-file-connection-problems.md)    
