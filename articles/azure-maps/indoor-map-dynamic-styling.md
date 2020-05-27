---
title: 实现 Azure Maps Creator 室内定位的动态样式
description: 了解如何实现 Creator 室内定位的动态样式
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6d31edaea109f2969cc68c566594dc436be203d7
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743549"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>实现 Creator 室内定位的动态样式

借助 Azure Maps Creator [特征状态服务](https://docs.microsoft.com/rest/api/maps/featurestate)，可以根据室内定位数据特征的动态属性来应用样式。  例如，可以使用特定颜色来渲染设施会议室，以反映占用状态。 本文将介绍如何使用[特征状态服务](https://docs.microsoft.com/rest/api/maps/featurestate)和[室内 Web 模块](how-to-use-indoor-module.md)来动态渲染室内定位特征。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。
3. [创建 Creator 资源](how-to-manage-creator.md)
4. 下载[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。
5. [创建室内定位](tutorial-creator-indoor-maps.md)，以获取 `tilesetId` 和 `statesetId`。
6. 按照[如何使用“室内定位”模块](how-to-use-indoor-module.md)中的步骤操作，以生成 Web 应用。

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="implement-dynamic-styling"></a>实现动态样式

完成先决条件后，应该有一个配置有订阅密钥、`tilesetId` 和 `statesetId` 的简单 Web 应用。

### <a name="select-features"></a>选择特征

必须按特征 `id` 引用特征（如会议室），才能实现动态样式。 将使用特征 `id` 来更新相应特征的动态属性或状态。 若要查看数据集中定义的特征，可以使用以下方法之一：

* WFS API（Web 特征服务）。 可以使用 WFS API 查询数据集。 WFS 沿用开放地理空间信息联盟 API 特征。 WFS API 有助于查询数据集中的特征。 例如，可以使用 WFS 来查找给定设施和楼层的所有中型会议室。

* 实现自定义代码，让用户能够使用 Web 应用来选择定位上的特征。 本文将使用这种方法。  

下面的脚本实现鼠标单击事件。 此代码根据单击点来检索特征 `id`。 在应用中，可以在室内管理器代码块下面插入此代码。 运行应用，然后检查控制台，以获取单击点的特征 `id`。

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor")

    var result = features.reduce(function (ids, feature) {
        if (feature.layer.id == "indoor_unit_office") {
            console.log(feature);
        }
    }, []);
});
```

[创建室内定位](tutorial-creator-indoor-maps.md)教程将特征状态集配置为接受 `occupancy` 的状态更新。

下一部分将把办公室 `UNIT26` 的占用状态设置为 `true`， 而办公室 `UNIT27` 的占用状态则设置为 `false`。

### <a name="set-occupancy-status"></a>设置占用状态

 现在要更新 `UNIT26` 和 `UNIT27` 这两间办公室的状态：

1. 在 Postman 应用中，选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中输入名称，然后选择一个集合。 单击“保存”

2. 使用[特征更新状态 API](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview) 来更新状态。 为两个单元之一传递状态集 ID 和 `UNIT26`。 追加 Azure Maps 订阅密钥。 以下是用于更新状态的 POST 请求的 URL：

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 在 POST 请求的“头”中，将“`Content-Type`”设置为“`application/json`”。 在 POST 请求的“正文”中，编写以下包含特征更新的 JSON。 只有当 POST 请求的时间戳晚于同一特征 `ID` 的上一特征状态更新请求的时间戳时，才会保存更新。 传递“occupied”`keyName`来更新它的值。

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. 使用 `UNIT27` 重做第 2 步和第 3 步，JSON 如下所示。

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>可视化定位上的动态样式

之前在浏览器中打开的 Web 应用现在应该会反映定位特征的更新后状态。 `UNIT27`(151) 应显示为绿色，而 `UNIT26`(157) 应显示为红色。

![可用会议室显示为绿色，被占用的会议室显示为红色](./media/indoor-map-dynamic-styling/room-state.png)

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请阅读以下文章：

> [!div class="nextstepaction"]
> [适用于室内定位的 Creator](creator-indoor-maps.md)

有关本文中提到的 API，请参阅以下参考资料：

> [!div class="nextstepaction"]
> [数据上传](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [数据转换](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [数据集](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [图块集](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [特征状态集](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS 服务](creator-indoor-maps.md#web-feature-service-api)

