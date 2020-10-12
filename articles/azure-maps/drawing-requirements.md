---
title: Azure Maps Creator 中的绘图包要求
description: 了解将设备设计文件转换为映射数据的绘图包要求
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 1f25aadf716b7768b6122a4fb165466aef7f8a16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90053386"
---
# <a name="drawing-package-requirements"></a>绘图包要求

您可以使用 [Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion)将上载的绘图包转换为映射数据。 本文介绍了适用于转换 API 的绘图包要求。 若要查看示例包，可以下载示例[绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

## <a name="prerequisites"></a>先决条件

此绘图包包含以 DWG 格式保存的绘图，这是 Autodesk 的 AutoCAD®软件的本机文件格式。

您可以选择任何 CAD 软件来生成绘图包中的绘图。  

[Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion)可以将绘图包转换为定位数据。 转换服务与 AutoCAD DWG 文件格式一起使用。 `AC1032` 是 DWG 文件的内部格式版本，因此最好选择 `AC1032` 内部 dwg 文件格式版本。  

## <a name="glossary-of-terms"></a>术语词汇表

为了便于参考，以下是一些重要的术语和定义，如您阅读本文中所述。

| 术语  | 定义 |
|:-------|:------------|
| 层 | AutoCAD DWG 图层。|
| 级别 | 位于固定高度的建筑物区域。 例如，建筑物的地板。 |
| Xref  |AutoCAD DWG 文件格式的文件 ( DWG) ，作为外部引用附加到主绘图。  |
| Feature | 将几何图形与其他元数据信息合并在一起的对象。 |
| 功能类 | 特征的常见蓝图。 例如，某个 *单元* 是一个功能类，而 *office* 是一项功能。 |

## <a name="drawing-package-structure"></a>绘图包结构

绘图包是包含以下文件的 .zip 存档：

* AutoCAD DWG 文件格式的 DWG 文件。
* 单个设施的 manifest.json 文件。

你可以在文件夹中以任何方式组织 DWG 文件，但清单文件必须位于文件夹的根目录中。 必须使用 .zip 扩展名将文件夹压缩到一个存档文件中。 以下各节详细说明了 DWG 文件、清单文件和这些文件的内容的要求。  

## <a name="dwg-files-requirements"></a>DWG 文件要求

设施的每个楼层都需要一个 DWG 文件。 楼层的数据必须包含在一个 DWG 文件中。 所有外部引用 (Xref) 都必须绑定到父绘图。 此外，每个 DWG 文件：

* 必须定义 Exterior 和 Unit 图层。 它可以选择定义以下可选层： _墙壁_、 _门板_、 _UnitLabel_、 _Zone_和 _ZoneLabel_。
* 不得包含来自多个楼层的特征。
* 不得包含来自多个设施的特征。

[Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion)可以从 DWG 文件中提取以下特征类：

* Levels
* 单位
* 区域
* Openings
* Walls
* 垂直 penetrations

所有转换作业都会生成至少所需的一组默认类别：room、structure.wall、opening.door、zone 和 facility。 其他类别用于对象引用的每个类别名称。  

DWG 图层必须包含一个类的特征。 类不得共用图层。 例如，units 和 walls 不得共用图层。

DWG 图层还必须遵循以下条件：

* 所有 DWG 文件的绘图源都必须保持纬度和经度相同。
* 每个楼层都必须与其他楼层的方向相同。
* 自相交多边形会自动修复，并且 [Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion) 会引发警告。 你应手动检查修复后的结果，因为它们可能与预期的结果不匹配。

所有层实体都必须是以下类型之一：直线、折线、多边形、圆弧、圆圈或文本 (单行) 。 任何其他实体类型都将被忽略。

下表概述了支持的实体类型和每个层支持的功能。 如果层包含不受支持的实体类型，则 [Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion) 将忽略这些实体。  

| 层 | 实体类型 | 功能 |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Polygon、PolyLine（闭合）、Circle | Levels
| [单位](#unit-layer) |  Polygon、PolyLine（闭合）、Circle | 垂直 penetrations，单位
| [Wall](#wall-layer)  | Polygon、PolyLine（闭合）、Circle | 不适用。 有关详细信息，请参阅 [Wall 图层](#wall-layer)。
| [Door](#door-layer) | Polygon、PolyLine、Line、CircularArc、Circle | Openings
| [区域](#zone-layer) | Polygon、PolyLine（闭合）、Circle | Zone
| [UnitLabel](#unitlabel-layer) | Text（单行） | 不适用。 此图层只能向 Units 图层中的 unit 特征添加属性。 有关详细信息，请参阅 [UnitLabel 图层](#unitlabel-layer)。
| [ZoneLabel](#zonelabel-layer) | Text（单行） | 不适用。 此图层只能向 Zones 图层中的 zone 特征添加属性。 有关详细信息，请参阅 [ZoneLabel 层](#zonelabel-layer)。

下面各部分详细介绍了每个图层的要求。

### <a name="exterior-layer"></a>Exterior 图层

每个楼层的 DWG 文件都必须包含用于定义相应楼层的外围的图层。 该层称为 *外部* 层。 例如，如果某个设施包含两个楼层，那么它需要有两个 DWG 文件，每个文件都有一个 Exterior 图层。

无论外部层有多少实体绘图， [生成的工具数据集](tutorial-creator-indoor-maps.md#create-a-feature-stateset) 在每个 DWG 文件中只包含一个级别功能。 此外：

* Exteriors 必须绘制为多边形、折线 (闭合) 或圆圈。
* Exteriors 可以重叠，但会被解析为一个几何。

如果该层包含多个重叠折线，则这些折线将被解析为单个级别特征。 或者，如果层包含多个非重叠折线，则生成的级别特征具有多多边形表示形式。

您可以在 [示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中看到外部层的一个示例。

### <a name="unit-layer"></a>Unit 图层

每个级别的 DWG 文件定义一个包含单位的层。 单元是建筑物中可导航的空间，如办公室、走廊、楼梯和电梯。 Unit 图层应遵循以下要求：

* 必须将单位绘制为多边形、折线 (闭合) 或圆圈。
* Unit 必须位于设施外围的边界内。
* Unit 不得部分重叠。
* Unit 不得包含任何自相交几何图形。

在 UnitLabel 层中创建一个文本对象，然后将该对象置于单元边界内，从而为该单元命名。 有关详细信息，请参阅 [UnitLabel 图层](#unitlabel-layer)。

可以在 [示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看 "单元" 层的示例。

### <a name="wall-layer"></a>Wall 图层

每个级别的 DWG 文件可以包含定义墙壁、柱形和其他建筑结构的物理范围的层。

* 必须将墙壁绘制为多边形、折线 (闭合) 或圆圈。
* 墙壁层只应包含解释为建筑结构的几何。

可以在 [示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看墙壁层的示例。

### <a name="door-layer"></a>Door 图层

可以包含包含门的 DWG 层。 每个门必须与单元的边缘重叠。

Azure Maps 数据集中的门开口表示为与多个单元边界重叠的单行段。 下图显示了如何在门层中转换几何以打开数据集中的功能。

![显示生成开口的步骤的四个图形](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zone 图层

每个级别的 DWG 文件可以包含定义区域物理区的区域层。 区域可以是室内的空地，也可以是后院。

* 区域必须绘制为多边形、折线 (闭合) 或圆圈。
* 区域可以重叠。
* 区域可以位于设施外部外围的内部或外部。

通过在 ZoneLabel 层中创建一个文本对象并将该文本对象置于区域边界内来命名区域。 有关详细信息，请参阅 [ZoneLabel 图层](#zonelabel-layer)。

可以在 [示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看区域层的示例。

### <a name="unitlabel-layer"></a>UnitLabel 图层

每个级别的 DWG 文件可以包含一个 UnitLabel 层。 UnitLabel 层将名称属性添加到从单元层提取的单位。 具有 name 属性的单元可以在清单文件中指定其他详细信息。

* UnitLabel 必须是单行文本实体。
* UnitLabel 必须位于单元的边界内。
* 单元不能包含 UnitLabel 层中的多个文本实体。

可以在 [示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看 UnitLabel 层的示例。

### <a name="zonelabel-layer"></a>ZoneLabel 图层

每个级别的 DWG 文件可以包含一个 ZoneLabel 层。 此图层向从 Zone 图层中提取的区域添加 name 属性。 具有 name 属性的区域可以在清单文件中指定其他详细信息。

* ZoneLabel 必须是单行文本实体。
* ZoneLabel 必须位于区域的边界内。
* 区域不能包含 ZoneLabel 层中的多个文本实体。

可以在 [示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看 ZoneLabel 层的示例。

## <a name="manifest-file-requirements"></a>清单文件要求

zip 文件夹必须在目录的根级别包含命名为“manifest.json”的清单文件。 它描述了 DWG 文件，以便 [Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion)能够分析其内容。 仅引入清单标识的文件。 位于 zip 文件夹中但未在清单中正确列出的文件将被忽略。

清单文件的对象中的文件路径 `buildingLevels` 必须是相对于 zip 文件夹的根的路径。 DWG 文件名必须与设施楼层的名称完全匹配。 例如，"上" 级别的 DWG 文件为 ""。 级别2的 DWG 文件被命名为 "level_2 dwg"。 如果楼层名称中有空格，请使用下划线字符。

尽管使用清单对象时有要求，但并不是所有的对象都是必需的。 下表显示 [Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion)版本1.1 的必需和可选对象。

| 对象 | 必选 | 说明 |
| :----- | :------- | :------- |
| `version` | true |清单架构版本。 目前仅支持版本1.1。|
| `directoryInfo` | true | 概述设施地理位置和联系人信息。 它还可用于概述居用者的地理位置和联系人信息。 |
| `buildingLevels` | true | 指定建筑物的楼层，以及包含各楼层的设计的文件。 |
| `georeference` | true | 包含设施绘图的数值地理信息。 |
| `dwgLayers` | true | 列出图层名称，每个图层都列出自己的特征的名称。 |
| `unitProperties` | false | 可用于插入 unit 特征的其他元数据。 |
| `zoneProperties` | false | 可用于插入 zone 特征的其他元数据。 |

下面各部分详细介绍了每个对象的要求。

### `directoryInfo`

| 属性  | 类型 | 必须 | 说明 |
|-----------|------|----------|-------------|
| `name`      | 字符串 | true   |  建筑物名称。 |
| `streetAddress`|    字符串 |    false    | 建筑物地址。 |
|`unit`     | 字符串    |  false    |  建筑物中的单元。 |
| `locality` |    字符串 |    false |    区域、居住区或地区的名称。 例如，“Overlake”或“Central District”。 locality 不是通讯地址的一部分。 |
| `adminDivisions` |    字符串的 JSON 数组 |    false     | 包含地址标记的数组 (Country, State, City) 或 (Country, Prefecture, City, Town)。 使用 ISO 3166 国家/地区代码和 ISO 3166-2 州/省/区域代码。 |
| `postalCode` |    字符串    | false    | 邮件分拣代码。 |
| `hoursOfOperation` |    字符串 |     false | 遵循 [OSM 开放时间](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification)格式。 |
| `phone`    | 字符串 |    false |    与建筑物关联的电话号码。 必须包含国家/地区代码。 |
| `website`    | 字符串 |    false    | 与建筑物关联的网站。 必须以 http 或 https 开头。 |
| `nonPublic` |    bool    | false | 指定建筑物是否向公众开放的标志。 |
| `anchorLatitude` | numeric |    false | 设施定位点（图钉）的纬度。 |
| `anchorLongitude` | numeric |    false | 设施定位点（图钉）的经度。 |
| `anchorHeightAboveSeaLevel`  | numeric | false | 设施底层的海拔高度（以米为单位）。 |
| `defaultLevelVerticalExtent` | numeric | false | 在楼层的 `verticalExtent` 未定义时使用的、此设施的楼层的默认高度（厚度）。 |

### `buildingLevels`

`buildingLevels` 对象包含建筑物楼层的 JSON 数组。

| properties  | 类型 | 必须 | 说明 |
|-----------|------|----------|-------------|
|`levelName`    |字符串    |true |    楼层的描述性名称。 例如：第1层、会议厅、蓝色停车场或一起。|
|`ordinal` | integer |    true | 确定级别的垂直顺序。 每个设施都必须有序号为 0 的楼层。 |
|`heightAboveFacilityAnchor` | numeric | false |    定位点上方的高度（以米为单位）。 |
| `verticalExtent` | numeric | false | 以米为单位 (粗细) 的地面到天花板高度。 |
|`filename` |    字符串 |    true |    建筑物楼层的 CAD 绘图的文件系统路径。 它必须相对于建筑物的 zip 文件的根。 |

### `georeference`

| 属性  | 类型 | 必须 | 说明 |
|-----------|------|----------|-------------|
|`lat`    | numeric |    true |    设施绘图的原点的纬度（用十进制表示）。 坐标原点必须位于 WGS84 Web Mercator (`EPSG:3857`)。|
|`lon`    |numeric|    true|    设施绘图的原点的经度（用十进制表示）。 坐标原点必须位于 WGS84 Web Mercator (`EPSG:3857`)。 |
|`angle`|    numeric|    true|   True 与绘图的垂直 (Y) 轴之间的顺时针角度（以度为单位）。   |

### `dwgLayers`

| 属性  | 类型 | 必须 | 说明 |
|-----------|------|----------|-------------|
|`exterior`    |字符串数组|    true|    定义外部生成配置文件的层的名称。|
|`unit`|    字符串数组|    true|    定义单位的层的名称。|
|`wall`|    字符串数组    |false|    定义墙壁的层的名称。|
|`door`    |字符串数组|    false   | 定义门的层的名称。|
|`unitLabel`    |字符串数组|    false    |定义单元名称的层的名称。|
|`zone` | 字符串数组    | false    | 定义区域的层的名称。|
|`zoneLabel` | 字符串数组 |     false |    定义区域名称的层的名称。|

### `unitProperties`

`unitProperties` 对象包含 unit 属性的 JSON 数组。

| properties  | 类型 | 必须 | 说明 |
|-----------|------|----------|-------------|
|`unitName`    |字符串    |true    |要与此 `unitProperty` 记录关联的单元的名称。 仅当在层中找到标签匹配时，此记录才有效 `unitName` `unitLabel` 。 |
|`categoryName`|    字符串|    false    |类别名称。 有关完整的类别列表，请参阅[类别](https://aka.ms/pa-indoor-spacecategories)。 |
|`navigableBy`| 字符串数组 |    false    |指明可遍历单元的导航代理的类型。 此属性告知 wayfinding 功能。 允许的值为： `pedestrian` 、 `wheelchair` 、 `machine` 、 `bicycle` 、 `automobile` 、 `hiredAuto` 、 `bus` `railcar` `emergency` `ferry` `boat` 、、、、和 `disallowed` 。|
|`routeThroughBehavior`|    字符串|    false    |单元的穿过行为。 允许的值为 `disallowed`、`allowed` 和 `preferred`。 默认值是 `allowed`。|
|`occupants`    |directoryInfo 对象的数组 |false    |单元的居用者列表。 |
|`nameAlt`|    字符串|    false|    单元的备用名称。 |
|`nameSubtitle`|    字符串    |false|    单元的副标题。 |
|`addressRoomNumber`|    字符串|    false|    设备的房间、单位、单元或套件编号。|
|`verticalPenetrationCategory`|    字符串|    false| 如果定义了此属性，则生成的功能是 VRT) （而不是单元）的垂直渗透 (。 你可以使用 Vrt 来跳到它上面或下面的级别中的其他 VRT 功能。 垂直穿透是 [类别](https://aka.ms/pa-indoor-spacecategories) 名称。 如果定义了此属性，则 `categoryName` 属性会被重写 `verticalPenetrationCategory` 。 |
|`verticalPenetrationDirection`|    字符串|    false    |如果定义了 `verticalPenetrationCategory`，则可以视需要选择定义有效的行进方向。 允许的值为： `lowToHigh` 、 `highToLow` 、 `both` 和 `closed` 。 默认值是 `both`。|
| `nonPublic` | bool | false | 指明单元是否向公众开放。 |
| `isRoutable` | bool | false | 如果将此属性设置为 `false` ，则不能通过单元进行切换。 默认值是 `true`。 |
| `isOpenArea` | bool | false | 允许导航代理输入单位，而无需连接到设备。 默认情况下，此值设置为 `true` "对于没有开口的单位"，并将设置为 " `false` 带有开口的单位"。 如果 `isOpenArea` `false` 在没有打开的单元上手动设置为，则会出现警告。 这是因为导航代理无法访问结果单元。|

### `zoneProperties`

`zoneProperties` 对象包含 zone 属性的 JSON 数组。

| properties  | 类型 | 必须 | 说明 |
|-----------|------|----------|-------------|
|zoneName        |字符串    |true    |要与 `zoneProperty` 记录关联的区域的名称。 只有当在区域的 `zoneLabel` 图层中找到与 `zoneName` 匹配的标签时，此记录才有效。  |
|categoryName|    字符串|    false    |类别名称。 有关完整的类别列表，请参阅[类别](https://aka.ms/pa-indoor-spacecategories)。 |
|zoneNameAlt|    字符串|    false    |区域的备用名称。  |
|zoneNameSubtitle|    字符串 |    false    |区域的副标题。 |
|zoneSetId|    字符串 |    false    | 设置 ID 以在多个区域之间建立关系，以便可以将它们作为一个组来查询或选择。 例如，跨越多个级别的区域。 |

### <a name="sample-drawing-package-manifest"></a>示例绘图包清单

下面是示例绘图包的示例清单文件。 若要下载整个包，请参阅 [示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

#### <a name="manifest-file"></a>清单文件

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

当你的绘图包满足要求时，你可以使用 [Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion) 将包转换为地图数据集。 然后，可以使用该数据集，通过使用室内地图模块来生成室内地图。

> [!div class="nextstepaction"]
>[适用于室内定位的 Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [教程：创建 Creator 室内定位](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [室内地图动态样式](indoor-map-dynamic-styling.md)
