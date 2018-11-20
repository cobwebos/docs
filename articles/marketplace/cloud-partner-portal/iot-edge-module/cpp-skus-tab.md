---
title: Azure IoT Edge 模块 SKU | Microsoft Docs
description: 为 IoT Edge 模块创建 SKU。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 205fd258ed397f5a9588773549368fc3c4aec058
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684811"
---
# <a name="iot-edge-module-skus-tab"></a>IoT Edge 模块“SKU”选项卡

在“新建套餐”页的“SKU”选项卡中，可以创建一个或多个 SKU 并将其关联到新套餐。  可以使用不同的 SKU 按功能集、计费模型或其他某种特征来区分解决方案。

## <a name="sku-settings"></a>SKU 设置

开始创建新套餐时，该套餐没有任何关联的 SKU。 若要创建新 SKU，请执行以下步骤：

- 在“IoT Edge 模块”>“新建套餐”页上，选择“SKU”选项卡。
- 在“SKU”下，选择“+ 新建 SKU”打开一个对话框。

  ![IoT Edge 模块的“新建套餐”选项卡上的“新建 SKU”按钮](./media/iot-edge-module-skus-tab-new-sku.png)

- 在“新建 SKU”对话框中输入 SKU 的标识符，然后选择“确定”。
（下表提供了标识符命名约定。）

随后“SKU”选项卡将会刷新，显示配置 SKU 时所要编辑的字段。 名称旁边附有星号 (*) 的字段表示必填字段。

|  **字段**       |     **说明**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU ID**       | 此 SKU 的标识符。 此名称最多包含 50 个字符，包括小写字母数字字符或短划线 (-)，但不能以短划线结尾。 **注意：** 发布套餐后无法更改此名称。 此名称将在产品 URL 中公开显示。 |

## <a name="sku-details"></a>SKU 详细信息

配置“SKU 详细信息”，以定义 SKU 在 Azure 市场和 Azure 门户网站上的显示方式。

![IoT Edge 模块 SKU 元数据](media/iot-edge-module-skus-tab-metadata.png)

下表描述了“SKU 详细信息”下的字段的用途、内容和格式。

|  **字段**       |     **说明**                                                          |
|  ---------       |     ---------------                                                          |
| **标题**        | 此 SKU 的标题。 最大长度为 50 个字符。 <br/> 它将显示在 Azure 门户中，并在部署模块时用作默认模块名称（不包含空格和特殊字符）。 请参阅下图了解此字段的确切显示位置。|
| **摘要**      | 此 SKU 的简短摘要。 最大长度为 100 个字符。 **不是**套餐的摘要，而只是 SKU 的摘要。  此摘要将显示在 Azure 市场中。 请参阅下图了解此字段的确切显示位置。|
| **说明**  | 此 SKU 的简短说明。最大长度为 3000 个字符。 不是描述套餐，而只是描述此 SKU。 它将显示在 Azure 市场和 Azure 门户中。 在 Azure 门户中，它将追加到描述“市场”选项卡中所定义的套餐的“市场说明”。此说明可与 SKU 摘要内容相同。 请参阅下图了解此字段的确切显示位置。|
| **隐藏此 SKU** | 保留默认设置，即“否”。 |

### <a name="sku-example"></a>SKU 示例

 以下示例演示了 SKU 的“标题”、“摘要”和“说明”字段在不同视图中的显示方式。
 
#### <a name="on-the-azure-marketplace-website"></a>在 Azure 市场网站上：

- 查看 SKU 详细信息时：

    ![SKU 在 Azure 市场网站中的显示方式](media/iot-edge-module-ampdotcom-pdp-plans.png)

#### <a name="on-the-azure-portal-website"></a>在 Azure 门户网站上：

