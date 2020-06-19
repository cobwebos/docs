---
title: 如何从 Chromebook 连接到 Azure 实验室服务 VM | Microsoft Docs
description: 了解如何从 Chromebook 连接到 Azure 实验室服务中的虚拟机。
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
ms.date: 05/11/2020
ms.author: nicolela
ms.openlocfilehash: 581ba32f536728fec88ddf3120637f9347e2e925
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704372"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>在 Chromebook 上使用远程桌面协议连接到 VM
本部分介绍学生如何使用 RDP 从 Chromebook 连接到课堂实验室 VM。

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>在 Chromebook 上安装 Microsoft 远程桌面
1. 在 Chromebook 上打开 App Store，然后搜索“Microsoft 远程桌面”。

    ![Microsoft 远程桌面](../media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. 安装最新版本的 Microsoft 远程桌面。 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>使用 RDP 从 Chromebook 访问 VM
1. 在安装了 **Microsoft 远程桌面**的计算机上打开下载的 **RDP** 文件。 它应该开始连接到 VM。 

    ![连接到 VM](../media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. 根据提示输入密码。
    ![连接到 VM](../media/how-to-use-classroom-lab/password-chromebook.png)


1. 如果收到以下警告，请选择“继续”。 

    ![证书警告](../media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. 你应看到要连接到的 VM 的桌面。

## <a name="next-steps"></a>后续步骤
若要了解有关如何连接到 Linux VM 的详细信息，请参阅[连接到 Linux 虚拟机](how-to-use-remote-desktop-linux-student.md)


