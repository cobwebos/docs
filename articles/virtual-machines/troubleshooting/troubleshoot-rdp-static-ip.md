---
title: 由于静态 IP 而无法通过远程桌面连接到 Azure 虚拟机 |Microsoft Docs
description: 了解如何对由 Microsoft Azure 中的静态 IP 导致的 RDP 问题进行故障排除 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 81a3064290e0aa720a4fe6b0fa0d8eb13cfe6903
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318912"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>由于静态 IP 而无法通过远程桌面连接到 Azure 虚拟机

本文介绍了在 VM 中配置静态 IP 后无法通过远程桌面连接到 Azure Windows 虚拟机 (VM) 的问题。

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用资源管理器部署模型。建议对新部署使用该模型，而不要使用经典部署模型。

## <a name="symptoms"></a>症状

与 Azure 中的 VM 建立 RDP 连接时，收到以下错误消息：

**由于以下原因之一，远程桌面无法连接到远程计算机：**

1. **未启用服务器的远程访问**

2. **远程计算机已关闭**

3. **远程计算机在网络中不可用**

**请确保远程计算机已打开并已连接到网络，并且已启用远程访问。**

在 Azure 门户中的[“启动诊断”](../troubleshooting/boot-diagnostics.md)中检查屏幕截图时，你看到 VM 正常启动并且在登录屏幕中等待凭据。

## <a name="cause"></a>原因

VM 具有一个在 Windows 中的网络接口上定义的静态 IP 地址。 此 IP 地址不同于在 Azure 门户中定义的地址。

## <a name="solution"></a>解决方案

在执行这些步骤之前，请创建受影响 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

若要解决此问题，请使用串行控制台启用 DHCP 或者为 VM [重置网络接口](reset-network-interface.md)。

### <a name="use-serial-control"></a>使用串行控制台

1. 连接到[串行控制台并打开 CMD 实例](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
)。 如果未在 VM 上启用串行控制台，请参阅[重置网络接口](reset-network-interface.md)。
2. 在网络接口上检查是否禁用了 DHCP：

        netsh interface ip show config
3. 如果禁用了 DHCP，请将你的网络接口的配置恢复为使用 DHCP：

        netsh interface ip set address name="<NIC Name>" source=dhc

    例如，如果网络接口名为“Ethernet 2”，请运行以下命令：

        netsh interface ip set address name="Ethernet 2" source=dhc

4. 再次查询 IP 配置以确保网络接口现在已正确设置。 新 IP 地址应当与 Azure 提供的地址相同。

        netsh interface ip show config

    此时无需重启 VM。 VM 将恢复为可访问的。

之后，如果希望为 VM 配置静态 IP，请参阅[为 VM 配置静态 IP 地址](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。