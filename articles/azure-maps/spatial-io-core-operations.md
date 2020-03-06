---
title: 核心 IO 操作 |Microsoft Azure 映射
description: 了解如何使用空间 IO 模块中的核心库有效地读取和写入 XML 数据和分隔数据。
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d2a82fd5d9ba958fd6490a83ecbbe0a4bdf820a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370929"
---
# <a name="core-io-operations"></a>核心 IO 操作

除了提供读取空间数据文件的工具外，空间 IO 模块还公开核心基础库，以便快速高效地读取和写入 XML 数据和分隔的数据。

`atlas.io.core` 命名空间包含两个可快速读取和写入 CSV 和 XML 数据的低级别类。 这些基类使空间数据读取器和编写器在空间 IO 模块中供电。 可随意使用它们为 CSV 或 XML 文件添加额外的读取和写入支持。
 
## <a name="read-delimited-files"></a>读取分隔文件

`atlas.io.core.CsvReader` 类读取包含分隔数据集的字符串。 此类提供两种方法来读取数据：

- `read` 函数将读取完整的数据集，并返回表示分隔数据集的所有单元格的字符串的二维数组。
- `getNextRow` 函数读取分隔数据集中的每一行文本，并返回一个字符串数组，该数组表示该数据集行中的所有单元格。 在处理下一行之前，用户可以处理行并释放该行中的任何不需要的内存。 因此，函数的内存效率更高。

默认情况下，读取器将使用逗号字符作为分隔符。 但是，分隔符可以更改为任意单个字符或设置为 `'auto'`。 当设置为 `'auto'`时，读取器将分析字符串中的第一行文本。 然后，它将从下表中选择最常见的字符作为分隔符。

| | |
| :-- | :-- |
| 逗号 | `,` |
| 选项卡 | `\t` |
| Pipe | `|` |

此读取器还支持用于处理包含分隔符字符的单元格的文本限定符。 引号（`'"'`）字符是默认文本限定符，但可以更改为任意单个字符。

## <a name="write-delimited-files"></a>写入分隔文件

`atlas.io.core.CsvWriter` 以分隔字符串的形式写入对象数组。 任何单个字符均可用作分隔符或文本限定符。 默认分隔符为逗号（`','`），默认文本限定符为引号（`'"'`）。

若要使用此类，请按照以下步骤操作：

- 创建类的实例，并根据需要设置自定义分隔符或文本限定符。
- 使用 `write` 函数或 `writeRow` 函数将数据写入类。 对于 `write` 函数，传递表示多个行和单元格的对象的二维数组。 若要使用 `writeRow` 函数，请传递对象数组，该数组表示包含多个列的数据行。
- 调用 `toString` 函数以检索分隔的字符串。 
- （可选）调用 `clear` 方法，使编写器可重用并减小其资源分配，或调用 `delete` 方法来释放写入器实例。

> [!Note]
> 写入的列数将被限制为传递给写入器的数据的第一行中的单元格的数目。

## <a name="read-xml-files"></a>读取 XML 文件

在分析 XML 文件时，与 `DOMParser`相比，`atlas.io.core.SimpleXmlReader` 类更快。 但 `atlas.io.core.SimpleXmlReader` 类要求 XML 文件的格式正确。 格式不正确的 XML 文件（如缺少结束标记）可能会导致错误。

下面的代码演示如何使用 `SimpleXmlReader` 类将 XML 字符串分析为 JSON 对象并将其序列化为所需的格式。

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

`atlas.io.core.SimpleXmlWriter` 类以内存有效的方式写入格式正确的 XML。

下面的代码演示如何使用 `SimpleXmlWriter` 类生成格式正确的 XML 字符串。

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

以上代码中生成的 XML 将如下所示。

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
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)