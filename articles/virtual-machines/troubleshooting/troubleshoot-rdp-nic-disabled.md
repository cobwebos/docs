---
title: 因为 NIC 被禁用而无法远程连接到 Azure 虚拟机 |Microsoft Docs
description: 了解因为 NIC 在 Azure VM 中被禁用而导致 RDP 失败时如何进行故障排除 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 9038bdb79504289c3aa149868d8a02e74c11fdb7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103432"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>无法通过远程桌面连接到 VM，因为网络接口被禁用

本文介绍如何解决在禁用网络接口时无法与 Azure Windows 虚拟机 (VM) 建立远程桌面连接的问题。

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用资源管理器部署模型。建议对新部署使用该模型，而不要使用经典部署模型。

## <a name="symptoms"></a>症状

无法与 Azure 中的 VM 的任何其他端口建立 RDP 连接或任何其他类型的连接，因为 VM 中的网络接口被禁用。

## <a name="solution"></a>解决方案

在执行这些步骤之前，请创建受影响 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

若要为 VM 启用该接口，请使用串行控制台为 VM [重置网络接口](##reset-network-interface)。

### <a name="use-serial-control"></a>使用串行控制台

1. 连接到[串行控制台并打开 CMD 实例](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
)。 如果未在 VM 上启用串行控制台，请参阅[重置网络接口](#reset-network-interface)。
2. 检查网络接口的状态：

        netsh interface show interface

    记下被禁用的网络接口的名称。

3. 启用网络接口：

        netsh interface set interface name="interface Name" admin=enabled

    例如，如果网络接口名为“以太网 2”，请运行以下命令：

        netsh interface set interface name=""Ethernet 2" admin=enabled


4.  再次检查该网络接口的状态以确保该网络接口已启用。

        netsh interface show interface

    此时无需重启 VM。 VM 将恢复为可访问的。

5.  连接到 VM 并查看问题是否得以解决。

## <a name="reset-network-interface"></a>重置网络接口

要重置网络接口，请将 IP 地址更改为子网中可用的其他 IP 地址。 若要执行此操作，请使用 Azure 门户或 Azure PowerShell。 有关详细信息，请参阅[重置网络接口](reset-network-interface.md)。