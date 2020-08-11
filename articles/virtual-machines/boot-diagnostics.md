---
title: Azure 启动诊断
description: Azure 启动诊断和托管启动诊断概述
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067080"
---
# <a name="azure-boot-diagnostics"></a>Azure 启动诊断

启动诊断是适用于 Azure 虚拟机 (VM) 的一项调试功能，可用于诊断 VM 启动故障。 启动诊断使用户能够通过收集串行日志信息和屏幕截图来观察其 VM 的状态。

## <a name="boot-diagnostics-view"></a>启动诊断视图
在 "虚拟机" 边栏选项卡中，"启动诊断" 选项位于 Azure 门户的 "*支持和故障排除*" 部分下。 选择 "启动诊断" 将显示屏幕截图和串行日志信息。 串行日志包含内核消息，屏幕快照是 Vm 当前状态的快照。 根据 VM 是否正在运行，Windows 或 Linux 会确定所需的屏幕快照。 对于 Windows，用户将看到桌面背景和 Linux，用户会看到登录提示。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 启动诊断的屏幕截图":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 启动诊断屏幕截图":::


## <a name="limitations"></a>限制
- 启动诊断仅适用于 Azure 资源管理器 Vm。 
- 启动诊断不支持高级存储帐户，如果高级存储帐户用于启动诊断，用户将 `StorageAccountTypeNotSupported` 在启动 VM 时收到错误。 

## <a name="next-steps"></a>后续步骤

了解有关[Azure 串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)的详细信息，以及如何使用启动诊断对[azure 中的虚拟机进行故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)。
