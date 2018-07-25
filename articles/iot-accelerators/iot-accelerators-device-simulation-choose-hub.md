---
title: 将现有的 IoT 中心与设备模拟解决方案配合使用 - Azure | Microsoft Docs
description: 本文介绍如何配置设备模拟解决方案加速器以使用现有的 IoT 中心。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967474"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>将现有的 IoT 中心与设备模拟解决方案加速器配合使用

在预配设备模拟解决方案加速器时，可以选择在解决方案加速器的资源组中部署 IoT 中心，以便在模拟中使用。

如果不选择部署可选的 IoT 中心，则必须为运行的所有模拟使用你自己的中心。 如果选择部署可选的 IoT 中心，则可以选择使用此可选中心或你自己的中心。

如果还没有 IoT 中心，则在 [Azure 门户](https://portal.azure.com)中新建一个即可。

若要使用预先存在的 IoT 中心，则需要 iothubowner 共享访问策略的连接字符串。 可从 [Azure 门户](https://portal.azure.com)获取此连接字符串：

1. 在门户的 IoT 中心配置页上，单击“共享访问策略”。
1. 单击“iothubowner”。
1. 复制主连接字符串或辅助连接字符串。

[![获取连接字符串](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

在配置模拟时使用复制的连接字符串：

[![配置模拟](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>后续步骤

在此操作方法指南中，你已了解如何在模拟中使用现有的 IoT 中心。 接下来，你可能会想要了解如何为模拟[配置自定义设备模型](iot-accelerators-device-simulation-custom-model.md)。
