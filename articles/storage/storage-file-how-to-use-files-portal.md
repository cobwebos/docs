---
title: "如何在 Azure 门户中管理 Azure 文件存储 | Microsoft Docs"
description: "了解如何使用 Azure 门户管理 Azure 文件存储。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 58353a8058c2722788a38d2ca596061d14b68caf
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---

# 如何在 Azure 门户中使用 Azure 文件存储
<a id="how-to-use-azure-file-storage-from-the-azure-portal" class="xliff"></a>
[Azure 门户](https://portal.azure.com)提供用于管理 Azure 文件存储的用户界面。 你可以从 Web 浏览器中执行以下操作：

* 创建文件共享
* 将文件上传到文件共享和从文件共享下载文件。
* 监视每个文件共享的实际使用情况。
* 调整文件共享大小配额。
* 复制装载命令，用于从 Windows 或 Linux 客户端装载文件共享。

## 创建文件共享
<a id="create-file-share" class="xliff"></a>
1. 登录到 Azure 门户。
2. 在导航菜单中，单击“存储帐户”或“存储帐户(经典)”。
    
    ![演示如何在门户中创建文件共享的屏幕快照](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. 选择存储帐户。

    ![演示如何在门户中创建文件共享的屏幕快照](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. 选择“文件”服务。

    ![演示如何在门户中创建文件共享的屏幕快照](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. 单击“文件共享”，然后单击相应链接创建第一个文件共享。

    ![演示如何在门户中创建文件共享的屏幕快照](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. 填写文件共享名称以及文件共享的大小（最高 5120 GB），以便创建你的第一个文件共享。 创建文件共享之后，即可从任何支持 SMB 2.1 或 SMB 3.0 的文件系统装载它。 可以通过单击文件共享中的“配额”来更改文件大小，最大为 5120 GB。 若要估算使用 Azure 文件存储时的存储费用，请参阅 [Azure 价格计算器](https://azure.microsoft.com/pricing/calculator/)。

    ![演示如何在门户中创建文件共享的屏幕快照](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## 上传和下载文件
<a id="upload-and-download-files" class="xliff"></a>
1. 选择一个已创建的文件共享。

    ![演示如何从门户上传和下载文件的屏幕快照](media/storage-file-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. 单击 **上传** 打开用于文件上传的用户界面。

    ![演示如何从门户上传文件的屏幕快照](media/storage-file-how-to-use-files-portal/use-files-portal-upload-file2.png)

## 连接到文件共享
<a id="connect-to-file-share" class="xliff"></a>
-  单击“连接”获取从 Windows 和 Linux 装载文件共享的命令行。 对于 Linux 用户，你还可以参阅[如何将 Azure 文件存储与 Linux 配合使用](storage-how-to-use-files-linux.md)，了解针对其他 Linux 发行版的更多装载说明。

    ![演示如何装载文件共享的屏幕快照](media/storage-file-how-to-use-files-portal/use-files-portal-connect.png)
-  可以复制在 Windows 或 Linux 上装载文件共享的命令，然后在 Azure VM 或本地计算机中运行。

    ![屏幕截图，显示适用于 Windows 和 Linux 的装载命令](media/storage-file-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

提示：  
若要查找用于装载的存储帐户访问密钥，请单击“连接”页底部的“查看此存储帐户的访问密钥”。

## 另请参阅
<a id="see-also" class="xliff"></a>
请参阅以下链接以获取有关 Azure 文件存储的更多信息。

* [常见问题](storage-files-faq.md)
* [故障排除](storage-troubleshoot-file-connection-problems.md)
