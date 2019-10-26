---
title: 了解 Azure IoT Central 应用的设备模板版本控制 | Microsoft Docs
description: 通过创建新版本循环访问设备模板，不影响实时连接设备
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 79a04725c8289baa3008b229d5dc608357cde881
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949594"
---
# <a name="create-a-new-device-template-version-preview-features"></a>创建新的设备模板版本（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central 用于快速开发 IoT 应用程序。 通过添加、编辑或删除设备功能、视图和自定义，可以快速遍历设备模板设计。 发布设备模板后，设备功能模型将显示为 "**已发布**"，并在模型旁边显示锁定图标。 若要对设备功能模型进行更改，需要创建新版本的设备模板。 同时，可以随时编辑云属性、自定义和视图，而无需对设备模板进行版本设置。 保存这些更改中的任何一项后，可以发布设备模板，使操作员能够在 Device Explorer 中查看最新的更改。

> [!NOTE]
> 若要了解有关如何创建设备模板的详细信息，请参阅[设置和管理设备模板](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>将自定义添加到设备模板而不进行版本控制

可以编辑设备功能的某些元素，而无需对设备模板和接口进行版本。 例如，其中一些字段包含显示名称、语义类型、最小值、最大值、小数位数、颜色、单位、显示单位、注释和说明。 添加以下自定义之一：

1. 请参阅**设备模板**页。
1. 选择要自定义的设备模板。
1. 选择 "**自定义**" 选项卡。
1. 你的设备功能模型中定义的所有功能将在此处列出。 可在此处编辑的所有字段都可以在应用程序中保存和使用，而无需对设备模板进行版本。 如果要编辑的字段是只读的，则需要对设备模板进行版本更改。 选择要编辑的字段，然后在任何新值中输入。
1. 单击“保存”。 现在，这些值将覆盖你的设备模板中最初保存的所有内容，并将在应用程序中使用。

## <a name="versioning-a-device-template"></a>设备模板的版本控制

创建新版本的设备模板将创建模板的草稿版本，可在其中编辑设备功能模型。 任何已发布的接口都将保持发布状态，直到它们被单独进行版本控制。 为了修改已发布的接口，您必须首先创建一个新的设备模板版本。

仅当你尝试编辑无法在设备模板的 "自定义" 部分中编辑的设备功能模型的一部分时，才应对设备模板进行版本控制。 

若要版本设备模板：

1. 请参阅**设备模板**页。
1. 选择要尝试版本的设备模板。
1. 单击页面顶部的 "**版本**" 按钮，并为模板指定一个新名称。 我们建议你可以编辑的新名称。
1. 单击“创建”。
1. 现在，你的设备模板处于草稿模式。 您将看到您的接口仍处于锁定状态，并且必须单独进行版本控制才能进行编辑。 

### <a name="versioning-an-interface"></a>对接口进行版本控制

使用版本控制接口，可以添加、更新和删除已创建的接口内的功能。 

若要为接口版本：

1. 请参阅**设备模板**页。
1. 选择处于草稿模式的设备模板。
1. 选择要进行版本和编辑的已发布模式下的接口。
1. 单击 "界面" 页顶部的 "**版本**" 按钮。 
1. 单击“创建”。
1. 现在界面处于草稿模式。 你将能够向接口添加或编辑功能，而不会破坏现有的自定义和视图。 

> [!NOTE]
> Azure IoT 发布的标准接口无法进行版本控制或编辑。 这些标准接口用于设备证书。

> [!NOTE]
> 接口发布后，即使在草稿模式下，也不能删除任何此项功能。 功能只能在草稿模式下编辑或添加到接口。


## <a name="migrate-a-device-across-device-template-versions"></a>跨设备模板版本迁移设备

可以创建多个版本的设备模板。 一段时间过后，会有多个使用这些设备模板的连接设备。 可以将设备从设备模板的一个版本迁移到另一个版本。 以下步骤介绍如何迁移设备：

1. 请参阅**Device Explorer**页面。
1. 选择需迁移到另一版本的设备。
1. 选择 "**迁移**"。
1. 选择具有要将设备迁移到的版本号的设备模板，然后选择 "**迁移**"。

![如何迁移设备](media/howto-version-device-template-pnp/pick-version.png)

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中使用设备模板版本后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [如何创建遥测规则](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
