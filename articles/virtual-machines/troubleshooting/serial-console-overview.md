---
title: Azure 串行控制台 | Microsoft Docs
description: 使用 Azure 串行控制台，可以在 SSH 或 RDP 不可用时连接到 VM。
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
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374059"
---
# <a name="azure-serial-console"></a>Azure 串行控制台

使用 Azure 门户中的串行控制台可以访问适用于运行 Linux 或 Windows 的虚拟机 (VM) 和虚拟机规模集实例的基于文本的控制台。 此串行控制台连接到 VM 或虚拟机规模集实例的 ttyS0 或 COM1 串行端口，为用户提供访问权限，而不管它的网络或操作系统状态如何。 串行控制台只能通过使用 Azure 门户来访问，并且仅供具有参与者访问角色或高于 VM 或虚拟机规模集的用户使用。

串行控制台的工作方式与 VM 和虚拟机规模集实例的工作方式相同。 在本文档中，除非另有说明，否则所有对 VM 的提及都将隐式包含虚拟机规模集实例。

在全球 Azure 区域和 Azure 政府公共预览版中，串行控制台已正式发布。 它在 Azure 中国云中尚不可用。

## <a name="prerequisites-to-access-the-azure-serial-console"></a>访问 Azure 串行控制台的先决条件
若要在 VM 或虚拟机规模集实例上访问串行控制台，将需要以下各项：

- 必须为 VM 启用启动诊断
- 使用密码身份验证的用户帐户必须存在于 VM 中。 可以使用 VM 访问扩展的[重置密码](../extensions/vmaccess.md#reset-password)功能创建基于密码的用户。 在“支持 + 故障排除”部分选择“重置密码”。 
- 访问串行控制台的 Azure 帐户必须对 VM 和[启动诊断](boot-diagnostics.md)存储帐户拥有[虚拟机参与者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
- 不支持经典部署。 VM 或虚拟机规模集实例必须使用 Azure 资源管理器部署模型。

> [!NOTE]
> 串行控制台当前与托管的启动诊断存储帐户不兼容。 若要使用串行控制台，请确保使用的是自定义的存储帐户。

## <a name="get-started-with-the-serial-console"></a>开始使用串行控制台
只能通过 Azure 门户访问适用于 VM 和虚拟机规模集的串行控制台：

### <a name="serial-console-for-virtual-machines"></a>适用于虚拟机的串行控制台
只需在 Azure 门户的“支持 + 故障排除”部分中单击“串行控制台”即可访问适用于 VM 的串行控制台 。
  1. 打开 [Azure 门户](https://portal.azure.com)。

  1. 导航到“所有资源”并选择虚拟机。 此时会打开该 VM 的概述页。

  1. 向下滚动到“支持 + 故障排除”部分，并选择“串行控制台”。  此时会打开一个包含串行控制台的新窗格，并启动连接。

     ![Linux 串行控制台窗口](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的串行控制台
串行控制台可用于虚拟机规模集，可在规模集内的每个实例上访问。 在看到“串行控制台”按钮之前，必须导航到虚拟机规模集的单个实例。 如果虚拟机规模集未启用启动诊断，请确保更新虚拟机规模集模型以启用启动诊断，然后将所有实例升级到新模型，以便访问串行控制台。
  1. 打开 [Azure 门户](https://portal.azure.com)。

  1. 导航到“所有资源”并选择虚拟机规模集。 将打开虚拟机规模集的概述页。

  1. 导航到“实例”

  1. 选择虚拟机规模集实例

  1. 在“支持 + 故障排除”部分中，选择“串行控制台”。  此时会打开一个包含串行控制台的新窗格，并启动连接。

     ![Linux 虚拟机规模集串行控制台](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>串行控制台中的 TLS 1.2
串行控制台使用 TLS 1.2 端到端来保护服务中的所有通信。 串行控制台依赖于用户管理的启动诊断存储帐户，并且必须为存储帐户单独配置 TLS 1.2。 [此处](../../storage/common/transport-layer-security-configure-minimum-version.md)提供了操作说明。

## <a name="advanced-uses-for-serial-console"></a>串行控制台的高级用途
除了通过控制台访问 VM 之外，还可以使用 Azure 串行控制台来执行以下操作：
* 向 VM 发送[系统请求命令](./serial-console-nmi-sysrq.md)
* 向 VM 发送[不可屏蔽的中断](./serial-console-nmi-sysrq.md)
* 正常[重新启动或强制重新打开 VM 电源](./serial-console-power-options.md)


## <a name="next-steps"></a>后续步骤
边栏中提供了其他串行控制台文档。
- [适用于 Linux VM 的串行控制台](./serial-console-linux.md)中提供了详细信息。
- [适用于 Windows VM 的串行控制台](./serial-console-windows.md)中提供了详细信息。
