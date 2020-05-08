---
title: 了解 Azure IoT Central 应用的设备模板版本控制 | Microsoft Docs
description: 通过创建新版本循环访问设备模板，不影响实时连接设备
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 772521a8d3181721270d7fe4dbd11b7807c8d90e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583666"
---
# <a name="create-a-new-device-template-version"></a>创建新设备模板版本

本文适用于解决方案构建者和设备开发人员。 

设备模板包含一个架构，该架构描述设备如何与 IoT Central 进行交互。 这些交互包括遥测、属性和命令。 设备和 IoT Central 应用程序都依赖于此架构的共享了解来交换信息。 只能对架构进行有限的更改，而不会中断协定，这就是大多数架构更改需要新版本的设备模板的原因。 版本控制设备模板允许较旧的设备继续使用其所理解的架构版本，而更新或更新的设备则使用更高版本的架构。

设备模板中的架构是在设备功能模型（DCM）及其接口中定义的。 设备模板包括其他信息，如云属性、显示自定义和视图。 如果在设备模板的这些部分中进行了更改，但未定义设备与 IoT Central 交换数据的方式，则无需对模板进行版本。

你必须发布任何设备模板更改，无论它们是否需要版本更新，操作员都可以使用它。 IoT Central 会阻止你在不首先对模板进行版本控制的情况下将重大更改发布到设备模板。

> [!NOTE]
> 若要详细了解如何创建设备模板，请参阅[设置和管理设备模板](howto-set-up-template.md)

## <a name="versioning-rules"></a>版本规则

本部分概述了适用于设备模板的版本控制规则。 DCMs 和接口都有版本号。 以下代码片段显示了环境传感器设备的 DCM。 DCM 有两个接口： **DeviceInformation**和**EnvironmentalSensor**。 您可以在`@id`字段末尾看到版本号。 若要在 IoT Central UI 中查看此信息，请在设备模板编辑器中选择 "**查看标识**"。

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* 发布 DCM 后，即使在新版本的设备模板中，也不能删除任何接口。
* 发布 DCM 后，可以在创建新版本的设备模板时添加接口。
* 发布 DCM 后，如果创建新版本的设备模板，则可以将接口替换为较新的版本。 例如，如果传感器 v1 设备模板使用 EnvironmentalSensor v1 接口，则可以创建使用 EnvironmentalSensor v2 接口的传感器 v2 设备模板。
* 接口发布后，即使在新版本的设备模板中，也不能删除任何接口内容。
* 在发布接口后，如果创建新版本的接口和设备模板，则可以向接口的内容中添加项。 可以添加到接口中的项包括遥测、属性和命令。
* 在发布接口后，如果创建新版本的接口和设备模板，则可以对接口中的现有项进行非架构更改。 接口项的非架构部分包括显示名称和语义类型。 无法更改的接口项的架构部分包括名称、功能类型和架构。

以下部分将指导你完成在 IoT Central 中修改设备模板的一些示例。

## <a name="customize-the-device-template-without-versioning"></a>自定义设备模板而不进行版本管理

无需对设备模板和接口进行版本控制，就能编辑设备功能的某些元素。 例如，其中的一些字段包括显示名称、语义类型、最小值、最大值、小数位数、颜色、单位、显示单位、注释和说明。 若要添加其中的某个自定义项：

1. 转到“设备模板”页。****
1. 选择要自定义的设备模板。
1. 选择“自定义”选项卡。****
1. 此处列出了设备功能模型中定义的所有功能。 您可以编辑、保存和使用所有这些字段，而无需对您的设备模板进行版本。 如果要编辑的字段是只读的，则必须对设备模板进行版本更改。 选择要编辑的字段，然后输入任何新值。
1. 单击“保存”  。 现在，这些值将覆盖在设备模板中最初保存并在应用程序中使用的所有内容。

## <a name="version-a-device-template"></a>版本设备模板

创建新版本的设备模板会创建模板的草稿版本，可在其中编辑设备功能模型。 任何已发布的接口都保持发布状态，直到它们被单独进行版本控制。 若要修改已发布的接口，请先创建新的设备模板版本。

仅当你尝试编辑无法在 "自定义" 部分中编辑的设备功能模型的一部分时，才会设置设备模板的版本。

版本设备模板：

1. 转到“设备模板”页。****
1. 选择要尝试版本的设备模板。
1. 单击页面顶部的“版本”按钮，并为模板指定新名称。**** IoT Central 建议一个新名称，您可以对其进行编辑。
1. 单击“创建”。 
1. 该设备模板现在处于草稿模式。 您可以看到您的接口仍处于锁定状态。 版本要修改的任何接口。

## <a name="version-an-interface"></a>版本接口

对接口进行版本控制可以在已创建的接口中添加、更新和删除功能。

对接口进行版本：

1. 转到“设备模板”页。****
1. 选择处于草稿模式的设备模板。
1. 选择处于已发布模式的、要进行版本控制和编辑的接口。
1. 单击接口页面顶部的“版本”按钮。****
1. 单击“创建”。 
1. 该接口现在处于草稿模式。 你可以在接口中添加或编辑功能，而不会破坏现有的自定义和视图。

## <a name="migrate-a-device-across-versions"></a>跨版本迁移设备

可以创建多个版本的设备模板。 随着时间的推移，将使用这些设备模板来连接多个设备。 可以将设备从设备模板的一个版本迁移到另一个版本。 以下步骤介绍如何迁移设备：

1. 转到“设备资源管理器”页。****
1. 选择需迁移到另一版本的设备。
1. 选择“迁移”。****
1. 选择具有要将设备迁移到的版本号的设备模板，然后选择“迁移”。****

![如何迁移设备](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>后续步骤

如果你是操作员或解决方案生成器，建议下一步是了解[如何管理你的设备](./howto-manage-devices.md)。

如果你是设备开发人员，建议下一步是了解[Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)。
