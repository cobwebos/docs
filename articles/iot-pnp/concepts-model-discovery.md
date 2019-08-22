---
title: 实现 IoT 即插即用预览模型发现 |Microsoft Docs
description: 作为解决方案开发人员, 了解如何在解决方案中实现 IoT 即插即用模型发现。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/17/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e4ab1d45e27762ef05ab7ec74c98ab0b0b934cbf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880548"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>在 IoT 解决方案中实现 IoT 即插即用预览模型发现

本文介绍了如何作为解决方案开发人员在 IoT 解决方案中实现 IoT 即插即用预览模型发现。  IoT 即插即用模式发现是 IoT 即插即用设备如何识别其支持的功能模型和接口, 以及 IoT 解决方案如何检索这些功能模型和接口。

IoT 解决方案分为两大类: 专门构建的解决方案, 适用于一组已知的 IoT 即插即用设备和与任何 IoT 即插即用设备一起使用的模型驱动的解决方案。

此概念文章介绍了如何在这两种类型的解决方案中实现模型发现。

## <a name="model-discovery"></a>模型发现

IoT 即插即用设备首次连接到 IoT 中心时, 会发送一个模型信息遥测消息。 此消息包括设备实现的接口 Id。 要使你的解决方案与设备一起工作, 它必须解析这些 Id 并检索每个接口的定义。

以下是 IoT 即插即用设备在使用设备预配服务 (DPS) 连接到集线器时所采用的步骤:

1. 当设备处于开启状态时, 它将连接到 DPS 的全局终结点并使用允许的方法之一进行身份验证。
1. 然后, DPS 对设备进行身份验证, 并查找规则, 告诉它要将设备分配到哪个 IoT 中心。 然后, DPS 向该中心注册设备。
1. DPS 会将 IoT 中心连接字符串返回到设备。
1. 然后, 设备会将发现遥测消息发送到 IoT 中心。 发现遥测消息包含设备所实现的接口的 Id。
1. IoT 即插即用设备现在已准备好使用 IoT 中心的解决方案。

如果设备直接连接到 IoT 中心, 它将使用嵌入到设备代码中的连接字符串进行连接。 然后, 设备会将发现遥测消息发送到 IoT 中心。

若要详细了解模型信息遥测消息, 请参阅[ModelInformation](concepts-common-interfaces.md)接口。

### <a name="purpose-built-iot-solutions"></a>专门构建的 IoT 解决方案

专门构建的 IoT 解决方案适用于一组已知的 IoT 即插即用设备功能模型和接口。

你将提前连接到你的解决方案的设备的功能模型和接口。 使用以下步骤来准备解决方案:

1. 将接口 JSON 文件存储在 Azure 中, 使其可供解决方案读取。
1. 根据预期的 IoT 即插即用功能模型和接口, 在 IoT 解决方案中编写逻辑。
1. 订阅来自你的解决方案使用的 IoT 中心的通知。

收到新设备连接的通知后, 请执行以下步骤:

1. 阅读发现遥测消息以检索功能模型的 Id 和设备实现的接口。
1. 将功能模型的 ID 与事先存储的功能模型的 id 进行比较。
1. 现在, 你知道已连接的设备类型。 使用之前编写的逻辑使用户能够适当地与设备交互。

### <a name="model-driven-solutions"></a>模型驱动的解决方案

模型驱动的 IoT 解决方案可与任何 IoT 即插即用设备配合使用。 构建模型驱动的 IoT 解决方案更复杂, 但优点在于您的解决方案适用于将来添加的任何设备。

若要生成模型驱动的 IoT 解决方案, 需要根据 IoT 即插即用接口基元构建逻辑: 遥测、属性和命令。 IoT 解决方案的逻辑通过合并多个遥测、属性和命令功能来表示设备。

解决方案还必须订阅来自其使用的 IoT 中心的通知。

当解决方案收到新设备连接的通知时, 请执行以下步骤:

1. 阅读发现遥测消息以检索功能模型的 Id 和设备实现的接口。
1. 对于每个 ID, 请阅读完整的 JSON 文件以查找设备的功能。
1. 检查是否每个接口都存在于你为存储解决方案之前检索到的 JSON 文件而生成的任何缓存中。
1. 然后, 检查具有该 ID 的接口是否存在于全局模型存储库中。 有关详细信息, 请参阅[全局模型存储库](howto-manage-models.md)。
1. 如果接口不存在于全局模型存储库中, 请尝试在解决方案已知的任何专用模型存储库中查找它。 需要连接字符串才能访问专用模型存储库。 有关详细信息, 请参阅[专用模型存储库](howto-manage-models.md)。
1. 如果在全局模型存储库或专用模型存储库中都找不到所有接口, 可以检查设备是否可以提供接口定义。 设备可以实现标准[ModelDefinition](concepts-common-interfaces.md)接口, 以发布有关如何使用命令检索接口文件的信息。
1. 如果找到了设备实现的每个接口的 JSON 文件, 则可以枚举设备的功能。 使用之前编写的逻辑使用户能够与设备交互。
1. 你随时都可以调用数字孪生 API 来检索设备的功能模型 ID 和接口 Id。

## <a name="next-steps"></a>后续步骤

现在, 你已了解了 IoT 解决方案的模型发现, 接下来详细了解[Azure Iot 平台](overview-iot-plug-and-play.md), 以利用解决方案的其他功能。
