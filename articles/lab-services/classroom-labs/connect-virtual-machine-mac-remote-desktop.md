---
title: 如何从 Mac 连接到 Azure 实验室服务 VM | Microsoft Docs
description: 了解如何从 Mac 连接到 Azure 实验室服务中的虚拟机。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 9087e4fb7989f89c19a70afd9ee8f061f9061166
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704362"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>在 Mac 上使用远程桌面协议连接到 VM
本部分介绍了学生如何使用 RDP 从 Mac 连接到课堂实验室 VM。

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>在 Mac 上安装 Microsoft 远程桌面
1. 在 Mac 上打开应用商店，然后搜索“Microsoft 远程桌面”。

    ![Microsoft 远程桌面](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 安装最新版本的 Microsoft 远程桌面。 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>使用 RDP 从 Mac 访问 VM
1. 在安装了 **Microsoft 远程桌面**的计算机上打开下载的 **RDP** 文件。 它应该开始连接到 VM。 

    ![连接到 VM](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 如果收到以下警告，请选择“继续”。 

    ![证书警告](../media/how-to-use-classroom-lab/certificate-error.png)
1. 应会看到 VM。 

    > [!NOTE]
    > 以下示例适用于 CentOS Linux VM。 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>后续步骤
若要了解如何使用 RDP 连接到 Linux VM，请参阅[使用适用于 Linux 虚拟机的远程桌面](how-to-use-remote-desktop-linux-student.md)


