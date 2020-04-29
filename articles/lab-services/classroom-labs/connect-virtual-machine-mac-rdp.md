---
title: 如何从 Mac 连接到 Azure 实验室服务 VM |Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503083"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>在 Mac 上使用 RDP 连接到 VM
本部分说明了学生如何使用 RDP 从 Mac 连接到课堂实验室 VM。

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
若要了解如何使用 RDP 连接到 Linux Vm，请参阅[使用适用于 linux 虚拟机的远程桌面](how-to-use-remote-desktop-linux-student.md)


