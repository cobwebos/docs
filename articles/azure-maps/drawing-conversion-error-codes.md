---
title: Azure Maps 绘图转换错误和警告
description: 了解在使用 Azure Maps 转换服务时可能会看到的转换错误和警告。 阅读关于如何解决这些错误和警告的建议，以及一些示例。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: d79c42f3bdf84efcdf2187741ac270087be05272
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682001"
---
# <a name="drawing-conversion-errors-and-warnings"></a>绘图转换错误和警告

使用 [Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion)，可以将上传的绘图包转换为定位数据。 绘图包必须符合[绘图包要求](drawing-requirements.md)。 如果不符合一个或多个要求，转换服务就会返回错误或警告。 本文列出了转换错误和警告代码，以及关于如何解决这些问题的建议。 此外，还提供了一些可能会导致转换服务返回这些代码的绘图示例。

如果有任何转换警告，转换服务也会成功。 尽管如此，仍建议你审阅并解决所有警告。 警告表示转换的一部分被忽略或自动修复。 如果不解决警告，可能会导致后续进程出错。

## <a name="general-warnings"></a>常规警告

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>GeometryWarning 的说明

当绘图中包含无效实体时，服务会返回 geometryWarning。 无效实体是指不符合几何约束的实体。 例如，自相交多边形或只支持闭合几何图形的图层中的非闭合折线都是无效实体。

转换服务无法通过无效实体创建定位特征，而是会忽略它。

#### <a name="examples-for-geometrywarning"></a>geometryWarning 的示例

