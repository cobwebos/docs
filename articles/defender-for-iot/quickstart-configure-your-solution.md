---
title: 快速入门：配置解决方案
description: 在本快速入门中，了解如何使用 Azure Defender for IoT 配置端到端 IoT 解决方案。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 3ecb2e2022cab80abf6a8692ac8cc3ba54eff1e4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944076"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>快速入门：配置你的 Azure Defender for IoT 解决方案

本文介绍如何使用 Defender for IoT 执行 IoT 安全解决方案的初始配置。

## <a name="what-is-defender-for-iot"></a>什么是 Defender for IoT？

Defender for IoT 为基于 Azure 的 IoT 解决方案提供全面的端到端安全性。

使用 Defender for IoT 时，可在一个仪表板中监视整个 IoT 解决方案，并显示 Azure 中你的所有 IoT 设备、IoT 平台和后端资源。

在 IoT 中心启用后，Defender for IoT 会自动识别也已连接到你的 IoT 中心的，以及与你的 IoT 解决方案相关的其他 Azure 服务。

除了自动关系检测以外，还可以选择要将其他哪些 Azure 资源组标记为 IoT 解决方案的一部分。

做出选择后，可以添加整个订阅、资源组或单个资源。

定义所有资源关系后，Defender for IoT 将利用 Defender 来提供安全建议，并针对这些资源发出警报。

## <a name="add-azure-resources-to-your-iot-solution"></a>将 Azure 资源添加到 IoT 解决方案

若要将新资源添加到 IoT 解决方案，请执行以下操作：

1. 在 Azure 门户中打开你的 **IoT 中心**。
1. 从左侧菜单的“安全性”部分选择并打开“设置”，然后选择“受监视资源”  。
1. 选择“编辑”并选择属于 IoT 解决方案的受监视资源。
1. 单击“添加”  。

祝贺你！ 现已将新资源组添加到 IoT 解决方案。

Defender for IoT 现在可以监视新添加的资源组，并将相关的安全建议和警报作为 IoT 解决方案的一部分。

## <a name="next-steps"></a>后续步骤

请转到下一篇文章了解如何创建安全模块...

> [!div class="nextstepaction"]
> [创建安全模块](quickstart-create-security-twin.md)
