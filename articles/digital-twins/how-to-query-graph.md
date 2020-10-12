---
title: 查询孪生图
titleSuffix: Azure Digital Twins
description: 有关信息，请参阅如何查询 Azure 数字孪生克隆图。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 72658a97f89b14529e8ccb3639cb1b78f1b92316
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91848801"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>查询 Azure 数字孪生克隆图形

本文提供了有关使用 [Azure 数字孪生查询语言](concepts-query-language.md) 查询克隆 [图形](concepts-twins-graph.md) 以获取信息的示例和更多详细信息。 使用 Azure 数字孪生 [**查询 api**](how-to-use-apis-sdks.md)在图形上运行查询。

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

本文的其余部分提供了有关如何使用这些操作的示例。

## <a name="query-syntax"></a>查询语法

本部分包含的示例查询阐释了查询语言结构并在 [数字孪生](concepts-twins-graph.md)上执行可能的查询操作。

### <a name="show-all-existing-digital-twins"></a>显示所有现有数字孪生

下面是一个基本查询，它将返回实例中所有数字孪生的列表：

```sql
SELECT *
FROM DIGITALTWINS
```

### <a name="select-top-items"></a>选择顶级项

您可以使用子句在查询中选择多个 "top" 项 `Select TOP` 。

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="query-by-property"></a>按属性查询

获取数字孪生按 **属性** (包括 ID 和元数据) ：
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> 使用元数据字段查询数字克隆的 ID `$dtId` 。

还可以基于 **是否定义了某个属性**来获取孪生。 下面是一个查询，用于获取具有定义的 *Location* 属性的孪生：

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

这可以帮助你按 *标记* 属性获取孪生，如 [向数字孪生添加标记](how-to-use-tags.md)中所述。 下面是一个查询，用于获取用 *red*标记的所有孪生：

```sql
select * from digitaltwins where is_defined(tags.red) 
```

还可以基于 **属性的类型**来获取孪生。 下面是一个可获取孪生的查询，其 *温度* 属性为数字：

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>按模型查询

`IS_OF_MODEL`运算符可用于基于克隆的[**模型**](concepts-models.md)进行筛选。 它支持继承，并且具有多个重载选项。

最简单的用法 `IS_OF_MODEL` 仅采用 `twinTypeName` 参数： `IS_OF_MODEL(twinTypeName)` 。
下面是传递此参数中的值的查询示例：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

若要在有多个 (时指定要搜索的非克隆集合（如 `JOIN`) 使用时一样），请添加 `twinCollection` 参数： `IS_OF_MODEL(twinCollection, twinTypeName)` 。
下面是添加此参数值的查询示例：

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

若要执行完全匹配，请添加 `exact` 参数： `IS_OF_MODEL(twinTypeName, exact)` 。
下面是添加此参数值的查询示例：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

你还可以将所有这三个参数同时传递： `IS_OF_MODEL(twinCollection, twinTypeName, exact)` 。
下面是一个用于为所有三个参数指定值的查询示例：

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>基于关系的查询

当基于数字孪生 **关系**进行查询时，Azure 数字孪生查询语言具有特殊的语法。

关系将被提取到子句中的查询范围内 `FROM` 。 "经典" SQL 类型语言中的一个重要区别在于，此子句中的每个表达式 `FROM` 不是一个表; 相反， `FROM` 子句表示跨实体关系遍历，并使用 Azure 数字孪生版本编写 `JOIN` 。 

回忆一下，通过 Azure 数字孪生 [模型](concepts-models.md) 功能，关系不能独立于孪生存在。 这意味着，Azure 数字孪生查询语言与 `JOIN` 常规 SQL 略有不同 `JOIN` ，因为此处的关系不能单独查询，必须绑定到一个完全不同的位置。
为了引入这种差异， `RELATED` 子句中使用关键字 `JOIN` 来引用克隆的一组关系。 

以下部分提供了此类外观的几个示例。

> [!TIP]
> 从概念上讲，这项功能模拟了 CosmosDB 的以文档为中心的功能， `JOIN` 可以在文档中的子对象上执行。 CosmosDB 使用 `IN` 关键字指示用于 `JOIN` 循环访问当前上下文文档内的数组元素。

#### <a name="relationship-based-query-examples"></a>基于关系的查询示例

若要获取包含关系的数据集，请使用单个 `FROM` 语句，后跟 N `JOIN` 个语句，其中的 `JOIN` 语句表示上一个或语句的结果上的关系 `FROM` `JOIN` 。

下面是一个基于关系的示例查询。 此代码段选择 *ID* 属性为 "ABC" 的所有数字孪生，并通过 *包含* 关系与这些数字孪生相关的所有数字孪生。 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> 开发人员无需将此 `JOIN` 与子句中的键值相关联 `WHERE` (或使用定义) 以内联方式指定键值 `JOIN` 。 此关联由系统自动计算，因为关系属性本身标识目标实体。

#### <a name="query-the-properties-of-a-relationship"></a>查询关系的属性

类似于通过 DTDL 描述的数字孪生的方式，关系也可以具有属性。 您可以基于孪生的 **关系的属性**来查询。
使用 Azure 数字孪生查询语言，可以通过将别名分配给子句内的关系，来筛选和投影关系 `JOIN` 。 

