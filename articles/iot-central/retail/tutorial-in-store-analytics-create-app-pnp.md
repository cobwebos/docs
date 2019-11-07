---
title: 在 Azure IoT Central 中创建店内分析应用程序 | Microsoft Docs
description: 本教程演示如何在 IoT Central 中创建店内分析零售应用程序。 你将创建该应用程序，对其进行自定义并添加传感器设备。
services: iot-central
ms.service: iot-central
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 10/03/2019
ms.openlocfilehash: 0d586aaab4fc87bb671e67152cf66e9fb1447504
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495167"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>教程：在 Azure IoT Central 中创建店内分析应用程序

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程向解决方案构建者介绍如何创建 Azure IoT Central 店内分析应用程序。 示例应用程序适用于零售商店。 这是一种满足常见业务需求的解决方案，可监视和适应占用和环境情况。

你构建的示例应用程序包括三个实际设备：一个 Rigado Cascade 500 网关和两个 RuuviTag 传感器。 本教程还演示如何将应用程序模板中包含的模拟占用传感器用于测试用途。 Rigado C500 网关充当应用程序中的通信中心。 它与应用商店中的传感器通信，并管理其与云的连接。 RuuviTag 是一个环境传感器，提供温度、湿度和压力等遥测数据。 模拟占用传感器提供一种在应用商店的结帐区域中跟踪运动和状态的方法。 

本教程包括将 Rigado 和 RuuviTag 设备连接到应用程序的说明。 如果有其他网关和传感器，则仍可以按照步骤生成应用程序。 本教程还演示如何创建模拟 RuuviTag 传感器。 如果没有实际设备，则使用模拟传感器可以生成应用程序。 

开发结帐和条件监视解决方案包括三个部分：

* 创建应用程序并将设备连接到监视条件
* 自定义仪表板，使操作员能够监视和管理设备
* 配置数据导出，使商店经理能够运行分析和可视化见解

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 使用 Azure IoT Central“店内分析 - 结帐”模板来创建零售商店应用程序 
> * 自定义应用程序设置
> * 创建和自定义 IoT 设备模板
> * 将设备连接到应用程序
> * 添加规则和操作来监视条件

## <a name="prerequisites"></a>先决条件

若要完成本教程系列，需要：
* 建议使用 Azure 订阅。 可以选择使用 7 天免费试用版。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上创建一个。
* 访问网关设备和两个环境传感器（可以选择使用如本教程中所述的模拟设备）
* 你使用的设备的设备模板（为本教程中使用的所有设备提供模板）

## <a name="create-an-application"></a>创建应用程序
在本部分中，将从模板创建新的 Azure IoT Central 应用程序。 你将在本系列教程中使用此应用程序来生成完整的解决方案。

若要创建新的 Azure IoT Central 应用程序：

1. 导航到 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站。