* 下面的两幅图展示了自相交多边形的示例。

     ![自相交多边形的示例](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![自相交多边形的示例](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* 下图展示了非闭合折线。 假设图层只支持闭合几何图形。

    ![非闭合折线的示例](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>如何修复 geometryWarning

请检查每个实体的 geometryWarning，以验证它是否遵循几何约束。

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>unexpectedGeometryInLayer 的说明

当绘图中包含的几何图形不是给定图层的预期几何图形类型时，服务会返回 unexpectedGeometryInLayer 警告。 如果返回 unexpectedGeometryInLayer 警告，转换服务会忽略相应几何图形。

#### <a name="example-for-unexpectedgeometryinlayer"></a>unexpectedGeometryInLayer 的示例

下图展示了非闭合折线。 假设图层只支持闭合几何图形。

![非闭合折线的示例](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>如何修复 unexpectedGeometryInLayer

请检查每个 unexpectedGeometryInLayer 警告，并将不符合预期的几何图形移到应包含相应类型几何图形的图层中。 如果其他任何图层都不包含此类型的几何图形，应将它删除。

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>unsupportedFeatureRepresentation 的说明

当绘图中包含不受支持的实体类型时，服务会返回 unsupportedFeatureRepresentation 警告。

#### <a name="example-for-unsupportedfeaturerepresentation"></a>unsupportedFeatureRepresentation 的示例

下图展示了不受支持的实体类型，即标签层上的多行文本对象。
  
![标签层上的多行文本对象的示例](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>如何修复 unsupportedFeatureRepresentation

请确保 DWG 文件只包含受支持的实体类型。 [“绘图包要求”一文的“绘图文件要求”部分](drawing-requirements.md#drawing-package-requirements)下列出了受支持的类型。

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>automaticRepairPerformed 的说明

当转换服务自动修复无效的几何图形时，服务会返回 automaticRepairPerformed 警告。

#### <a name="examples-for-automaticrepairperformed"></a>automaticRepairPerformed 的示例

* 下图展示了转换服务如何将自相交多边形修复为有效的几何图形。

    ![已修复自相交多边形的示例](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* 下图展示了转换服务如何通过贴靠非闭合折线的第一个和最后一个顶点来创建闭合折线（其中第一个和最后一个顶点相距不到 1 mm）。  

    ![已贴靠折线的示例](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* 下图展示了转换服务如何在只支持闭合折线的图层中修复了多个非闭合折线。 为了避免放弃非闭合折线，转换服务将它们合并成一条闭合折线。

    ![合并成一条闭合折线的非闭合折线的示例](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>如何修复 automaticRepairPerformed

若要修复 automaticRepairPerformed 警告，请执行以下操作：

1. 检查每个警告的几何图形和特定的警告文本。
2. 确定自动修复是否正确。
3. 如果修复正确，请继续操作。 否则，请转到设计文件，并手动解决警告。

>[!TIP]
>为了阻止将来出现警告，请将原始绘图更改为与修复后的绘图一致。

## <a name="manifest-warnings"></a>清单警告

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>redundantAttribution 的说明

当清单包含多余或冲突的对象属性时，服务会返回 redundantAttribution 警告。

#### <a name="examples-for-redundantattribution"></a>redundantAttribution 的示例

* 在下面的 JSON 代码片段中，两个或多个 `unitProperties` 对象具有相同的 `name`。

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* 在下面的 JSON 代码片段中，两个或多个 `zoneProperties` 对象具有相同的 `name`。

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>如何修复 redundantAttribution

若要修复 redundantAttribution* 警告，请删除多余或冲突的对象属性。

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>manifestWarning 的说明

当清单包含在转换期间未用的 unitProperties 或 zoneProperties 对象时，服务会返回 manifestWarning。

#### <a name="examples-for-manifestwarning"></a>manifestWarning 的示例

* 清单中的一个 `unitProperties` 对象具有在 `unitLabel` 图层中没有匹配标签的 `unitName`。

* 清单中的一个 `zoneProperties` 对象具有在 `zoneLabel` 图层中没有匹配标签的 `zoneName`。

#### <a name="how-to-fix-manifestwarning"></a>如何修复 manifestWarning

若要修复 manifestWarning，请从清单中删除未用的 `unitProperties` 或 `zoneProperties` 对象，或向绘图添加单元/区域标签，以便在转换期间使用属性对象。

## <a name="wall-warnings"></a>墙警告

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>wallOutsideLevel 的说明

当绘图中包含位于楼层轮廓边界外的墙几何图形时，服务会返回 wallOutsideLevel 警告。

#### <a name="example-for-walloutsidelevel"></a>wallOutsideLevel 的示例

* 下图展示了位于黄色楼层边界外的红色内墙。

    ![位于楼层边界外的内墙的示例](./media/drawing-conversion-error-codes/wall-outside-level.png)

* 下图展示了位于黄色楼层边界外的红色外墙。

    ![位于楼层边界外的外墙的示例](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>如何修复 wallOutsideLevel

若要修复 wallOutsideLevel 警告，请扩展楼层几何图形，以将所有墙都包含在内。 也可以将墙边界修改为位于楼层边界内。

## <a name="unit-warnings"></a>单元警告

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>unitOutsideLevel 的说明

当绘图中包含位于楼层轮廓边界外的单元几何图形时，服务会返回 unitOutsideLevel 警告。

#### <a name="example-for-unitoutsidelevel"></a>unitOutsideLevel 的示例

 在下图中，红色的单元几何图形超出了黄色的楼层边界。

 ![超出了楼层边界的单元的示例](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>如何修复 unitOutsideLevel

若要修复 unitOutsideLevel 警告，请扩展楼层边界，以将所有单元都包含在内。 也可以将单元几何图形修改为位于楼层边界内。

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>partiallyOverlappingUnit 的说明

当绘图中包含一个单元几何图形与另一个单元几何图形部分重叠时，服务会返回 partiallyOverlappingUnit 警告。 转换服务会放弃重叠的单元。

#### <a name="example-scenarios-partiallyoverlappingunit"></a>partiallyOverlappingUnit 的示例方案

在下图中，重叠单元以红色突出显示。 `UNIT110` 和 `HALLWAY` 已被放弃。

![重叠单元的示例](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>如何修复 partiallyOverlappingUnit

若要修复 partiallyOverlappingUnit 警告，请将每个部分重叠的单元重绘为不与其他任何单元重叠。

## <a name="door-warnings"></a>门警告

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>doorOutsideLevel 的说明

当绘图中包含位于楼层几何图形边界外的门几何图形时，服务会返回 doorOutsideLevel 警告。

#### <a name="example-for-dooroutsidelevel"></a>doorOutsideLevel 的示例

在下图中，以红色突出显示的门几何图形与黄色的楼层边界重叠。

![与楼层边界重叠的门的示例](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>如何修复 doorOutsideLevel

若要修复 doorOutsideLevel 警告，请将门几何图形重绘为位于楼层边界内。

## <a name="zone-warnings"></a>区域警告

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>zoneWarning 的说明

当区域不包含标签时，服务会返回 zoneWarning。 转换服务会放弃未标记的区域。

#### <a name="example-for-zonewarning"></a>zoneWarning 的示例

下图展示了不包含标签的区域。

![不包含标签的区域的示例](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>如何修复 zoneWarning

若要修复 zoneWarning，请验证每个区域是否都有一个标签。

## <a name="label-warnings"></a>标签警告

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>labelWarning 的说明

当绘图中包含不明确或矛盾的标签特征时，服务会返回 labelWarning。

导致 labelWarning 返回的原因有以下一种或多种：

* 单元标签不在任何单元内。
* 区域标签不在任何区域内。
* 区域标签位于两个或多个区域内。

#### <a name="example-for-labelwarning"></a>labelWarning 的示例

下图展示了位于两个区域内的标签。

![位于两个区域内的标签的示例 ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>如何修复 labelWarning

若要修复 labelWarning，请确保：

* 所有单元标签都位于单元内。
* 所有区域标签都位于区域内。
* 所有区域标签都位于一个且只有一个区域内。

## <a name="drawing-package-errors"></a>绘图包错误

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>invalidArchiveFormat 的说明

当绘图包采用无效的存档格式（如 GZIP 或 7-Zip）时，服务会返回 invalidArchiveFormat 错误。 只支持 ZIP 存档格式。

如果 ZIP 存档为空，也会发生 invalidArchiveFormat 错误。

#### <a name="how-to-fix-invalidarchiveformat"></a>如何修复 invalidArchiveFormat

若要修复 invalidArchiveFormat 错误，请验证：

* 存档文件名是否以 .zip 结尾。
* ZIP 存档是否包含数据。
* ZIP 存档能否打开。

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>invalidUserData 的说明

当转换服务无法从存储中读取用户数据对象时，服务会返回 invalidUserData 错误。

#### <a name="example-scenario-for-invaliduserdata"></a>InvalidUserData 的示例方案

你尝试使用不正确的 `udid` 参数来上传绘图包。

#### <a name="how-to-fix-invaliduserdata"></a>如何修复 invalidUserData

若要修复 invalidUserData 错误，请验证：

* 是否为已上传包提供了正确的 `udid`。
* 是否为用于上传绘图包的 Azure Maps 帐户启用了 Azure Maps Creator。
* 对转换服务的 API 请求是否包含用于上传绘图包的 Azure Maps 帐户的订阅密钥。

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>dwgError 的说明

当绘图包在已上传的 ZIP 存档中有一个或多个存在问题的 DWG 文件时，服务会返回 dwgError。

当绘图包中包含因为无效或损坏而无法打开的 DWG 文件时，服务会返回 dwgError。

* DWG 文件不是有效的 AutoCAD DWG 文件格式绘图。
* DWG 文件已损坏。
* DWG 文件在 manifest.json 文件中列出，但在 ZIP 存档中缺失。

#### <a name="how-to-fix-dwgerror"></a>如何修复 dwgError

若要修复 dwgError，请检查 manifest.json 文件来确认：

* ZIP 存档中的所有 DWG 文件是否都是有效的 AutoCAD DWG 格式绘图，并在 AutoCAD 中打开每个文件。 请删除或修复所有无效的绘图。
* manifest.json 中列出的 DWG 文件是否与 ZIP 存档中的 DWG 文件一致。

## <a name="manifest-errors"></a>清单错误

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>invalidJsonFormat 的说明

当 manifest.json 文件无法读取时，服务会返回 invalidJsonFormat 错误。

manifest.json 文件因 JSON 格式或语法错误而无法读取。 若要详细了解 JSON 格式和语法，请参阅 [JavaScript 对象表示法 (JSON) 数据交换格式](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>如何修复 invalidJsonFormat

若要修复 invalidJsonFormat 错误，请使用 JSON Linter 来检测和解决所有 JSON 错误。

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>missingRequiredField 的说明

当 manifest.json 文件缺少必需数据时，服务会返回 missingRequiredField 错误。

#### <a name="how-to-fix-missingrequiredfield"></a>如何修复 missingRequiredField

若要修复 missingRequiredField 错误，请验证清单中是否包含所有必需属性。 有关必需清单对象的完整列表，请参阅[“绘图包要求”中的“清单文件要求”部分](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>missingManifest 的说明

当 manifest.json 文件在 ZIP 存档中缺失时，服务会返回 missingManifest 错误。

导致 missingManifest 错误返回的原因有以下一种或多种：

* manifest.json 文件有拼写错误。
* manifest.json 文件缺失。
* manifest.json 没有位于 ZIP 存档的根目录内。

#### <a name="how-to-fix-missingmanifest"></a>如何修复 missingManifest

若要修复 missingManifest 错误，请确认 ZIP 存档的根级别是否有名为 manifest.json 的文件。

### <a name="conflict"></a>**conflict**

#### <a name="description-for-conflict"></a>conflict 的说明

当 manifest.json 文件包含冲突的信息时，服务会返回 conflict 错误。

#### <a name="example-scenario-for-conflict"></a>conflict 的示例方案

当多个级别被定义为具有相同的级别序号时，转换服务会返回 conflict 错误。 下面的 JSON 代码片段展示了两个级别被定义为具有相同的序号。

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>如何修复 conflict

若要修复 conflict 错误，请检查 manifest.json 文件，并删除任何冲突的信息。

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>invalidGeoreference 的说明

当 manifest.json 文件中包含无效的地理参考时，服务会返回 invalidGeoreference 错误。

导致 invalidGeoreference 错误返回的原因有以下一种或多种：

* 用户正在对超出范围的纬度或经度值生成地理参考。
* 用户正在对超出范围的旋转值生成地理参考。

#### <a name="example-scenario-for-invalidgeoreference"></a>invalidGeoreference 的示例方案

在下面的 JSON 代码片段中，纬度超出上限。

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>如何修复 invalidGeoreference

若要修复 invalidGeoreference 错误，请验证地理参考值是否在范围内。

>[!IMPORTANT]
>在 GeoJSON 中，坐标顺序为经度和纬度。 如果没有使用正确的顺序，可能会意外地参考超出范围的纬度或经度值。

## <a name="wall-errors"></a>墙错误

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>wallError 的说明

如果绘图在你尝试创建墙特征时出错，服务会返回 wallError。

#### <a name="example-scenario-for-wallerror"></a>wallError 的示例方案

下图展示了未与任何单元重叠的墙特征。

![未与任何单元重叠的墙特征的示例](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>如何修复 wallError

若要修复 wallError 错误，请将墙重绘为与至少一个单元重叠。 也可以新建与墙重叠的单元。

## <a name="vertical-penetration-errors"></a>垂直渗透错误

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>VerticalPenetrationError 的说明

当绘图中包含不明确的垂直渗透特征时，服务会返回 verticalPenetrationError。

导致 verticalPenetrationError 返回的原因有以下一种或多种：

* 绘图中包含垂直渗透区域，但在其上下的任何楼层上都没有重叠的垂直渗透区域。
* 绘图包中的一个楼层上有两个或多个垂直渗透特征，它们与其正上方或正下方的另一个楼层上的同一个垂直渗透特征重叠。

#### <a name="example-scenario-for-verticalpenetrationerror"></a>verticalPenetrationError 的示例方案

下图展示了一个垂直渗透区域在其上下的任何楼层上都没有重叠的垂直渗透区域。

![垂直渗透的示例 1](./media/drawing-conversion-error-codes/vrt-2.png)

下图展示了一个垂直渗透区域与相邻楼层上的多个垂直渗透区域重叠。

![垂直渗透的示例 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>如何修复 verticalPenetrationError

若要修复 verticalPenetrationError 错误，请阅读[绘图包要求](drawing-requirements.md)一文中有关如何使用垂直渗透特征的内容。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用 Azure Maps 绘图错误可视化工具](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [适用于室内定位的 Creator](creator-indoor-maps.md)