例如，假设有一个具有*reportedCondition*属性的*servicedBy*关系。 在下面的查询中，为此关系提供了一个 "R" 别名，以便引用其属性。

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

在上面的示例中，请注意， *reportedCondition* 是 *servicedBy* 关系本身的属性， (不属于具有 *servicedBy* 关系) 的某些数字克隆。

### <a name="query-with-multiple-joins"></a>具有多个联接的查询

目前处于预览状态，一个查询最多支持五个 `JOIN` 。 这使您可以一次遍历多个级别的关系。

下面是多联接查询的一个示例，它获取房间1和2中的灯具面板中包含的所有光源电灯泡。

```sql
SELECT LightBulb 
FROM DIGITALTWINS Room 
JOIN LightPanel RELATED Room.contains 
JOIN LightBulb RELATED LightPanel.contains 
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1') 
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1') 
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="other-compound-query-examples"></a>其他复合查询示例

您可以使用组合运算符 **组合** 以上任意类型的查询，以便在单个查询中包含更多详细信息。 下面是一些其他查询多个类型为一次的克隆说明符的复合查询示例。

| 说明 | 查询 |
| --- | --- |
| 在 *房间 123* 具有的设备中，返回服务于操作员角色的 MxChip 设备 | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| 获取具有名为*id1*的关系*的孪生* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| 获取*floor11*包含的此聊天室模型的所有聊天室 | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="reference-expressions-and-conditions"></a>参考：表达式和条件

本部分包含有关编写 Azure 数字孪生查询时可用的运算符和函数的参考。

### <a name="operators"></a>运算符

支持以下运算符：

| 系列 | 运算符 |
| --- | --- |
| 逻辑 |AND、OR、NOT |
| 比较 |=、！ =、<、>、<=、>= |
| 包含 | 在中，N |

### <a name="functions"></a>函数

支持以下类型检查和强制转换函数：

| 函数 | 说明 |
| -------- | ----------- |
| IS_DEFINED | 返回一个布尔，它指示属性是否已经分配了值。 仅当该值为基元类型时才支持此功能。 基元类型包括字符串、布尔值、数字或 `null`。 不支持日期/时间、对象类型和数组。 |
| IS_OF_MODEL | 返回一个布尔值，该值指示指定的上值是否与指定的模型类型匹配 |
| IS_BOOL | 返回一个布尔值，指示指定表达式的类型是否为布尔表达式。 |
| IS_NUMBER | 返回一个布尔值，指示指定表达式的类型是否为数字。 |
| IS_STRING | 返回一个布尔值，指示指定表达式的类型是否为字符串。 |
| IS_NULL | 返回一个布尔值，指示指定表达式的类型是否为 null。 |
| IS_PRIMITIVE | 返回一个布尔值，指示指定表达式的类型是否为基元（字符串、布尔值、数字或 `null`）。 |
| IS_OBJECT | 返回一个布尔值，指示指定表达式的类型是否为 JSON 对象。 |

支持以下字符串函数：

| 函数 | 说明 |
| -------- | ----------- |
| STARTSWITH (x、y)  | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。 |
| ENDSWITH (x、y)  | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。 |

## <a name="run-queries-with-an-api-call"></a>使用 API 调用运行查询

一旦您决定了查询字符串，就可以通过调用 **查询 API**来执行它。
下面的代码段演示了从客户端应用程序进行的此调用：

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

此调用以 QueryResult 对象的形式返回查询结果。 

查询调用支持分页。 下面是一个完整的示例，其中包含错误处理和分页：

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>查询限制

在查询中反映实例的更改之前，可能会有最多10秒的延迟。 例如，如果使用 DigitalTwins API 完成了创建或删除孪生等操作，则结果可能不会立即反映在查询 API 请求中。 等待一小段时间应该足以解决问题。

在预览期间，使用还有其他限制 `JOIN` 。
* 语句内不支持子查询 `FROM` 。
* `OUTER JOIN` 不支持语义，这意味着，如果关系的排名为零，则将从输出结果集中消除整个 "行"。
* 预览期间，图形遍历深度限制为每个查询的五个 `JOIN` 级别。
* 操作的源 `JOIN` 受到限制：查询必须声明查询开始处的孪生。

## <a name="query-best-practices"></a>查询最佳做法

下面是用于通过 Azure 数字孪生进行查询的一些技巧。

* 在模型设计阶段，请考虑查询模式。 尝试确保在单个查询中需要回答的关系作为单级关系建模。
* 设计属性的方式可避免图形遍历中的大型结果集。
* 您可以通过生成孪生数组并使用运算符查询来大幅减少所需的查询数 `IN` 。 例如，假设 *建筑物* 包含 *楼层* ， *地面* 包含 *房间*。 若要在生成中搜索热点，可以执行以下操作：

    1. 基于关系查找建筑物中的地面 `contains`
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. 若要查找房间，而不是逐个考虑并运行 `JOIN` 查询来查找每个房间的房间，可以在下面的查询中使用名为 *Floor* 的建筑物 (中的楼层集合进行查询) 。

        在客户端应用中：
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        在查询中：
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* 属性名称和值区分大小写，因此请注意使用模型中定义的确切名称。 如果属性名称拼写错误或大小写不正确，则结果集为空，并且不返回任何错误。

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 数字孪生 api 和 sdk](how-to-use-apis-sdks.md)的详细信息，包括用于运行本文中查询的查询 API。
