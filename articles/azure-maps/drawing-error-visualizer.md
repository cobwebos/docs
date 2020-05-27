---
title: 使用 Azure Maps 绘图错误可视化工具
description: 本文介绍了如何可视化由 Creator 转换 API 返回的警告和错误。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 01d131833f36c1b46eea1b9c08339b067fb9e937
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681952"
---
# <a name="using-the-azure-maps-drawing-error-visualizer"></a>使用 Azure Maps 绘图错误可视化工具

绘图错误可视化工具是一款独立 Web 应用，用于显示在转换过程中检测到的[绘图包警告和错误](drawing-conversion-error-codes.md)。 错误可视化工具 Web 应用由一个静态页组成，可以在不连接到 Internet 的情况下使用此页。  可以使用错误可视化工具来根据[绘图包要求](drawing-requirements.md)修复错误和警告。 只有在检测到错误时，[Azure Maps 转换 API](https://docs.microsoft.com/rest/api/maps/conversion) 才会返回包含错误可视化工具链接的响应。

## <a name="prerequisites"></a>先决条件

必须先执行以下操作，然后才能下载绘图错误可视化工具：

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。
3. [创建 Creator 资源](how-to-manage-creator.md)

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="download"></a>下载

1. 将绘图包上传到 Azure Maps Creator 服务，以获取已上传包的 `udid`。 有关如何上传包的步骤，请参阅[上传绘图包](tutorial-creator-indoor-maps.md#upload-a-drawing-package)。

2. 至此，绘图包已上传，现在我们将使用已上传包的 `udid` 将此包转换为定位数据。 有关如何转换包的步骤，请参阅[转换绘图包](tutorial-creator-indoor-maps.md#convert-a-drawing-package)。

    >[!NOTE]
    >如果转换过程成功，则不会收到错误可视化工具链接。

3. 在 Postman 应用中的响应“头”选项卡下，查找由转换 API 返回的 `diagnosticPackageLocation` 属性。 此响应头应如以下 JSON 所示：

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. 通过向 `diagnosticPackageLocation` URL 发出 `HTTP-GET` 请求，下载绘图包错误可视化工具。

## <a name="setup"></a>设置

在从 `diagnosticPackageLocation` 链接下载的压缩包中，你会找到两个文件。

* VisualizationTool.zip：包含绘制错误可视化工具的源代码、媒体和网页。
* ConversionWarningsAndErrors.json：包含绘图错误可视化工具使用的警告、错误和其他详细信息的格式化列表。

解压缩 VisualizationTool.zip 文件夹。 它包含以下项：

* assets 文件夹：包含图像和媒体文件
* static 文件夹：源代码
* index.html 文件：Web 应用。

使用以下具有相应版本号的任意浏览器来打开 index.html 文件。 可以使用其他版本，但前提是此版本提供与所列版本等效的兼容行为。

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>使用绘图错误可视化工具

在启动“绘图错误可视化工具”工具后，你会看到上传页面。 上传页面包含一个拖放框。 拖放框也可以用作启动“文件资源管理器”对话框的按钮。

![绘图错误可视化工具应用 - 起始页](./media/drawing-errors-visualizer/start-page.png)

ConversionWarningsAndErrors.json 文件已被置于下载目录的根目录下。 若要加载 ConversionWarningsAndErrors.json，可以将此文件拖放到框中，也可以单击拖放框，在“文件资源管理器”对话框中查找此文件，然后上传此文件。

![绘制错误可视化工具应用 - 拖放以加载数据](./media/drawing-errors-visualizer/loading-data.gif)

在 ConversionWarningsAndErrors.json 文件加载后，你就会看到绘图包错误和警告的列表。 每个错误或警告都由图层、楼层和详细消息指定。 现在可以转到每个错误，以详细了解如何解决相应错误。  

![绘制错误可视化工具应用 - 错误和警告](./media/drawing-errors-visualizer/errors.png)

## <a name="next-steps"></a>后续步骤

在[绘图包符合要求](drawing-requirements.md)后，你就可以使用 [Azure Maps 数据集服务](https://docs.microsoft.com/rest/api/maps/conversion)将绘图包转换为数据集了。 然后，可以使用“室内定位”Web 模块来开发应用。 若要了解详细信息，请阅读以下文章：

> [!div class="nextstepaction"]
> [绘图转换错误代码](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [适用于室内定位的 Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [使用“室内定位”模块](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [实现室内定位的动态样式](indoor-map-dynamic-styling.md)