---
title: "为应用商店创建数据服务指南 | Microsoft Docs"
description: "详细说明如何创建、验证和部署在 Azure 应用商店上提供购买的数据服务。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 107baab2-5828-4158-abdf-59a580c80d37
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 8ff76ea21ba684ae2a2afcb74d66b4912d7be053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>了解通过 CSDL 将现有 Web 服务映射到 OData 的节点架构
> [!IMPORTANT]
> **本次我们不再载入任何新的数据服务发布服务器。新的 dataservices 将不会获准出现在列表中。** 如果想要在 AppSource 上发布 SaaS 业务应用程序，可以在[此处](https://appsource.microsoft.com/partners)找到更多信息。 如果想要在 Azure 应用商店上发布 IaaS 应用程序或开发人员服务，可以在[此处](https://azure.microsoft.com/marketplace/programs/certified/)找到更多信息。
>
>

本文档将帮助阐明用于将 OData 协议映射到 CSDL 的节点结构。 务必注意节点结构是格式正确的 XML。 因此，在设计 OData 映射时根节点、父节点和子节点结构也适用。

## <a name="ignored-elements"></a>被忽略的元素
以下是 Azure 应用商店后端在 Web 服务的元数据导入过程中将不会使用的高级 CSDL 元素（XML 节点）。 它们可以存在，但会被忽略。

| 元素 | 范围 |
| --- | --- |
| Using 元素 |节点、子节点和所有属性 |
| Documentation 元素 |节点、子节点和所有属性 |
| ComplexType |节点、子节点和所有属性 |
| Association 元素 |节点、子节点和所有属性 |
| 扩展的属性 |节点、子节点和所有属性 |
| EntityContainer |仅以下属性被忽略：*extends* 和 *AssociationSet* |
| 架构 |仅以下属性被忽略：*Namespace* |
| FunctionImport |仅以下属性被忽略：*Mode*（假定默认值为 ln） |
| EntityType |仅以下子节点被忽略：*Key* 和 *PropertyRef* |

以下部分将详细介绍对各种 CSDL XML 节点的更改（添加和被忽略的元素）。

## <a name="functionimport-node"></a>FunctionImport 节点
FunctionImport 节点表示一个将服务公开给最终用户的 URL（入口点）。 此节点允许介绍如何查找上述 URL，哪些参数可供最终用户使用以及如何提供这些参数。

可在[此处][MSDNFunctionImportLink](https://msdn.microsoft.com/library/cc716710.aspx)找到有关此节点的详细信息

以下是由 FunctionImport 节点公开的其他属性 （或新增属性）：

**d:BaseUri** - 向应用商店公开的 REST 资源的 URI 模板。 市场上使用该模板来构建针对 REST Web 服务的查询。 URI 模板包含 {parameterName} 形式的参数占位符，其中 parameterName 是参数名称。 例如： apiVersion = {apiVersion}。
参数可以作为 URI 参数或作为 URI 路径的一部分出现。 出现在路径中时，参数始终是必需的（不能标记为可为 null）。 *示例：* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**名称** — 已导入函数的名称。  不能与 CSDL 中其他定义的名称相同。  例如： 名称 ="GetModelUsageFile"

**EntitySet***（可选）*- 如果函数返回实体类型的集合，**EntitySet** 的值必须是该集合所属的实体集。 否则，不得使用 **EntitySet** 属性。 *示例：* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType***（可选）*- 指定 URI 返回的元素类型。  如果函数未返回值，则不要使用此属性。 以下是支持的类型：

* **Collection (<Entity type name>)**：指定已定义实体类型的集合。 名称显示在 EntityType 节点的 Name 属性中。 示例为 Collection(WXC.HourlyResult)。
* **Raw (<mime type>)**：指定返回给用户的原始文档/blob。 示例为 Raw(image/jpeg) 其他示例：

  * ReturnType="Raw(text/plain)"
  * ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging** - 指定 REST 资源如何处理分页。 参数值在大括号内使用，例如 page={$page}&itemsperpage={$size} 可用的选项为：

* **None：**无分页可用
* **Skip：**分页通过逻辑“skip”和“take”（顶部）表示。 “skip”跳过 M 元素，“take”随之返回后续的 N 元素。 参数值：$skip
* **Take：**Take 返回后续的 N 元素。 参数值：$take
* **PageSize：**分页通过逻辑页和大小（每页的项目）表示。 Page 表示返回的当前页。 参数值：$page
* **Size：** Size 表示为每页返回的项目数。 参数值：$size

**d:AllowedHttpMethods***（可选）*- 指定 REST 资源处理哪个谓词。 此外，将接受的谓词限制为指定值。  默认值 = POST。  *示例：* `d:AllowedHttpMethods="GET"`可用的选项为：

* **GET：**通常用于返回数据
* **POST：**通常用于插入新数据
* **PUT：**通常用于更新数据
* **DELETE：**用于删除数据

FunctionImport 节点内的其他子节点（CSDL 文档未涉及）为：

**d:RequestBody** *（可选）*- 请求正文用于指示请求预期发送正文。 参数可在请求正文内提供。 它们在大括号内表示，例如 {parameterName}。 这些参数从用户输入映射到传输到内容提供商服务的正文中。 requestBody 元素具有名称为 httpMethod 的属性。 属性允许两个值：

* **POST：** 当请求为 HTTP POST 时使用
* **GET：** 当请求为 HTTP GET 时使用

    示例：

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** 和 **d:Namespace** - 此节点描述在函数导入（URI 终结点）返回的 XML 中定义的命名空间。 后端服务返回的 XML 可能包含任意数量的命名空间，以区分返回的内容。 **如果在 d:Map 或 d:Match XPath 查询中使用，需要列出所有这些命名空间。** d:Namespaces 节点包含 d:Namespace 节点集/列表。 其中每一个列出在后端服务响应中使用的命名空间。 以下是 d:Namespace 节点的属性：

* **d:Prefix：**命名空间的前缀，如服务返回的 XML 结果中所示，例如 f:FirstName、f:LastName，其中 f 是前缀。
* **d:Uri：**结果文档中使用的命名空间的完整 URI。 它表示前缀在运行时解析为的值。

**d:ErrorHandling***（可选）* - 此节点包含用于错误处理的条件。 针对内容提供商的服务返回的结果验证每个条件。 如果条件与建议的 HTTP 错误代码匹配，则向最终用户返回一条错误消息。

**d:ErrorHandling***（可选）*和 **d:Condition** *（可选）* - 条件节点承载一个在内容提供商服务返回的结果中检查的条件。 以下是**必需的**属性：

* **d:Match：**验证给定节点/值是否显示在内容提供商的输出 XML 中的 XPath 表达式。 针对输出运行 XPath，如果条件匹配，应返回 true，否则返回 false。
* **d:HttpStatusCode：**当条件匹配时应用商店应返回的 HTTP 状态代码。 应用商店通过 HTTP 状态代码向用户指出错误。 HTTP 状态代码列表在 http://en.wikipedia.org/wiki/HTTP_status_code 提供
* **d:ErrorMessage：**（通过 HTTP 状态代码）向用户返回的错误消息。 这应是不包含任何机密的友好错误消息。

**d:Title** *（可选）*- 运行描述函数的标题。 标题的值来自

* 可选映射属性（一个 xpath），用于指定在从服务请求返回的响应中查找标题的位置。
* -或 - 标题指定为节点的值。

**d:Rights***（可选）* - 与函数相关联的权利（例如版权）。 权利的值来自：

* 可选映射值（一个 xpath），用于指定在从服务请求返回的响应中查找权利的位置。
* -或 - 权利指定为节点的值。

**d:Description** *（可选）* - 函数的简短说明。 说明的值来自

* 可选映射属性（一个 xpath），用于指定在从服务请求返回的响应中查找说明的位置。
* -或 - 说明指定为节点的值。

**d:EmitSelfLink** - *请参阅上述示例“用于通过返回的数据‘分页’的 FunctionImport”*

**d:EncodeParameterValue** - OData 的可选扩展

**d:QueryResourceCost** - OData 的可选扩展

**d:Map** - OData 的可选扩展

**d:Headers** - OData 的可选扩展

**d:Headers** - OData 的可选扩展

**d:Value** - OData 的可选扩展

**d:HttpStatusCode** - OData 的可选扩展

**d:ErrorMessage** - OData 的可选扩展

## <a name="parameter-node"></a>参数节点
此节点表示作为 URI 模板 / 请求正文（已在 FunctionImport 节点中指定）的一部分公开的一个参数。

可在[此处](http://msdn.microsoft.com/library/ee473431.aspx)找到关于“参数元素”节点的非常有帮助的详细信息文档页（必要时使用“其他版本”下拉列表查看该文档）。 *示例：* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| 参数属性 | 是否必需 | 值 |
| --- | --- | --- |
| Name |是 |参数的名称。 区分大小写！  与 BaseUri 大小写匹配。 **示例：** `<Property Name="IsDormant" Type="Byte" />` |
| 类型 |是 |参数类型。 值必须为 **EDMSimpleType** 或模型范围内的复杂类型。 有关详细信息，请参阅“6 种受支持的参数/属性类型”。  （区分大小写！ 第一个字符是大写，其余是小写。）另请参阅[概念模型类型 (CSDL)][MSDNParameterLink](http://msdn.microsoft.com/library/bb399548.aspx)。 **示例：**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode |否 |**In**、Out 或 InOut 取决于参数是输入、输出还是输入/输出参数。 （仅“IN”在 Azure 应用商店中可用。）**示例：** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength |否 |允许的参数最长长度。 **示例：**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precision |否 |参数的精度。 **示例：**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| 缩放 |否 |参数的确定位数。 **示例：** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

以下是已添加到 CSDL 规范的属性：

| 参数属性 | 说明 |
| --- | --- |
| **d:Regex***（可选）* |用于验证参数的输入值的正则表达式语句。 如果输入值与语句不匹配，则拒绝该值。 这允许还指定一组可能的值，例如 ^[0-9]+?$ 以仅允许数字。 **示例：** `<Parameter Name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="无法包含任何空格或非字母非英语字符的名称" d:SampleValues="George |
| **d:Enum***（可选）* |参数有效值的竖线分隔列表。 值的类型需要与参数的已定义类型匹配。 例如：`english |
| **d:Nullable***（可选）* |允许定义参数是否可以为 null。 默认值为：true。 但是，作为 URI 模板中的路径的一部分公开的参数不能为 null。 当为这些参数将属性设置为 false 时，重写用户输入。 **示例：** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *（可选）* |在 UI 中作为注释向客户端显示的示例值。  可通过使用竖线分隔的列表添加多个值，即 `a |

## <a name="entitytype-node"></a>EntityType 节点
此节点表示从应用商店返回给最终用户的类型之一。 它还包含从内容提供商服务返回的输出到返回给最终用户的值的映射。

可在[此处](http://msdn.microsoft.com/library/bb399206.aspx)找到关于此节点的详细信息（必要时使用“其他版本”下拉列表查看该文档。）

| 属性名称 | 是否必需 | 值 |
| --- | --- | --- |
| Name |是 |实体类型的名称。 **示例：**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType |否 |作为正在定义的实体类型的基类型的另一个实体类型。 **示例：**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

以下是已添加到 CSDL 规范的属性：

**d:Map** - 针对服务输出执行的 XPath 表达式。 此处的假设是，服务输出包含一组重复的元素，如有一组重复的实体节点的 ATOM 源。 其中每个重复的节点都包含一个记录。 然后，将 XPath 指定为指向内容提供商服务结果中的个别重复节点，该节点承载个别记录的值。 来自服务的示例输出：

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

XPath 表达式将是 /foo/bar，因为 bar 节点是输出中的重复节点，并且它包含返回给最终用户的实际内容。

**Key** - 应用商店忽略此属性。 基于 REST 的 Web 服务通常不公开主键。

## <a name="property-node"></a>属性节点
此节点包含记录的一个属性。

可在 [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) 找到关于此节点的详细信息（必要时使用 **其他版本** 下拉列表查看该文档。）*示例：*`<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"     Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | 必选 | 值 |
| --- | --- | --- |
| Name |是 |属性的名称。 |
| 类型 |是 |属性值的类型。 属性值类型必须为 **EDMSimpleType** 或模型范围内的复杂类型（由完全限定名称指示）。 有关详细信息，请参阅概念模型类型 (CSDL)。 |
| Nullable |否 |**True**（默认值）或 **False**，取决于属性是否可以具有 null 值。 注意：在 [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) 命名空间指示的 CSDL 版本中，复杂类型属性必须使 Nullable="False"。 |
| DefaultValue |否 |属性的默认值。 |
| MaxLength |否 |属性值的最大长度。 |
| FixedLength |否 |**True** 或 **False**，取决于属性值是否以固定长度字符串形式存储。 |
| Precision |否 |指在数值中保留的最大数字位数。 |
| 缩放 |否 |在数值中保留的最大小数位数。 |
| Unicode |否 |**True** 或 **False**，取决于属性值是否以 Unicode 字符串的形式存储。 |
| Collation |否 |指定要在数据源中使用的排序的字符串。 |
| ConcurrencyMode |否 |**无**（默认值）或**固定**。 如果此值设置为**固定**，会在乐观并发检查中使用属性值。 |

以下是已添加到 CSDL 规范的其他属性：

**d:Map** - 针对服务输出执行的 XPath 表达式，并提取输出的一个属性。 该 XPath 相对于已在 EntityType 节点的 XPath 中选择的重复节点指定。 还可以指定一个绝对 XPath 以允许在每个输出节点中包含静态资源，例如，仅在原始服务输出中找到一次但应在 OData 输出的每行中显示的版权声明。 来自服务的示例：

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

此处的 XPath 表达式将是 /bar/baz0，以从内容提供商的服务获取 baz0 节点。

**d:CharMaxLength** - 对于字符串类型，可指定最大长度。 请参阅 DataService CSDL 示例

**d:IsPrimaryKey** - 指示列在表/视图中是否是主键。 请参阅 DataService CSDL 示例。

**d:isExposed** - 确定是否公开表架构（通常为 true）。 请参阅 DataService CSDL 示例

**d:IsView***（可选）* - 如果这基于视图而不是表，则为 true。  请参阅 DataService CSDL 示例

**d:Tableschema** - 请参阅 DataService CSDL 示例

**d:ColumnName** - 是表/视图中的列名称。  请参阅 DataService CSDL 示例

**d:IsReturned** - 是确定服务是否向客户端公开此值的布尔值。  请参阅 DataService CSDL 示例

**d:IsQueryable** - 是确定列是否可在数据库查询中使用的布尔值。   请参阅 DataService CSDL 示例

**d:OrdinalPosition** - 是列在表或视图中出现的数值位置 x，其中 x 的范围是从 1 到表中的列数。  请参阅 DataService CSDL 示例

**d:DatabaseDataType** - 是数据库中列的数据类型，即 SQL 数据类型。 请参阅 DataService CSDL 示例

## <a name="supported-parametersproperty-types"></a>支持的参数/属性类型
以下是受支持的参数和属性类型。 （区分大小写）

| 基元类型 | 说明 |
| --- | --- |
| Null |表示值不存在 |
| 布尔 |表示二进制值逻辑的数学概念 |
| Byte |无符号的 8 位整数值 |
| DateTime |表示日期和时间，值的范围是从公元 1753 年 1 月 1 日午夜 12:00:00 到公元 9999 年下午 11:59:59。 |
| 小数 |表示具有固定精度和确定位数的数值。 此类型可描述范围为从负 10^255 + 1 到正 10^255 -1 的数值。 |
| Double |表示具有 15 位精度的浮点数，可表示范围大约为 ± 2.23e -308 到 ± 1.79e +308 的值。 **由于 Exel 导出问题，使用小数** |
| Single |表示具有 7 位精度的浮点数，可表示范围大约为 ± 1.18e -38 到 ± 3.40e +38 的值。 |
| Guid |表示 16 字节（128 位）唯一标识符值 |
| Int16 |表示有符号的 16 位整数值 |
| Int32 |表示有符号的 32 位整数值 |
| Int64 |表示有符号的 64 位整数值 |
| String |表示固定或可变长度的字符数据 |

## <a name="see-also"></a>另请参阅
* 如果有兴趣了解全面的 OData 映射过程和用途，请阅读文章“[Data Service OData Mapping](marketplace-publishing-data-service-creation-odata-mapping.md)”（数据服务 OData 映射）以查看定义、结构和说明。
* 如果有兴趣查看示例，请阅读[数据服务 OData 映射示例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)一文以查看示例代码，了解代码的语法和上下文。
* 要返回到用于将数据服务发布到 Azure 应用商店的指定路径，请阅读这篇文章[数据服务发布指南](marketplace-publishing-data-service-creation.md)。
