---
title: 使用 Azure Maps 室内定位模块
description: 了解如何使用 Microsoft Azure Maps 室内定位模块，通过嵌入模块的 JavaScript 库来呈现定位。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9b2a47cde4d79671aada7c280c2bffd9bb8fe759
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594026"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>使用 Azure Maps 室内定位模块

Azure Maps Web SDK 包括“Azure Maps 室内”模块。 通过“Azure Maps 室内”模块，可以呈现在 Azure Maps Creator 中创建的室内定位。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [创建 Creator 资源](how-to-manage-creator.md)
3. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。
4. 通过完成[创建室内定位教程](tutorial-creator-indoor-maps.md)获取 `tilesetId` 和 `statesetId`。
 需要使用这些标识符通过“Azure Maps 室内定位”模块来呈现室内定位。

## <a name="embed-the-indoor-maps-module"></a>嵌入室内定位模块

可以通过以下两种方式之一来安装和嵌入“Azure Maps 室内”模块。

若要使用“Azure Maps 室内”模块全球托管的 Azure 内容分发网络版本，请参阅以下 HTML 文件 `<head>` 元素中的 JavaScript 和样式表引用：

  ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
  ```

 或者，你可以下载“Azure Maps 室内”模块。 “Azure Maps 室内”模块包含用于访问 Azure Maps 服务的客户端库。 按照以下步骤安装室内模块，并将其加载到 Web 应用程序中。  
  
  1. 下载 [azure-maps-indoor package](https://www.npmjs.com/package/azure-maps-indoor)。
  
  2. 安装 NPM 包。 请确保在控制台中使用管理员权限：

      ```powershell
        >npm install azure-maps-control
        >npm install azure-maps-indoor
      ```

  3. 引用 HTML 文件 `<head>` 元素中的“Azure Maps 室内”模块 JavaScript 和样式表：

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
      ```

## <a name="instantiate-the-map-object"></a>实例化定位对象

首先，创建一个定位对象。 定位对象将在下一步中用于实例化室内管理器对象。  下面的代码演示如何实例化定位对象：

```javascript
  const subscriptionKey = "<your Azure Maps Primary Subscription Key>";

  const map = new atlas.Map("map-id", {
    //use your facility's location
    center: [-122.13315, 47.63637],
    //or, you can use bounds: [#,#,#,#] and replace # with your map's bounds
    style: "blank",
    subscriptionKey,
    zoom: 19,
  });
```

## <a name="instantiate-the-indoor-manager"></a>实例化室内管理器

若要加载图块的室内图块集和定位样式，必须实例化室内管理器。 通过提供向定位对象和相应的 `tilesetId` 来实例化室内管理器。 如果希望支持[动态定位样式](indoor-map-dynamic-styling.md)，则必须传递 `statesetId`。 `statesetId` 变量名称区分大小写。 代码应类似于下面的 JavaScript。

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

若要启用提供的状态数据轮询，必须提供 `statesetId` 并调用 `indoorManager.setDynamicStyling(true)`。 通过轮询状态数据，可以动态更新动态属性的状态或状态。 例如，房间等功能可以有一个称为 `occupancy` 的动态属性（状态）。 应用程序可能希望轮询任何状态变更，以反映可视定位内的变化。 下面的代码演示如何启用状态轮询：

```javascript

const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}

```

## <a name="indoor-level-picker-control"></a>室内楼层选取器控件

 可使用“室内楼层选取器”控件更改呈现的定位的楼层。 可以根据需要通过“室内管理器”初始化“室内楼层选取器”控件。 下面是用于初始化楼层控制选取器的代码：

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>室内事件

 “Azure Maps 室内”模块支持定位对象事件。 当楼层或设施发生更改时，将调用定位对象事件侦听器。 如果要在楼层或设施发生更改时运行代码，请将代码置于事件侦听器中。 下面的代码演示如何将事件侦听器添加到定位对象。

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

    //code that you want to run after a level has been changed
    console.log("The level has changed: ", eventData);

});
map.events.add("facilitychanged", indoorManager, (eventData) => {

    //code that you want to run after a facility has been changed
    console.log("The facility has changed: ", eventData);
});
```

`eventData` 变量分别保存有关调用 `levelchanged` 或 `facilitychanged` 事件的楼层或设备的信息。 当楼层发生更改时，`eventData` 对象将包含 `facilityId`、新的 `levelNumber` 和其他元数据。 当设施发生更改时，`eventData` 对象将包含新的 `facilityId`、新的 `levelNumber` 和其他元数据。

## <a name="example-use-the-indoor-maps-module"></a>示例：使用室内定位模块

此示例演示如何在 Web 应用程序中使用“Azure Maps 室内”模块。 尽管该示例的范围受到限制，但它涵盖了开始使用“Azure Maps 室内”模块所需的基础知识。 完整的 HTML 代码如下所述。

1. 使用 Azure 内容分发网络[选项](#embed-the-indoor-maps-module)安装“Azure Maps 室内”模块。

2. 创建新的 HTML 文件

3. 在 HTML 标头中，引用“Azure Maps 室内”模块 JavaScript 和样式表样式。

4. 初始化定位对象。 定位对象支持以下选项：
    - `Subscription key` 是 Azure Maps 主订阅密钥。
    - `center` 定义室内定位中心位置的纬度和经度。 如果你不希望为 `bounds` 提供值，则为 `center` 提供值。 格式应显示为 `center`：[-122.13315, 47.63637]。
    - `bounds` 是包含图块集定位数据的最小矩形。 如果你不希望为 `center` 设置值，则为 `bounds` 设置值。 可以通过调用[图块集列表 API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview) 找到定位边界。 图块集列表 API 返回 `bbox`，可以对其进行分析并将其分配给 `bounds`。 格式应显示为 `bounds`：[#,#,#,#]。
    - `style` 允许你设置背景颜色。 若要显示白色背景，请将 `style` 定义为“空白”。
    - `zoom` 允许你指定定位的最小和最大缩放级别。

5. 接下来，创建“室内管理器”模块。 为 Azure Maps 室内分配 `tilesetId`，并选择性地添加 `statesetId`。

6. 实例化“室内楼层选取器”控件。

7. 添加定位对象事件侦听器。  

文件现在应类似于下面的 HTML。

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
       <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [ # , # , # , # ] and replace # with your Map bounds
          style: "blank",
          subscriptionKey,
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

若要查看室内定位，请将其加载到 Web 浏览器中。 它应如下图所示。 如果单击楼梯井功能，则“楼层选取器”将显示在右上角。

  ![室内定位图像](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>后续步骤

了解与“Azure Maps 室内”模块相关的 API：

> [!div class="nextstepaction"]
> [绘图包要求](drawing-requirements.md)

>[!div class="nextstepaction"]
> [室内定位 Creator](creator-indoor-maps.md)

详细了解如何将更多数据添加到定位：

> [!div class="nextstepaction"]
> [室内定位的动态样式](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)