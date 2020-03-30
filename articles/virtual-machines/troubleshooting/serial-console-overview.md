---
title: Azure 串行控制台 |微软文档
description: Azure 串行控制台允许您在 SSH 或 RDP 不可用时连接到 VM。
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267009"
---
# <a name="azure-serial-console"></a>Azure 串行控制台

Azure 门户中的串行控制台为运行 Linux 或 Windows 的虚拟机 （VM） 和虚拟机缩放集实例提供对基于文本控制台的访问。 此串行连接连接到 VM 或虚拟机规模集实例的 ttyS0 或 COM1 串行端口，提供独立于网络或操作系统状态的访问。 串行控制台只能通过使用 Azure 门户进行访问，并且仅允许具有"参与者"或更高访问权限的用户访问 VM 或虚拟机规模集。

串行控制台的工作方式与 VM 和虚拟机缩放集实例的工作方式相同。 在本文档中，除非另有说明，否则对 VM 的所有提及都将隐式包含虚拟机规模集实例。

> [!NOTE]
> 串行控制台通常位于全局 Azure 区域中，在 Azure 政府版中提供公共预览版。 它在 Azure 中国云中尚不可用。

## <a name="prerequisites-to-access-the-azure-serial-console"></a>访问 Azure 串行控制台的先决条件
要访问 VM 或虚拟机缩放集实例上的串行控制台，您需要以下操作：

- 必须为 VM 启用启动诊断
- 使用密码身份验证的用户帐户必须存在于 VM 中。 您可以使用 VM 访问扩展的[重置密码](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)功能创建基于密码的用户。 在“支持 + 故障排除”部分选择“重置密码”。********
- 访问串行控制台的 Azure 帐户必须具有 VM 和[引导诊断](boot-diagnostics.md)存储帐户的[虚拟机参与者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

> [!NOTE]
> 不支持经典部署。 VM 或虚拟机缩放集实例必须使用 Azure 资源管理器部署模型。

## <a name="get-started-with-the-serial-console"></a>开始使用串行控制台
虚拟机和虚拟机缩放集的串行控制台只能通过 Azure 门户访问：

### <a name="serial-console-for-virtual-machines"></a>虚拟机串行控制台
VM 的串行控制台与单击 Azure 门户中的 **"支持 + 故障排除**"部分中的**串行控制台**一样简单。
  1. 打开[Azure 门户](https://portal.azure.com)。

  1. 导航到**所有资源**并选择虚拟机。 此时会打开该 VM 的概述页。

  1. 向下滚动到“支持 + 故障排除”部分，并选择“串行控制台”。******** 此时会打开一个包含串行控制台的新窗格，并启动连接。

     ![Linux 串行控制台窗口](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>虚拟机缩放集的串行控制台
串行控制台可用于虚拟机规模集，可在规模集中的每个实例上访问。 在看到**串行控制台**按钮之前，您必须导航到虚拟机规模集的各个实例。 如果虚拟机规模集未启用启动诊断，请确保更新虚拟机规模集模型以启用引导诊断，然后将所有实例升级到新模型以访问串行控制台。
  1. 打开[Azure 门户](https://portal.azure.com)。

  1. 导航到**所有资源**并选择虚拟机缩放集。 将打开虚拟机规模集的概览页。

  1. 导航到**实例**

  1. 选择虚拟机缩放集实例

  1. 在 **"支持 + 故障排除"** 部分中，选择**串行控制台**。 此时会打开一个包含串行控制台的新窗格，并启动连接。

     ![Linux 虚拟机规模集串行控制台](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>串行控制台的高级用途
除了对 VM 的控制台访问外，还可以使用 Azure 串行控制台进行以下操作：
* 向[VM 发送系统请求命令](./serial-console-nmi-sysrq.md)
* 向[VM 发送不可屏蔽的中断](./serial-console-nmi-sysrq.md)
* 优雅地[重新启动或强制重新启动 VM](./serial-console-power-options.md)


## <a name="next-steps"></a>后续步骤
侧边栏中提供了其他串行控制台文档。
- 更多信息可用于 Linux [VM 的串行控制台](./serial-console-linux.md)。
- 有关详细信息，适用于[Windows VM 的串行控制台](./serial-console-windows.md)。
