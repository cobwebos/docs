---
title: 快速入门：使用 Azure 门户部署 IoT 连接器（预览版）
description: 本快速入门介绍如何使用 Azure 门户部署、配置和使用 Azure API for FHIR 的 IoT 连接器功能。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: 95f5b5f13401c224ccf67c5f013deedf00379de7
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446802"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>快速入门：使用 Azure 门户部署 IoT 连接器（预览版）

IoT 连接器是 Azure API for FHIR 的一项可选功能，使你能够引入来自医疗物联网 (IoMT) 设备的数据。 在预览阶段，IoT 连接器功能免费提供。 在本快速入门中，你将学习如何：
- 使用 Azure 门户部署和配置 IoT 连接器
- 使用模拟设备将数据发送到 IoT 连接器
- 查看由 Azure API for FHIR 的 IoT 连接器创建的资源

## <a name="prerequisites"></a>先决条件

- 有效的 Azure 订阅 - [免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR 资源 - [使用 Azure 门户部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>转到 Azure API for FHIR 资源

打开 [Azure 门户](https://portal.azure.com)并转到要为其创建 IoT 连接器功能的 Azure API for FHIR 资源。

[![Azure API for FHIR 资源](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

在左侧导航菜单上，单击“加载项”部分下的“IoT 连接器(预览版)”，以打开“IoT 连接器”页面  。

[![IoT 连接器功能](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>创建新的 IoT 连接器（预览版）

单击“添加”按钮以打开“创建 IoT 连接器”页面 。

[![添加 IoT 连接器](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

输入新 IoT 连接器的设置。 单击“创建”按钮并等待 IoT 连接器部署。

> [!NOTE]
> 必须选择“创建”作为此次安装的“解决方法类型”的值 。 

[![创建 IoT 连接器](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|设置|值|说明 |
|---|---|---|
|连接器名称|唯一的名称|输入一个用于识别 IoT 连接器的名称。 此名称在 Azure API for FHIR 中应是唯一的。 名称只能包含小写字母、数字和连字符 (-)。 它必须以字母或数字开头和结尾，且长度必须介于 3-24 个字符之间。|
|解决方法类型|查找或创建|如果 Azure API for FHIR 中有一个用于创建[设备](https://www.hl7.org/fhir/device.html)和[患者](https://www.hl7.org/fhir/patient.html) FHIR 资源的带外进程，请选择“查找”。 创建[观察](https://www.hl7.org/fhir/observation.html) FHIR 资源时，IoT 连接器将使用对这些资源的引用来显示设备数据。 如果希望 IoT 连接器使用设备数据中显示的相应标识符值在 Azure API for FHIR 中创建基本的设备和患者资源，请选择“创建”。|

安装完成后，新创建的 IoT 连接器将显示在“IoT 连接器”页上。

[![已创建 IoT 连接器](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>配置 IoT 连接器（预览版）

IoT 连接器需要两个映射模板来将设备消息转换为基于 FHIR 的观察资源：设备映射和 FHIR 映射 。 必须上传这些映射，IoT 连接器才能正常运行。

[![IoT 连接器缺少映射](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

若要上传映射模板，请单击新部署的 IoT 连接器以转到“IoT 连接器”页面。

[![单击 IoT 连接器](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>设备映射

设备映射模板将设备数据转换为规范化架构。 在“IoT 连接器”页上，单击“配置设备映射”按钮以转到“设备映射”页面  。 

[![在 IoT 连接器处单击“配置设备映射”](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

在“设备映射”页上，将以下脚本添加到 JSON 编辑器并单击“保存” 。

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT 连接器设备映射](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)


#### <a name="fhir-mapping"></a>FHIR 映射

FHIR 映射模板将规范化消息转换为基于 FHIR 的观察资源。 在“IoT 连接器”页上，单击“配置 FHIR 映射”按钮以转到“FHIR 映射”页面  。  

[![在 IoT 连接器处单击“配置 FHIR 映射”](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

在“FHIR 映射”页上，将以下脚本添加到 JSON 编辑器并单击“保存” 。

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT 连接器 FHIR 映射](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>生成连接字符串

IoMT 设备需要连接字符串才能连接到 IoT 连接器并向其发送消息。 在“IoT 连接器”页上，对于新部署的 IoT 连接器，选择“管理客户端连接”按钮 。 

[![在 IoT 连接器处单击“管理客户端连接”](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

在“连接”页上，单击“添加”按钮以创建新连接 。 

[![IoT 连接器连接](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

在覆盖窗口上为此连接提供一个易记名称，然后选择“创建”按钮。

[![IoT 连接器新建连接](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

选择“连接”页中新创建的连接，并从右侧覆盖窗口复制“主连接字符串”字段的值 。

[![IoT 连接器连接字符串](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

保留此连接字符串，后面的步骤中会使用它。 

## <a name="connect-your-devices-to-iot"></a>将设备连接到 IoT

Azure 提供了一套全面的 IoT 产品，用于连接和管理 IoT 设备。 你可以使用 Azure IoT 中心并基于 PaaS 构建自己的解决方案，也可以使用 Azure IoT Central 管理 IoT 应用平台。 在本教程中，我们将使用 Azure IoT Central，它包含行业专用的解决方案模板来帮助你入门。

部署[持续患者监视应用程序模板](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template)。 此模板包括两个模拟设备，可生成实时数据以帮助你入门：Smart Vitals Patch 和 Smart Knee Brace 。

> [!NOTE]
> 实际设备准备就绪后，可以使用相同的 IoT Central 应用程序[加入设备](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template)并替换设备模拟器。 设备数据也将自动开始流向 FHIR。 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>使用 IoT 连接器（预览版）连接 IoT 数据
部署 IoT Central 应用程序后，两个现成的模拟设备将开始生成遥测。 在本教程中，我们将通过 IoT 连接器将遥测从 Smart Vitals Patch 模拟器引入 FHIR。 若要将 IoT 数据导出到 IoT 连接器，需要[在 IoT Central 内设置连续数据导出](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export)。 在“连续数据导出”页上：
- 选择“Azure 事件中心”作为导出目标。
- 选择“事件中心命名空间”字段的“使用连接字符串”值。
- 为“连接字符串”字段提供在上一步中获取的 IoT 连接器的连接字符串。
- 对于“要导出的数据”字段，保持“遥测”选项为启用状态。

## <a name="view-device-data-in-azure-api-for-fhir"></a>查看 Azure API for FHIR 中的设备数据

可以使用 Postman 查看由 Azure API for FHIR 的 IoT 连接器创建的基于 FHIR 的观察资源。 设置 [Postman 以访问 Azure API for FHIR](access-fhir-postman-tutorial.md)，并向 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 发出 `GET` 请求，以查看具有心率值的观察 FHIR 资源。 

> [!TIP]
> 确保用户可以访问 Azure API for FHIR 数据平面。 使用 [Azure 基于角色的访问控制 (Azure RBAC)](configure-azure-rbac.md) 分配所需的数据平面角色。

## <a name="clean-up-resources"></a>清理资源

如果不再需要 IoT 连接器的实例，可以通过删除关联的资源组或关联的 Azure API for FHIR 服务来删除该实例，也可直接删除该 IoT 连接器实例本身。 

若要直接删除 IoT 连接器实例，请转到“IoT 连接器”页面，从中选择该实例，再单击“删除”按钮  。 要求进行确认时，选择“是”。 

[![删除 IoT 连接器实例](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>后续步骤

在本快速入门指南中，你已在 Azure API for FHIR 资源中部署了 IoT 连接器功能。 选择下面的后续步骤以了解有关 IoT 连接器的详细信息：

了解 IoT 连接器中数据流的不同阶段。

>[!div class="nextstepaction"]
>[IoT 连接器数据流](iot-data-flow.md)

了解如何使用设备和 FHIR 映射模板配置 IoT 连接器。

>[!div class="nextstepaction"]
>[IoT 连接器映射模板](iot-mapping-templates.md)

FHIR 是 HL7 的注册商标，经 HL7 许可使用。
