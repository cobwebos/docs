---
title: Azure 串行控制台 |Microsoft Docs
description: 当 SSH 或 RDP 不可用时，Azure 串行控制台允许你连接到 VM。
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
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153853"
---
# <a name="azure-serial-console"></a>Azure 串行控制台

Azure 门户中的串行控制台提供对运行 Linux 或 Windows 的虚拟机（Vm）和虚拟机规模集实例的基于文本的控制台的访问。 此串行连接连接到 VM 或虚拟机规模集实例的 ttyS0 或 COM1 串行端口，提供独立于网络或操作系统状态的访问权限。 串行控制台只能通过使用 Azure 门户进行访问，而只允许访问角色为 "参与者" 或 "虚拟机" 或 "虚拟机规模集" 的用户使用。

串行控制台的工作方式与 Vm 和虚拟机规模集实例的工作方式相同。 在此文档中，除非另有说明，否则，所有对 Vm 的提及将隐式包含虚拟机规模集实例。

> [!NOTE]
> 在全球 Azure 区域和 Azure 政府公共预览版中，此串行控制台已正式发布。 它目前在 Azure 中国云中不可用。

## <a name="prerequisites-to-access-the-azure-serial-console"></a>访问 Azure 串行控制台的先决条件
若要访问 VM 或虚拟机规模集实例上的串行控制台，将需要以下各项：

- 必须为 VM 启用启动诊断
- 使用密码身份验证的用户帐户必须存在于 VM 中。 你可以使用 VM 访问扩展的 "[重置密码](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)" 功能创建基于密码的用户。 在“支持 + 故障排除”部分选择“重置密码”。
- 访问串行控制台的 Azure 帐户必须同时具有 VM 和[启动诊断](boot-diagnostics.md)存储帐户的 "[虚拟机参与者" 角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

> [!NOTE]
> 不支持经典部署。 VM 或虚拟机规模集实例必须使用 Azure 资源管理器部署模型。

## <a name="get-started-with-the-serial-console"></a>串行控制台入门
Vm 和虚拟机规模集的串行控制台只能通过 Azure 门户访问：

### <a name="serial-console-for-virtual-machines"></a>虚拟机的串行控制台
Vm 的串行控制台就像在 Azure 门户的 "**支持 + 故障排除**" 部分中单击**串行控制台**一样简单。
  1. 打开 [Azure 门户](https://portal.azure.com)。

  1. 导航到 "**所有资源**"，并选择虚拟机。 此时会打开该 VM 的概述页。

  1. 向下滚动到“支持 + 故障排除”部分，并选择“串行控制台”。 此时会打开一个包含串行控制台的新窗格，并启动连接。

     ![Linux 串行控制台窗口](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>虚拟机规模集的串行控制台
串行控制台可用于虚拟机规模集，可在规模集内的每个实例上访问。 在看到 "**串行控制台**" 按钮之前，必须导航到虚拟机规模集的单个实例。 如果虚拟机规模集未启用启动诊断，请确保更新虚拟机规模集模型以启用启动诊断，然后将所有实例升级到新模型，以便访问串行控制台。
  1. 打开 [Azure 门户](https://portal.azure.com)。

  1. 导航到 "**所有资源**"，并选择虚拟机规模集。 此时将打开虚拟机规模集的 "概述" 页。

  1. 导航到**实例**

  1. 选择虚拟机规模集实例

  1. 在 "**支持 + 故障排除**" 部分中，选择**串行控制台**。 此时会打开一个包含串行控制台的新窗格，并启动连接。

     ![Linux 虚拟机规模集串行控制台](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>串行控制台的高级用法
除了控制台访问 VM 之外，还可以使用 Azure 串行控制台来执行以下操作：
* [向 VM 发送系统请求命令](./serial-console-nmi-sysrq.md)
* [向 VM 发送不可屏蔽中断](./serial-console-nmi-sysrq.md)
* 正常[重新启动或强制虚拟机重启](./serial-console-power-options.md)


## <a name="next-steps"></a>后续步骤
边栏中提供了其他串行控制台文档。
- 有关适用于[Linux vm 的串行控制台](./serial-console-linux.md)的详细信息。
- 适用于[Windows vm 的串行控制台](./serial-console-windows.md)的详细信息。
