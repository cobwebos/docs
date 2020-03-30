---
title: 如何从 Mac 连接到 Azure 实验室服务 VM |微软文档
description: 本文
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
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503083"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>在 Mac 上使用 RDP 连接到 VM
本节演示如何使用 RDP 从 Mac 连接到教室实验室 VM。

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>在 Mac 上安装 Microsoft 远程桌面
1. 在 Mac 上打开应用商店，然后搜索“Microsoft 远程桌面”****。

    ![Microsoft 远程桌面](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 安装最新版本的 Microsoft 远程桌面。 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>使用 RDP 从 Mac 访问 VM
1. 在安装了 **Microsoft 远程桌面**的计算机上打开下载的 **RDP** 文件。 它应该开始连接到 VM。 

    ![连接到 VM](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 如果收到以下警告，请选择“继续”****。 

    ![证书警告](../media/how-to-use-classroom-lab/certificate-error.png)
1. 应会看到 VM。 

    > [!NOTE]
    > 以下示例适用于 CentOS Linux VM。 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>后续步骤
要了解如何使用 RDP 连接到 Linux VM，请参阅[为 Linux 虚拟机使用远程桌面](how-to-use-remote-desktop-linux-student.md)


