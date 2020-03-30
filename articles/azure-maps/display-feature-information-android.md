---
title: 在 Azure 地图 Android SDK 中显示要素信息 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Android SDK 在地图上显示要素信息。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911687"
---
# <a name="display-feature-information"></a>显示功能信息

空间数据通常使用点、线和面表示。 此数据通常具有与其关联的元数据信息。 例如，点可能表示商店的位置，有关该餐厅的元数据可能是其名称、地址和所供应的食物类型。 此元数据可以使用 添加为这些功能的属性`JsonObject`。 以下代码创建一个简单的点要素，`title`其属性的值为"你好世界！

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

当用户与地图上的要素交互时，可以使用事件对这些操作做出反应。 常见方案是显示由用户与之交互的功能的元数据属性组成的消息。 该`OnFeatureClick`事件是用于检测用户何时在地图上点击要素的主要事件。 还有一个`OnLongFeatureClick`事件。 将`OnFeatureClick`事件添加到地图时，可以通过传入图层的 ID 来将其限制为单个图层。 如果未传入图层 ID，则点击地图上的任何要素（无论该要素位于哪个图层中）都将触发此事件。 以下代码创建一个符号图层以渲染地图上的点数据，然后添加事件`OnFeatureClick`并将其限制为此符号图层。

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>显示 Toast 消息

Toast 消息是向用户显示信息的最简单方法之一，可在 Android 的所有版本中使用。 它不支持任何类型的用户输入，并且只显示一小段时间。 如果要快速让用户知道他们点击的内容，Toast 消息可能是一个不错的选择。 以下代码显示 Toast 消息如何与`OnFeatureClick`事件一起使用。

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![正在点击的功能的动画和显示 Toast 消息](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

除了 Toast 消息之外，还有许多其他方法来显示要素的元数据属性，例如：

- [Snakbar 小部件](https://developer.android.com/training/snackbar/showing.html)- 小吃杆提供有关操作的轻量级反馈。 它们在移动显示屏上屏幕底部显示一条简短消息，在较大的设备上显示左下角。 小吃条显示在屏幕上所有其他元素之上，一次只能显示一个。
- [对话框](https://developer.android.com/guide/topics/ui/dialogs)- 对话框是一个小窗口，提示用户做出决定或输入其他信息。 对话框不会填充屏幕，通常用于要求用户在继续操作之前执行操作的模式事件。
- 向当前活动添加[片段](https://developer.android.com/guide/components/fragments)。
- 导航到其他活动或视图。

## <a name="next-steps"></a>后续步骤

要向地图添加更多数据，请进行：

> [!div class="nextstepaction"]
> [添加符号图层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [将形状添加到 Android 地图](how-to-add-shapes-to-android-map.md)
