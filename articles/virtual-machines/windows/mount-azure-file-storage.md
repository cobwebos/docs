---
title: "从 Azure Windows VM 装载 Azure 文件存储 | Microsoft Docs"
description: "使用 Azure 文件存储在云中存储文件，并从 Azure 虚拟机 (VM) 中装载你的云文件共享。"
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: 6ffb2d2da1e2439df6f5da543411e3c2c68d3435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-file-shares-with-windows-vms"></a>通过 Windows VM 使用 Azure 文件共享 

可以使用 Azure 文件共享的方式存储和访问 VM 中的文件。 例如，可以存储希望所有 VM 共享的脚本或应用程序配置文件。 本主题介绍如何创建和装载 Azure 文件共享，以及如何上传和下载文件。

## <a name="connect-to-a-file-share-from-a-vm"></a>从 VM 连接到文件共享

本部分假设你已有想要连接到的文件共享。 如果需要创建一个，请参阅本主题后面的[创建文件共享](#create-a-file-share)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左边菜单中单击“存储帐户”。
3. 选择存储帐户。
4. 在“概述”页的“服务”下，选择“文件”。
5. 选择文件共享。
6. 单击“连接”，打开显示从 Windows 或 Linux 装载文件共享的命令行语法的页面。
7. 突出显示该命令语法，并将其粘贴到记事本或其他可以轻松访问的位置。 
8. 编辑该语法，删除前导 * * > * * 并将[驱动器号]替换为想要装载文件共享的驱动器号（例如，Y:）。
8. 连接到你的 VM，然后打开命令提示符。
9. 粘贴已编辑的连接语法并点击 "Enter"。
10. 当连接已创建时，会收到“命令已成功完成”的消息。
11. 键入驱动器号切换至该驱动器，然后键入 "dir" 查看文件共享的内容，以此方法来检查连接。



## <a name="create-a-file-share"></a>创建文件共享 
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左边菜单中单击“存储帐户”。
3. 选择存储帐户。
4. 在“概述”页的“服务”下，选择“文件”。
5. 在“文件服务”页中，单击“+ 文件共享”以创建你的第一个文件共享。\
6. 填写文件共享名。 文件共享名可使用小写字母、数字和单个连字符。 名称不能以连字符开头，并且不能使用多个连续的连字符。 
7. 填写文件大小限制，最大为 5120 GB。
8. 单击“确定”部署该文件共享。
   
## <a name="upload-files"></a>上传文件
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左边菜单中单击“存储帐户”。
3. 选择存储帐户。
4. 在“概述”页的“服务”下，选择“文件”。
5. 选择文件共享。
6. 单击“上传”以打开“上传文件”页。
7. 单击文件夹图标浏览本地文件系统以上传文件。   
8. 单击“上传”以上传文件至文件共享。

## <a name="download-files"></a>下载文件
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左边菜单中单击“存储帐户”。
3. 选择存储帐户。
4. 在“概述”页的“服务”下，选择“文件”。
5. 选择文件共享。
6. 右键单击文件，然后选择“下载”将其下载到本地计算机。
   

## <a name="next-steps"></a>后续步骤

也可以通过 PowerShell 创建和管理文件共享。 有关详细信息，请参阅 [在 Windows 上开始使用 Azure 文件存储](../../storage/files/storage-dotnet-how-to-use-files.md)。
