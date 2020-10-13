---
title: 转换行业标准模型
titleSuffix: Azure Digital Twins
description: 了解将行业标准 (RDF/OWL) 模型转换为 DTDL 的模式
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 76d1fd91053216103ef6ace0e56979c57eca569f
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002622"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>将行业标准模型转换为适用于 Azure 数字孪生的 DTDL

Azure 数字孪生中的模型以基于 JSON LD 的 [**数字孪生定义语言表示， (DTDL) **](concepts-models.md)。 如果现有的 Azure 数字孪生外部的模型基于行业标准（如 RDF 或 OWL），则需要 **将其转换为 DTDL** ，以将其与 Azure 数字孪生配合使用。 然后，DTDL 版本将成为 Azure 数字孪生中模型的真实来源。

本文介绍了一种将基于 RDF 的行业或自定义模型转换为 DTDL 的模式。 其中包括：
* 可应用于多种标准和模型类型的**策略级别指南**
* [RDF 特定转换器的**示例代码**](#sample-converter-application)

## <a name="industry-models"></a>行业模型  

在设计 Azure 数字孪生模型时，使用行业模型可提供丰富的起点。 使用行业模型还有助于标准化和信息共享。 

一些常见的行业模型包括：  

| 行业垂直 | “模型” |
| --- | --- | 
| 构建/设施管理 | [RealEstateCore](https://www.realestatecore.io/)<br>[程序块架构](https://brickschema.org/ontology/1.1/)<br>[构建拓扑 Ontology (机器人) ](https://w3c-lbd-cg.github.io/bot/)<br>[语义传感器网络](https://www.w3.org/TR/vocab-ssn/)<br>[buildingSmart 行业基础类 (IFC) ](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| 智能城市 | [ETSI NGSI](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[智能应用程序参考 (SAREF) ](https://saref.etsi.org/) |
| 能源网格 | [CIM](https://cimug.ucaiug.org/) /[IEC 61968](https://en.wikipedia.org/wiki/IEC_61968) | 

根据您的需要，您还可以使用 DTDL 自定义或扩展行业模型，或者从头开发您自己的自定义模型。 

## <a name="create-and-edit-models"></a>创建和编辑模型

建模的第一步是创建模型。 在将行业标准模型转换为 DTDL 之前，可以创建和完成对其进行编辑，也可以及早将它们转换为 DTDL，并继续将其作为 DTDL 文档进行编辑。

### <a name="with-industry-standards"></a>采用行业标准

大多数行业模型 (也称为 **本体论**) 基于语义 web 标准，例如 [OWL](https://www.w3.org/OWL/[)、 [RDF](https://www.w3.org/2001/sw/wiki/RDF)和 [RDFS](https://www.w3.org/2001/sw/wiki/RDFS)。 

在某些情况下，你可能想要使用基于 OWL 的模型工具创建或编辑模型。 您可以使用任意数量的模型创作工具来设计基于 OWL 的模型，其中包括：
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) 和 [Protégé桌面](https://protege.stanford.edu/products.php#desktop-protege) 是常见的示例。 您可以采用多种格式导入行业模型，编辑或扩展模型，以及导出模型。 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) 是用于可视化模型的另一常用工具。 

如果使用某个不 DTDL 的行业标准创建模型，则需要将其转换为 DTDL，并将其上传到 Azure 数字孪生。 

#### <a name="common-model-file-formats"></a>常见模型文件格式 

RDF、OWL 和 RDFS 是语义 web 的基本构建基块。 

**RDF** 提供了一个概念结构，用于描述使用 **三元**组的情况。 三部分由三部分组成： **主题**、 **谓词**和 **对象**。 对象可由 Uri 组成。 

下面是 RDF 三元组的一些示例：

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

这些示例都是有效的 RDF，但最后一条语句在语义上无效。 在这种情况下，OWL 规范进入图片。 **OWL** 定义了可以使用 RDF 编写的内容，以便具有有效的 ontology。

下面是使用 OWL 的示例： 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**RDFS** 提供了其他词汇语义，可帮助你定义和描述类。 例如，其中一个类是 `rdfs:subClassOf` ：

```
<Equipment> <subClassOf> <Asset>
```

可以采用多种文件格式保存、导入和导出模型，其中包括：  
* RDF/XML 
* 龟标 (TTL)  
* OWL/XML 

### <a name="with-dtdl"></a>With DTDL

Azure 数字孪生使用基于 JSON LD 的数字克隆 **定义语言 (DTDL) ** 进行建模。 将与 Azure 数字孪生一起使用的任何模型都需要最初写入或转换为 DTDL。

使用 DTDL 中的模型时，可以使用可用于 Visual Studio Code 的[**DTDL 扩展**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   ，此扩展提供语法验证和其他功能以便于编写 DTDL 模型。 [Visual Studio Code](https://code.visualstudio.com/)

可以在概念中阅读有关 Azure 数字孪生模型及其组件的详细信息 [*：自定义模型*](concepts-models.md) 和 [*操作方法：管理自定义模型*](how-to-manage-model.md)。

## <a name="convert-models-to-dtdl"></a>将模型转换为 DTDL

若要将模型用于 Azure 数字孪生，它必须采用 DTDL 格式。 本部分介绍如何将基于 RDF 的模型转换为 DTDL，以便可以将它们与 Azure 数字孪生配合使用。

可以使用多个第三方库，在将基于 RDF 的模型转换为 DTDL 时可以使用它们。 其中一些库允许您将模型文件加载到图形中。 您可以循环遍历图形查找特定的 RDFS 和 OWL 构造，并将其转换为 DTDL。   

下表举例说明了如何将 RDFS 和 OWL 构造映射到 DTDL。 

| RDFS/OWL 概念 | RDFS/OWL 构造 | DTDL 概念 | DTDL 构造 |
| --- | --- | --- | --- |
| 类 | `owl:Class`<br>IRI 后缀<br>``rdfs:label``<br>``rdfs:comment`` | 接口 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| 对  进行子类化 | `owl:Class`<br>IRI 后缀<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | 接口 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| 数据类型属性 | `owl:DatatypeProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:label`<br>`rdfs:range` | 接口属性 | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| 对象属性 | `owl:ObjectProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target`如果没有) ，则 (或省略 `rdfs:range`<br>`comment`<br>`displayName`<br>

下面的 c # 代码段演示如何使用 [**dotNetRDF**](https://www.dotnetrdf.org/) 库将 RDF 模型文件加载到图形并转换为 DTDL。 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>验证并上传 DTDL 模型

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

转换并验证某一模型后，可以将 **其上传到 Azure 数字孪生实例**。 有关此过程的详细信息，请参阅*操作方法：管理自定义模型*的 "[*上载模型*](how-to-manage-model.md#upload-models)" 部分。

## <a name="sample-converter-application"></a>示例转换器应用程序 

有一个可用的示例应用程序，它将基于 RDF 的模型文件转换为 [DTDL (版本 2) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) ，以供 Azure 数字孪生服务使用。 该示例是一个名为 **RdfToDtdlConverter**的 .net Core 命令行应用程序。

可在此处获取示例： [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

若要将代码下载到你的计算机，请点击示例登录页上标题下面的 " *下载 ZIP* " 按钮。 这会下载名称*RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*下的*ZIP*文件，然后你可以对其进行解压缩和浏览。

您可以使用此示例来查看上下文中的转换模式，还可以将其用作自己的应用程序的构建基块，根据自己的特定需要执行模型转换。

## <a name="next-steps"></a>后续步骤 

详细了解如何设计和管理数字克隆模型：
 
* [*概念：自定义模型*](concepts-models.md)
* [*操作说明：管理自定义模型*](how-to-manage-model.md)
* [*操作说明：分析和验证模型*](how-to-parse-models.md)