1. 如果你有一个 Azure 订阅，请使用用于访问该订阅的凭据登录，否则请使用 Microsoft 帐户登录：

    ![输入组织帐户](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. 若要开始创建新的 Azure IoT Central 应用程序，请选择“新建应用程序”  。

1. 选择“零售”  。  “零售”页将显示多个零售应用程序模板。

若要创建使用预览功能的新店内分析结帐应用程序，请执行以下操作：  

1. 选择“店内分析 - 结帐”应用程序模板  。 此模板包括本教程中使用的所有设备（RuuviTag 传感器除外）的设备模板。 此模板还提供用于监视结帐和环境条件以及设备状态的操作员仪表板。 

1. （可选）选择一个友好的应用程序名称  。 此应用程序基于名为 Contoso 的虚构零售商店。 本教程使用应用程序名称 Contoso 结帐   。 应用程序模板基于虚构公司 Northwind。 在本教程中，你将使用 Contoso 来了解如何自定义应用程序。

    > [!NOTE]
    > 如果你使用友好的应用程序名称，则仍必须为应用程序 URL 使用唯一值   。

1. 如果你有一个 Azure 订阅，请输入目录、Azure 订阅和区域  。 如果你没有订阅，则可以启用 7 天免费试用版并填写所需的联系人信息  。  

    有关目录和订阅的详细信息，请参阅[创建应用程序快速入门](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

1. 选择“创建”  。

    ![Azure IoT Central 的“创建应用程序”页](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template.png)

## <a name="customize-application-settings"></a>自定义应用程序设置
构建者可以更改多个设置来自定义应用程序中的用户体验。 在本部分中，你将选择预定义的应用程序主题。 你还可以了解如何创建自定义主题，以及如何更新应用程序图像。 使用自定义主题可以设置刊头中显示的应用程序浏览器颜色、浏览器图标和应用程序徽标。

若要选择预定义的应用程序主题，请执行以下操作：

1. 在刊头上选择“设置”  。

    ![Azure IoT Central 应用程序设置](./media/tutorial-in-store-analytics-create-app-pnp/settings-icon.png)

2. 选择新的“主题”  。

3. 选择“保存”。 

可以创建自定义主题，而不是使用预定义的主题。 如果要使用一组示例图像自定义应用程序并完成本教程，请下载 [Contoso 示例图像](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)。

若要创建自定义主题，请执行以下操作：

1. 展开左窗格（如果尚未展开）。

    ![Azure IoT Central 左窗格](./media/tutorial-in-store-analytics-create-app-pnp/dashboard-expand.png)

1. 选择“管理”>“自定义应用程序”  。

1. 使用“更改”按钮选择要作为应用程序徽标上传的图像   。 （可选）为徽标替换文字  指定一个值。 

1. 使用“更改”按钮选择将显示在浏览器选项卡上的浏览器图标图像   。

1. （可选）通过添加 HTML 十六进制颜色代码来替换默认浏览器颜色  。 对于标题，添加 #008575   。  对于主题色，添加 #A1F3EA   。 

1. 选择“保存”。  

    ![Azure IoT Central 自定义徽标](./media/tutorial-in-store-analytics-create-app-pnp/select-application-logo.png)

    保存后，应用程序将更新浏览器颜色、刊头中的徽标以及浏览器图标。 

    ![Azure IoT Central 更新的应用程序设置](./media/tutorial-in-store-analytics-create-app-pnp/saved-application-settings.png)

若要更新应用程序图像，请执行以下操作：

1. 选择“管理”>“应用程序设置”  。

1. 使用“选择图像”按钮选择要作为应用程序图像上传的图像  。 此图像显示在 IoT Central 应用程序管理器的“我的应用”  页中的应用程序磁贴上。

1. 选择“保存”。 

1. （可选）导航到 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上的“我的应用”视图  。 应用程序磁贴将显示更新的应用程序图像。

    ![Azure IoT Central 自定义应用程序图像](./media/tutorial-in-store-analytics-create-app-pnp/customize-application-image.png)

## <a name="create-device-templates"></a>创建设备模板
构建者可以创建使你和应用程序操作员能够配置和管理设备的设备模板。 可以通过以下方式创建模板：生成自定义模板，导入现有模板文件，或从 Azure IoT 设备目录导入模板。 创建并自定义设备模板后，使用该模板将实际设备连接到应用程序。 （可选）使用设备模板生成用于测试的模拟设备。

 “店内分析 - 结帐”应用程序模板包含多个设备的设备模板。  有设备模板可用于应用程序中使用的三个设备中的两个设备。 RuuviTag 设备模板不包含在“店内分析 - 结帐”应用程序模板中  。 在本部分中，你将 RuuviTag 传感器的设备模板添加到应用程序。

若要将 RuuviTag 设备模板添加到应用程序，请执行以下操作：

1. 在左窗格中选择“设备模板”  。

1. 选择“+ 新建”以创建新的设备模板  。

1. 在 Azure IoT 设备目录中查找并选择 RuuviTag 传感器设备模板  。 

1. 在完成时选择“下一步:  自定义”。

    ![Azure IoT Central RuuviTag 传感器设备模板](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template.png)

1. 选择“创建”  。 应用程序将添加 RuuviTag 设备模板。

1. 在左窗格中选择“设备模板”  。 页面将显示应用程序模板中包含的所有设备模板以及刚刚添加的 RuuviTag 设备模板。

    ![Azure IoT Central RuuviTag 传感器设备模板](./media/tutorial-in-store-analytics-create-app-pnp/device-templates-list.png)

## <a name="customize-device-templates"></a>自定义设备模板
可以通过三种方式自定义应用程序中的设备模板。 第一种方式是通过更改设备功能来自定义设备中的本机内置界面。 例如，使用温度传感器可以更改温度界面的显示名称、数据类型、度量单位、最小和最大工作范围等详细信息。 

第二种方式是通过添加云属性来自定义设备模板。 云属性不属于内置设备功能。 云属性是 Azure IoT Central 应用程序创建、存储并与设备关联的自定义数据。 云属性的示例可以是计算值，也可以是要与一组设备关联的位置等元数据。 

第三种方式是通过生成自定义视图来自定义设备模板。 视图为操作员提供一种可视化设备的遥测和元数据（如设备指标和运行状况）的方法。

此处，使用前两种方法来自定义 RuuviTag 传感器的设备模板。 有关为传感器创建视图的信息，请参阅[将模拟设备添加到 IoT Central 应用程序](../core/quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)快速入门。

若要自定义 RuuviTag 设备模板的内置界面，请执行以下操作：

1. 在左窗格中选择“设备模板”  。 

1. 选择 RuuviTag 传感器的模板。 

1. 隐藏左窗格。 模板的摘要视图将显示设备功能。

    ![Azure IoT Central RuuviTag 设备模板摘要视图](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-summary-view.png)

1. 在 RuuviTag 设备模板菜单中选择“自定义”  。 

1. 在功能列表中滚动并查找 `humidity` 遥测类型。 它是具有湿度的可编辑显示名称值的行项   。

在以下步骤中，你将为 RuuviTag 传感器自定义 `humidity` 遥测类型。 （可选）自定义一些其他遥测类型。

对于 `humidity` 遥测类型，进行以下更改：

1. 选择“展开”控件以展开该行的架构详细信息  。

1. 将“湿度”中的显示名称更新为自定义值，例如“相对湿度”    。

1. 将“语义类型”选项从“无”更改为“湿度”    。  （可选）在展开的架构视图中设置湿度遥测类型的架构值。 架构设置允许你为传感器跟踪的数据创建详细的验证要求。例如，可以为给定界面设置最小和最大工作范围值。

1. 选择“保存”  以保存更改。

    ![Azure IoT Central RuuviTag 设备模板自定义](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-customize.png)

若要将云属性添加到应用程序中的设备模板，请执行以下操作：

1. 在 RuuviTag 设备模板菜单中选择“云属性”  。

1. 选择“添加云属性”  。 

指定以下值以创建自定义属性来存储每个设备的位置：

1. 输入“显示名称”的“位置”值   。 此值将自动复制到“名称”  字段，它是属性的友好名称。 可以使用复制的值或对其进行更改。

1. 在“架构”下拉列表中选择“字符串”   。 使用字符串类型可以基于模板将位置名称字符串与任何设备关联。 例如，可以将应用商店中的某个区域与每个设备关联。 （可选）可以将属性的“语义类型”设置为“位置”，这会自动将“架构”设置为“地域点”     。 这使你可以将 GPS 坐标与设备关联。 

1. 将“最小长度”设置为“2”   。 

1. 将“修整空白”设置为“打开”   。

1. 选择“保存”  以保存自定义云属性。

    ![Azure IoT Central RuuviTag 设备模板自定义](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-cloud-property.png)

1. 选择“发布”  。 

    发布设备模板使其对应用程序操作员可见。 发布模板后，可以使用它来生成用于测试的模拟设备，或者将实际设备连接到应用程序。 如果已将设备连接到应用程序，则发布自定义模板会将更改推送到设备。

## <a name="add-devices"></a>添加设备
创建并自定义设备模板后，就可以添加设备了。 

对于本教程，使用以下一组实际和模拟设备来生成应用程序：
- 实际 Rigado C500 网关
- 两个实际 RuuviTag 传感器
- 模拟占用传感器  。 模拟传感器包含在应用程序模板中，因此不需要创建它。 

> [!NOTE]
> 如果没有实际设备，则仍可以通过创建模拟 RuuviTag 传感器来完成本教程。 以下说明包含创建模拟 RuuviTag 的步骤。 不需要创建模拟网关。

完成以下两篇文章中的步骤以连接实际 Rigado 网关和 RuuviTag 传感器。 完成后，请返回到本教程。 由于你已在本教程中创建了设备模板，因此不需要按照以下一组说明再次创建设备模板。

- 若要连接 Rigado 网关，请参阅[将 Rigado Cascade 500 连接到 Azure IoT Central 应用程序](../core/howto-connect-rigado-cascade-500-pnp.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)。
- 若要连接 RuuviTag 传感器，请参阅[将 RuuviTag 传感器连接到 Azure IoT Central 应用程序](../core/howto-connect-ruuvi-pnp.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)。 还可以使用这些说明创建两个模拟传感器（如果需要）。

## <a name="add-rules-and-actions"></a>添加规则和操作
使用 Azure IoT Central 应用程序中的传感器监视条件时，可以创建规则以在满足某些条件时运行操作。 规则与设备模板和一个或多个设备关联，并且包含必须根据设备遥测或事件满足的条件。 规则也有一个或多个关联的操作。 这些操作可能包括发送电子邮件通知，或触发 Webhook 操作以向其他服务发送数据。  “店内分析 - 结帐”应用程序模板包括应用程序中设备的一些预定义规则。

在本部分中，你将创建一个新规则，用于根据 RuuviTag 传感器遥测检查最大相对湿度级别。 向该规则添加操作，以便在湿度超过最大值时，应用程序发送电子邮件。 

若要创建规则，请执行以下操作： 

1. 展开左窗格。

1. 选择“规则”。 

1. 选择“+ 新建”  。

1. 输入“湿度级别”  作为规则的名称。 

1. 选择“范围”  中的 RuuviTag 设备模板。 你定义的规则将基于该模板应用于所有传感器。 或者，你可以创建仅将规则应用于已定义的传感器子集的筛选器。 

1. 选择 `Relative humidity` 作为“遥测”  。 这是上一步中自定义的设备功能。

1. 选择 `Is greater than` 作为“运算符”  。 

1. 输入环境的典型范围上限室内湿度级别作为“值”  。 例如，输入“65”  。 已为在任何 RuuviTag 实际或模拟传感器中的相对湿度超出此值时发生的规则设置条件。 可能需要根据环境中的正常湿度范围向上或向下调整值。  

   ![Azure IoT Central 添加规则条件](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-conditions.png)

若要向规则添加操作，请执行以下操作：

1. 选择“+ 电子邮件”  。 

1. 输入“高湿度通知”  作为操作的友好显示名称  。 

1. 在“收件人”中输入与帐户关联的电子邮件地址  。 如果使用其他电子邮件，则所使用的地址必须是已添加到应用程序的用户的地址。 该用户还需要至少进行一次登录和注销。

1. （可选）输入要包含在电子邮件文本中的注释。

1. 选择“完成”  ，完成操作。

   ![Azure IoT Central 向规则添加操作](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-action.png)

1. 选择“保存”  以保存并激活新规则。 

    几分钟后，指定的电子邮件帐户应开始收到电子邮件。 每当传感器指示湿度级别超出条件中的值时，应用程序都会发送电子邮件。

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

* 使用 Azure IoT Central“店内分析 - 结帐”模板来创建零售商店应用程序 
* 自定义应用程序设置
* 创建和自定义 IoT 设备模板
* 将设备连接到应用程序
* 添加规则和操作来监视条件

请注意，在创建 Azure IoT Central 条件监视应用程序之后，建议接下来执行以下后续步骤：

> [!div class="nextstepaction"]
> [自定义操作员仪表板](./tutorial-in-store-analytics-customize-dashboard-pnp.md)
