---
title: Azure 启动诊断
description: Azure 启动诊断和托管启动诊断概述
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042946"
---
# <a name="azure-boot-diagnostics"></a>Azure 启动诊断

启动诊断是适用于 Azure 虚拟机 (VM) 的一项调试功能，可用于诊断 VM 启动故障。 启动诊断使用户能够通过收集串行日志信息和屏幕截图来观察其 VM 的状态。

## <a name="boot-diagnostics-storage-account"></a>启动诊断存储帐户
默认情况下，为使用 Azure 门户创建的所有 Vm 启用启动诊断，并使用托管存储帐户。 通过使用托管存储帐户，用户可以显著提高 VM 部署时间。 出于此原因，我们建议客户将启动诊断用于所有 Vm 的托管存储帐户。

> [!NOTE]
> 如果选择在2020年10月1日前将启动诊断用于托管存储帐户，则不会对 Azure 客户进行存储收费。

另一种启动诊断体验是使用自定义的存储帐户。 用户可以创建新的存储帐户，也可以使用现有的存储帐户。 有关自定义存储帐户的详细信息，请参阅[存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="显示如何启用启动诊断的屏幕截图":::

## <a name="boot-diagnostics-view"></a>启动诊断视图
在 "虚拟机" 边栏选项卡中，"启动诊断" 选项位于 Azure 门户的 "*支持和故障排除*" 部分下。 选择 "启动诊断" 将显示屏幕截图和串行日志信息。 串行日志包含内核消息，屏幕快照是 Vm 当前状态的快照。 根据 VM 是否正在运行，Windows 或 Linux 会确定所需的屏幕快照。 对于 Windows，用户将看到桌面背景和 Linux，用户会看到登录提示。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 启动诊断的屏幕截图":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 启动诊断屏幕截图":::


## <a name="limitations"></a>限制
- 启动诊断仅适用于 Azure 资源管理器 (ARM) Vm。 
- 启动诊断不支持高级存储帐户，如果高级存储帐户用于启动诊断，用户将 `StorageAccountTypeNotSupported` 在启动 VM 时收到错误。 
- Azure 串行控制台目前不支持用于启动诊断的托管存储帐户。

## <a name="next-steps"></a>后续步骤

了解有关[Azure 串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)的详细信息，以及如何使用启动诊断对[azure 中的虚拟机进行故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)。
