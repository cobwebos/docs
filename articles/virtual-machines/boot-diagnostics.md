---
title: Azure 启动诊断
description: Azure 启动诊断和托管启动诊断概述
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: d425953b278a98af35a172d8777ab758db52709e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257749"
---
# <a name="azure-boot-diagnostics"></a>Azure 启动诊断

启动诊断是适用于 Azure 虚拟机 (VM) 的一项调试功能，可用于诊断 VM 启动故障。 启动诊断使用户能够通过收集串行日志信息和屏幕截图来观察其 VM 的状态。

## <a name="boot-diagnostics-storage-account"></a>启动诊断存储帐户
在 Azure 门户中创建 VM 时，默认情况下启用启动诊断。 建议的启动诊断体验是使用托管存储帐户，因为它会在创建 Azure VM 时产生显著的性能改进。 这是因为将使用 Azure 托管的存储帐户，删除创建新用户存储帐户以存储启动诊断数据所用的时间。

另一种启动诊断体验是使用用户管理的存储帐户。 用户可以创建新的存储帐户，也可以使用现有的存储帐户。

> [!IMPORTANT]
> 在10月2020，使用托管存储帐户通过启动诊断 associted，将不会对 Azure 客户收费。

## <a name="boot-diagnostics-view"></a>启动诊断视图
在 "虚拟机" 边栏选项卡中，"启动诊断" 选项位于 Azure 门户的 " *支持和故障排除* " 部分下。 选择 "启动诊断" 将显示屏幕截图和串行日志信息。 串行日志包含内核消息，屏幕快照是 Vm 当前状态的快照。 根据 VM 是否正在运行，Windows 或 Linux 会确定所需的屏幕快照。 对于 Windows，用户将看到桌面背景和 Linux，用户会看到登录提示。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 启动诊断的屏幕截图":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 启动诊断屏幕截图":::


## <a name="limitations"></a>限制
- 启动诊断仅适用于 Azure 资源管理器 Vm。 
- 启动诊断不支持高级存储帐户，如果高级存储帐户用于启动诊断，用户将 `StorageAccountTypeNotSupported` 在启动 VM 时收到错误。 
- Azure 串行控制台当前与用于启动诊断的托管存储帐户不兼容。 详细了解 [Azure 串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)。

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) 的详细信息，以及如何使用启动诊断对 [azure 中的虚拟机进行故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)。