- 浏览 SKU 时：

    ![浏览 Azure 门户时 IoT Edge 模块的显示方式 #1](media/iot-edge-module-portal-browse.png)

    ![浏览 Azure 门户时 IoT Edge 模块的显示方式 #2](media/iot-edge-module-portal-product-picker.png)

- 搜索 SKU 时：

    ![在 Azure 门户中搜索时 IoT Edge 模块的显示方式](media/iot-edge-module-portal-search.png)

- 查看 SKU 详细信息时：

    ![在门户中查看产品详细信息时 IoT Edge 模块的显示方式](./media/iot-edge-module-portal-pdp.png)

- 部署模块时：
    
    ![IoT Edge 模块在部署时的显示方式](./media/iot-edge-module-deployment.png)

## <a name="sku-content"></a>SKU 内容

在“Edge 模块映像”下，提供在上传 IoT Edge 模块时所需的信息。

请向我们授予包含 IoT Edge 模块映像的 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/) (ACR) 的访问权限，以便我们能够上传并验证该模块。 发布后，将使用 Azure 市场托管的公共容器注册表复制并分发你的 IoT Edge 模块。

可以面向多个平台，并通过标记提供多个版本。 在[准备 IoT Edge 模块技术资产](./cpp-create-technical-assets.md)中详细了解标记和版本控制。

![IoT Edge 模块映像](./media/iot-edge-module-skus-tab-acr.png)

下表描述了相应字段的用途、内容和格式：

- **映像存储库详细信息**
- **映像版本**

|  **字段**       |     **说明**                                                          |
|  ---------       |     ---------------                                                          |
|  ***映像存储库详细信息***   |  |
| **订阅 ID**        | ACR 的 Azure 订阅 ID。|
| **资源组名称**      | ACR 的资源组名称。|
| **注册表名称**  | ACR 注册表名称。 仅复制注册表名称，而不复制登录服务器名称（例如，不包含 `azurecr.io`。） |
| **存储库名称**  | 包含 IoT Edge 模块的 ACR 的存储库名称。 **注意：** 名称一经设置，以后不可更改。 请使用唯一的名称，确保帐户中不会出现其他同名的套餐。 |
| **用户名** | 与 ACR 关联的用户名（管理员用户名）。 |
| **密码** | 与 ACR 关联的密码。 |
|  ***映像版本***   |  |
| **映像标记或摘要** | 必须至少包含一个 `latest` 标记和一个版本标记（例如，从 `xx.xx.xx-` 开始，其中 xx 是一个数字）。 它们应是面向多个平台的[清单标记](https://github.com/estesp/manifest-tool)。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。 可以使用标记添加 IoT Edge 模块的多个版本。 所有清单标记（`latest` 除外）必须以 `X.Y-` 或 `X.Y.Z-` 开头，其中，X、Y、Z 为整数。 在[准备 IoT Edge 模块技术资产](./cpp-create-technical-assets.md)中详细了解标记和版本控制。 <br/> 例如，如果 `latest` 标记指向 `1.0.1-linux-x64`、`1.0.1-linux-arm32` 和 `1.0.1-windows-arm32`，则此处需要添加这 6 个标记。 |

### <a name="help-your-customers-launch-your-iot-edge-module-by-using-default-settings"></a>帮助客户使用默认设置启动 IoT Edge 模块

定义最常见的设置来部署 IoT Edge 模块。 让客户使用这些默认设置以“开箱即用”的方式启动 IoT Edge 模块，以优化客户部署。

![部署时的 IoT Edge 模块默认设置](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

下表描述了“默认路由”、“默认孪生所需属性”、“默认环境变量”和“默认 CreateOptions”的字段的用途、内容和格式。

|  **字段**       |     **说明**                                                          |
|  ---------       |     ---------------                                                          |
| **默认路由**        | 每个默认路由名称和值所包含的字符必须少于 512 个。 最多可以定义 5 个默认路由。 确保在路由值中使用正确的[路由语法](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)。 若要引用模块，请使用其默认模块名称，即不包含空格和特殊字符的“SKU 标题”。 若要引用未知的其他模块，请使用 `<FROM_MODULE_NAME>` 约定来告知客户他们需要更新此信息。 详细了解 [IoT Edge 路由](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)。 <br/> 例如，如果模块 `ContosoModule` 在 `ContosoInput` 上侦听输入，并在 `ContosoOutput` 上侦听输出数据，则最好是定义以下 2 个默认路由：<br/>- 名称 #1：`ToContosoModule`<br/>- 值 #1：`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- 名称 #2：`FromContosoModuleToCloud`<br/>- 值 #2：`FROM /messages/modules/ContonsoModule/outputs/ContosoOuput INTO $upstream`<br/>  |
| **默认孪生所需属性**      | 每个默认孪生所需属性名称和值所包含的字符必须少于 512 个。 最多可以定义 5 个名称/值孪生所需属性。 孪生所需属性的值必须是有效的 JSON、未转义且不包含数组，最多可包含 4 个嵌套层次结构。 详细了解[孪生所需属性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。 <br/> 例如，如果某个模块通过孪生所需属性支持可动态配置的刷新率，则最好是定义以下默认孪生所需属性：<br/> - 名称 #1：`RefreshRate`<br/>- 值 #1：`60`|
| **默认环境变量**  | 每个默认环境变量名称和值所包含的字符必须少于 512 个。 最多可以定义 5 个名称/值环境变量。 <br/>例如，如果某个模块在启动之前需要接受使用条款，则你可以定义以下环境变量：<br/> - 名称 #1：`ACCEPT_EULA`<br/>- 值 #1：`Y`|
| **默认 createOptions**  | createOptions 包含的字符必须少于 512 个。 必须是有效的 JSON 且未转义。 详细了解 [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules)。 <br/> 例如，如果某个模块需要绑定某个端口，则你可以定义以下 createOptions：<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|

<br/> 选择“保存”以保存 SKU 设置。 

## <a name="next-steps"></a>后续步骤

使用[“市场”选项卡](./cpp-marketplace-tab.md)创建套餐的市场说明。
