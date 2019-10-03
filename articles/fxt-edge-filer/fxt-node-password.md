---
title: 初始化硬件 - Microsoft Azure FXT Edge Filer
description: 如何在 Azure FXT Edge Filer 节点上设置初始密码
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 11cf9f49014648fff1e78aff91c5a724a812e9e7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451758"
---
# <a name="tutorial-set-hardware-passwords"></a>教程：设置硬件密码

首次打开 Azure FXT Edge Filer 节点电源时，必须设置 root 密码。 硬件节点未随附默认密码。 

在设置密码并且 root 用户登录之前，网络端口处于禁用状态。

请在安装节点并布线之后、尝试创建群集之前执行此步骤。 

本教程介绍如何连接到硬件节点和设置密码。 

在本教程中，将了解如何： 

> [!div class="checklist"]
> * 将键盘和监视器连接到节点，然后打开节点电源
> * 在此节点上设置 iDRAC 端口和 root 用户的密码
> * 以 root 身份登录 

针对要在群集中使用的每个节点重复这些步骤。 

完成本教程大约需要 15 分钟。 

## <a name="prerequisites"></a>先决条件

在开始本教程之前，请完成以下步骤： 

* 在设备机架中[安装](fxt-install.md)每个 Azure FXT Edge Filer 节点，并根据[前面的教程](fxt-network-power.md)中所述，连接电源线并设置网络访问。 
* 找到可接入硬件节点的已连接 USB 端口的键盘以及已连接 VGA 端口的监视器。 （在设置密码之前，节点的串行端口处于非活动状态。）

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>将键盘和监视器连接到节点

以物理方式将监视器和键盘连接到 Azure FXT Edge Filer 节点。 

* 将监视器连接到 VGA 端口。
* 将键盘连接到 USB 端口之一。 

根据此参考图在机箱背面找到端口。 

> [!NOTE]
> 在设置密码之前，串行端口处于非活动状态。 

![标有串行、VGA 和 USB 端口的 Azure FXT Edge Filer 背面示意图](media/fxt-back-serial-vga-usb.png)

若要将多个节点连接到相同的外围设备，可以使用 KVM 交换机。 

按下正面的电源按钮，打开节点的电源。 

![Azure FXT Edge Filer 正面示意图 - 右上角附近标有圆形电源按钮](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>设置初始密码 

启动时，Azure FXT Edge Filer 节点会在监视器中输出各种消息。 几分钟后，它会显示如下所示的初始设置屏幕：

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

输入的密码用于两个目的： 

* 用作此 Azure FXT Edge Filer 节点的临时 root 密码。 

  使用此节点创建群集，或者将此节点添加到群集时，此密码将会更改。 群集管理密码（与用户 ``admin`` 关联）也是群集中所有节点的 root 密码。

* 它是 iDRAC/IPMI 硬件管理端口的长期密码。

  请务必记住该密码，以防今后在排查硬件问题时需要使用 IPMI 登录。

输入并确认密码： 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

输入密码后，系统将继续启动。 启动完成后，它会显示 ``login:`` 提示符。 

## <a name="sign-in-as-root"></a>以 root 身份登录

使用刚刚设置的密码以 ``root`` 身份登录。 

```
login: root
Password:**********
```

以 root 身份登录后，网络端口将处于活动状态，并且会联系 DHCP 服务器来获取 IP 地址。 

## <a name="next-steps"></a>后续步骤

现已准备好将节点加入群集。 可以使用该节点创建 Azure FXT Edge Filer 群集，或者[将其添加到现有群集](fxt-add-nodes.md)。 

> [!div class="nextstepaction"]
> [创建群集](fxt-cluster-create.md)
