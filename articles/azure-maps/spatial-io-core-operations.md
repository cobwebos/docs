---
title: 核心 IO 操作 |微软 Azure 地图
description: 了解如何使用空间 IO 模块中的核心库高效地读取和写入 XML 和分隔数据。
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371444"
---
# <a name="core-io-operations"></a>核心 IO 操作

除了提供读取空间数据文件的工具外，空间 IO 模块还公开核心基础库以快速高效地读取和写入 XML 和分隔数据。

命名`atlas.io.core`空间包含两个低级类，可以快速读取和写入 CSV 和 XML 数据。 这些基类为空间 IO 模块中的空间数据读取器和编写器提供动力。 随意使用它们为 CSV 或 XML 文件添加额外的读取和写入支持。
 
## <a name="read-delimited-files"></a>读取已分隔的文件

类`atlas.io.core.CsvReader`读取包含分隔数据集的字符串。 此类提供两种读取数据的方法：

- 该`read`函数将读取完整数据集，并返回表示分隔数据集的所有单元格的二维字符串数组。
- 函数`getNextRow`读取分隔数据集中的每行文本，并返回表示该数据集中所有单元格的字符串数组。 在处理下一行之前，用户可以处理该行并释放该行中不需要的任何内存。 因此，函数更具有内存效率。

默认情况下，读取器将使用逗号字符作为分隔符。 但是，分隔符可以更改为任何单个字符或设置为`'auto'`。 `'auto'`设置为 时，读取器将分析字符串中的第一行文本。 然后，它将从下表中选择最常用的字符作为分隔符使用。

| | |
| :-- | :-- |
| 逗号 | `,` |
| 选项卡 | `\t` |
| Pipe | `|` |

此读取器还支持用于处理包含分隔符字符的单元格的文本限定符。 引号`'"'`（ ） 字符是默认文本限定符，但可以更改为任何单个字符。

## <a name="write-delimited-files"></a>写入分隔文件

将`atlas.io.core.CsvWriter`对象数组写入分隔字符串。 任何单个字符都可以用作分隔符或文本限定符。 默认分隔符为逗号 （`','`）， 默认文本限定符是引号`'"'`（ ） 字符。

要使用此类，请按照以下步骤操作：

- 创建类的实例，并可以选择设置自定义分隔符或文本限定符。
- 使用`write`函数或`writeRow`函数将数据写入类。 对于函数`write`，传递表示多个行和单元格的对象的二维数组。 要使用`writeRow`函数，传递表示具有多列的数据行的对象数组。
- 调用函数`toString`以检索分隔字符串。 
- 任由选择，`clear`调用方法以使编写器可重用并减少其资源分配，或调用`delete`方法以释放编写器实例。

> [!Note]
> 写入的列数将限制为传递给写入器的数据的第一行中的单元格数。

## <a name="read-xml-files"></a>读取 XML 文件

与`atlas.io.core.SimpleXmlReader`分析 XML 文件相比`DOMParser`，该类在解析 XML 文件时速度更快。 但是，`atlas.io.core.SimpleXmlReader`该类要求 XML 文件格式设置好。 格式不好的 XML 文件（例如缺少关闭标记）可能会导致错误。

以下代码演示如何使用 类`SimpleXmlReader`将 XML 字符串解析为 JSON 对象并将其序列化为所需的格式。

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>写入 XML 文件

该`atlas.io.core.SimpleXmlWriter`类以高效内存的方式编写格式良好的 XML。

以下代码演示如何使用 类`SimpleXmlWriter`生成格式良好的 XML 字符串。

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

从上述代码生成的 XML 如下所示。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [简单Xml阅读器](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [简单XmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)