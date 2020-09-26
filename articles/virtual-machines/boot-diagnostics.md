---
title: Azure 启动诊断
description: Azure 启动诊断和托管启动诊断概述
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: b51b44f3a3d0889836bb41e0bf2fa37234338cf4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287077"
---
# <a name="azure-boot-diagnostics"></a>Azure 启动诊断

启动诊断是 Azure 虚拟机 (VM) 的一项调试功能，可用于诊断 VM 启动故障。 启动诊断使用户能够通过收集串行日志信息和屏幕截图来观察其 VM 在启动时的状态。

## <a name="boot-diagnostics-storage-account"></a>启动诊断存储帐户
在 Azure 门户中创建 VM 时，默认情况下启用启动诊断。 建议的启动诊断体验是使用托管存储帐户，因为它会在创建 Azure VM 时产生显著的性能改进。 这是因为将使用 Azure 托管的存储帐户，删除创建新用户存储帐户以存储启动诊断数据所用的时间。

另一种启动诊断体验是使用用户管理的存储帐户。 用户可以创建新的存储帐户，也可以使用现有的存储帐户。

> [!IMPORTANT]
> 在10月2020，Azure 客户不会向使用托管存储帐户启动诊断相关的存储费用收费。

## <a name="boot-diagnostics-view"></a>启动诊断视图
虚拟机边栏选项卡中的启动诊断选项位于 Azure 门户的“支持和故障排除”部分。 选择启动诊断会显示屏幕截图和串行日志信息。 串行日志包含内核消息，屏幕快照是 VM 当前状态的快照。 Windows 或 Linux 会根据 VM 是否正在运行来确定预期的屏幕快照会是什么样子。 Windows 用户会看到桌面背景，Linux 用户会看到登录提示。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 启动诊断的屏幕截图":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 启动诊断的屏幕截图":::


## <a name="limitations"></a>限制
- 启动诊断仅适用于 Azure 资源管理器 VM。 
- 启动诊断不支持高级存储帐户。如果将高级存储帐户用于启动诊断，则在启动 VM 时，用户会收到 `StorageAccountTypeNotSupported` 错误。 
- 资源管理器 API 版本 "2020-06-01" 和更高版本支持托管存储帐户。
- Azure 串行控制台当前与用于启动诊断的托管存储帐户不兼容。 详细了解 [Azure 串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)。
- 当前只能通过 Azure 门户应用使用管理存储帐户的启动诊断。 

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) 的详细信息，以及如何使用启动诊断对 [azure 中的虚拟机进行故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)。
