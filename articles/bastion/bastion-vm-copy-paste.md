---
title: 在虚拟机之间进行复制和粘贴： Azure 堡垒
description: 本文介绍如何使用堡垒在 Azure VM 之间进行复制和粘贴。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: charwen
ms.openlocfilehash: 8580b7e28c8a4860739efa0763ebb7c20a80585e
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780277"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>复制并粘贴到虚拟机： Azure 堡垒

本文将帮助你在使用 Azure 堡垒时，将文本复制并粘贴到虚拟机中。 使用 VM 之前，请确保已按照[创建堡垒主机](bastion-create-host-portal.md)的步骤进行操作。 然后，使用[RDP](bastion-connect-vm-rdp.md)或[SSH](bastion-connect-vm-ssh.md)连接到要使用的虚拟机。

对于支持高级剪贴板 API 访问的浏览器，你可以使用在本地设备上的应用程序之间进行复制和粘贴的相同方式在本地设备与远程会话之间复制和粘贴文本。 对于其他浏览器，可以使用堡垒剪贴板访问工具面板。

>[!NOTE]
>目前仅支持文本复制/粘贴。
>

   ![允许剪贴板](./media/bastion-vm-manage/allow.png)

仅支持文本复制/粘贴。 对于直接复制和粘贴，浏览器可能会提示你在启动堡垒会话时进行剪贴板访问。 **允许**网页访问剪贴板。

## <a name="copy-to-a-remote-session"></a><a name="to"></a>复制到远程会话

使用[Azure 门户](https://portal.azure.com)连接到虚拟机后，请完成以下步骤：

1. 将本地设备中的文本/内容复制到本地剪贴板。
1. 在远程会话期间，通过选择两个箭头启动堡垒剪贴板访问工具面板。 箭头位于会话的左侧。

   ![工具调色板](./media/bastion-vm-manage/left.png)

   ![剪贴板](./media/bastion-vm-manage/clipboard.png)
1. 通常，已复制的文本会自动显示在 "堡垒复制粘贴" 面板上。 如果不存在文本，请将文本粘贴到调色板上的文本区域中。
1. 文本置于文本区域后，可以将其粘贴到远程会话。

   ![粘](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>从远程会话复制

使用[Azure 门户](https://portal.azure.com)连接到虚拟机后，请完成以下步骤：

1. 将远程会话中的文本/内容复制到远程剪贴板（使用 Ctrl + C）。

   ![工具调色板](./media/bastion-vm-manage/remote.png)
1. 在远程会话期间，通过选择两个箭头启动堡垒剪贴板访问工具面板。 箭头位于会话的左侧。

   ![剪贴板](./media/bastion-vm-manage/clipboard2.png)
1. 通常，已复制的文本会自动显示在 "堡垒复制粘贴" 面板上。 如果不存在文本，请将文本粘贴到调色板上的文本区域中。
1. 文本置于文本区域后，可以将其粘贴到本地设备。

   ![粘](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)。
