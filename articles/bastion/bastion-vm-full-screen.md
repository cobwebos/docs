---
title: 在 Azure 堡垒中将虚拟机会话视图更改为全屏 |Microsoft Docs
description: 本文介绍如何将视图更改为全屏。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: a8c9cab825644fe797713c80d3a710c7a2d54850
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498047"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>更改为 vm 会话的全屏视图： Azure 堡垒

本文可帮助你将虚拟机视图更改为全屏，并返回浏览器。 使用 VM 之前，请确保已按照[创建堡垒主机](bastion-create-host-portal.md)的步骤进行操作。 然后，使用[RDP](bastion-connect-vm-rdp.md)或[SSH](bastion-connect-vm-ssh.md)连接到要使用的虚拟机。

## <a name="launch-the-clipboard-tool"></a>启动剪贴板工具

在远程会话期间，通过选择位于会话左侧中心的两个箭头，启动 "堡垒剪贴板访问工具" 组件面板。

![工具](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>选择全屏

选择 "**全屏**" 按钮，将会话切换到全屏体验。 切换后，会话将重新初始化为全屏。

![全屏](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)。
了解如何在 Azure VM 之间进行[复制和粘贴](bastion-vm-copy-paste.md)。