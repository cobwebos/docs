---
title: "为应用商店创建数据服务指南 | Microsoft Docs"
description: "详细说明如何创建、验证和部署在 Azure 应用商店上提供购买的数据服务。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3a632825-db5b-49ec-98bd-887138798bc4
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: a853b4dbd1952ba4ea8ee68ea3ca98f588bb71a2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>通过 CSDL 将现有 Web 服务映射到 OData
> [!IMPORTANT]
> **本次我们不再载入任何新的数据服务发布服务器。新的 dataservices 将不会获准出现在列表中。** 如果想要在 AppSource 上发布 SaaS 业务应用程序，可以在[此处](https://appsource.microsoft.com/partners)找到更多信息。 如果想要在 Azure 应用商店上发布 IaaS 应用程序或开发人员服务，可以在[此处](https://azure.microsoft.com/marketplace/programs/certified/)找到更多信息。
> 
> 

本文概述了如何使用 CSDL 将现有服务映射到 OData 兼容服务。 它介绍了如何创建映射文档 (CSDL)，该文档可用于将来自经由服务调用和输出（数据）的客户端的输入请求转换回为经由 OData 兼容源的客户端。 Microsoft 的 Azure 应用商店通过使用 OData 协议向最终用户公开服务。 内容提供商（数据所有者）公开的服务以各种形式（例如，REST、SOAP 等）公开。

## <a name="what-is-a-csdl-and-its-structure"></a>什么是 CSDL 及其结构？
CSDL（概念架构定义语言）是一种规范，定义如何使用通用 XML 语言向 Azure 应用商店描述 Web 服务或数据库服务。

简要概述**请求流**：

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

**数据流**方向与之相反：

  `Client <- Azure Marketplace <- Content Provider’s WebService`


            **图 1**说明客户端如何通过访问 Azure 应用商店获取内容提供商（服务）的数据。  映射/转换组件使用 CSDL 处理请求以及内容提供商服务和请求客户端之间的数据传递。

*图 1：通过 Azure 应用商店从请求客户端到内容提供商的详细流程*

  ![绘制](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

有关 Azure 应用商店扩展生成所依据的 Atom、Atom Pub 和 OData 协议的背景信息，请参阅：[http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

上述链接中的节选：*“开发数据协议（以下称为 OData）的用途是针对已公开为数据服务的资源提供适用于 CRUD 样式操作（创建、读取、更新和删除）的基于 REST 的协议。“数据服务”是一个终结点，其中含有一个或多个“集合”公开的数据，每个集合带有零个或多个“条目”，这组成了类型化的命名值对。OData 由 Microsoft 根据 OASIS（结构化信息标准促进组织）标准发布，以便任何人都可以用它生成服务器、客户端或工具，无需缴付版税，也不会受到限制。*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>CSDL 必须定义的三个关键部分如下所示：
* 服务提供商的**终结点**，即服务的 Web 地址 (URI)
* 要作为输入传递到服务提供商的**数据参数**，即要发送给内容提供商服务的参数的定义以及数据类型。
* 要返回给请求服务的数据的**架构**，即内容提供商服务传递的数据的架构，包括容器、集合/表、变量/列和数据类型。

下图概述了从客户端输入 OData 语句（对内容提供商 Web 服务的调用）到取回结果/数据的流程。

  ![绘制](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>步骤：
1. 客户端通过服务调用向 Azure 应用商店发送请求（连同以 XML 定义的输入参数在内）
2. CSDL 用于验证服务调用。
   * 然后由 Azure 应用商店将完成格式设置的服务调用发送给内容提供商服务
3. Webservice 执行并完成 Http 动作操作（即 GET），即数据返回给 Azure 应用商店，其中请求的数据（如果有）将使用以 CSDL 定义的映射公开给客户端（XML 格式）。
4. 客户端以 XML 或 JSON 格式发送数据（如果有）

## <a name="definitions"></a>定义
### <a name="odata-atom-pub"></a>OData ATOM Pub
ATOM Pub 的扩展，其中每个条目表示结果集的一行。 条目的内容部分得到增强，包含行的值 - 作为键值对。 有关详细信息，请参阅：[https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - 概念架构定义语言
允许定义通过数据库公开的函数 (SPROC) 和实体。 有关详细信息，请参阅：[http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [!TIP]
> 如果未看到文章，请单击“其他版本”下拉列表，并选择某个版本。
> 
> 

### <a name="edm---entry-data-model"></a>EDM - 入口数据模型
* 概述：[http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx][OverviewLink]

[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
* 预览：[http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx][PreviewLink]

[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
* 数据类型：[http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][DataTypesLink]

[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

以下从左到右详细介绍了从客户端输入 OData 语句（对内容提供商 Web 服务的调用）到取回结果/数据的流程：

  ![绘制](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)

## <a name="csdl-basics"></a>CSDL 基础知识
CSDL（概念架构定义语言）是一种规范，定义如何使用通用 XML 语言向 Azure 应用商店描述 Web 服务或数据库服务。 CSDL 描述的关键部分**使数据能够从数据源传递到 Azure 应用商店**。 主要部分描述如下：

* 描述所有公开可用函数的接口信息（FunctionImport 节点）
* 所有消息请求（输入）和消息响应（输出）的数据类型信息（EntityContainer/EntitySet/EntityType 节点）
* 关于要使用的传输协议的绑定信息（Header 节点）
* 查找指定服务的地址信息（BaseURI 属性）

简而言之，CSDL 表示服务请求者和服务提供商之间独立于平台和语言的协定。 通过使用 CSDL，客户端可以找到 Web 服务/数据库服务，并调用其公开可用的任何函数。

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>将 CSDL 与数据库或集合关联
**CSDL 规范**

CSDL 是用于描述 Web 服务的 XML 语法。 规范本身划分为 4 个主要元素：EntitySet、FunctionImport、NameSpace 和 EntityType。

为了使这种抽象概念更容易理解，请将 CSDL 与表关联。

请记住；

  CSDL 表示**服务请求者**和**服务提供商**之间独立于平台和语言的协定。 通过使用 CSDL，**客户端**可以找到 **Web 服务/数据库服务**，并调用其公开可用的任何**函数**。

对于数据服务，CSDL 的四个部分可以考虑使用 Database、Table、Column 和 Store Procedure。

为数据服务关联以下所示内容：

* EntityContainer  ~=  Database
* EntitySet  ~=  Table
* EntityType  ~= Columns
* FunctionImport  ~=  Stored Procedure

**允许的 HTTP 动作**

* GET – 从数据库返回值（返回集合）
* POST – 用于将数据传递到数据库，从数据库选择性返回值（在集合中创建新的条目，返回 ID/URI）
* DELETE – 从数据库中删除数据（删除集合）
* PUT – 将数据更新到数据库（替换集合或创建一个集合）

## <a name="metadatamapping-document"></a>元数据/映射文档
元数据/映射文档用于映射内容提供商的现有 Web 服务，以便 Azure 应用商店系统可以将它公开为 OData Web 服务。 它基于 CSDL，并实现了 CSDL 的一些扩展，以满足通过 Azure 应用商店公开的基于 REST 的 Web 服务需求。 [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) 命名空间中可以找到这些扩展。

以下是 CSDL 的一个示例：（将以下 CSDL 示例复制并粘贴到 XML 编辑器并更改，以匹配服务。  然后在 [Azure 应用商店发布门户](https://publish.windowsazure.com)中创建服务时，将修改内容粘贴到“DataService”选项卡下的 CSDL 映射）。

**条款：**将 CSDL 条款与[发布门户](https://publish.windowsazure.com) UI (PPUI) 条款关联。

* PPUI 中的优惠“标题”与 MyWebOffer 关联
* PPUI 中的 MyCompany 与 [Microsoft 开发人员中心](http://dev.windows.com/registration?accountprogram=azure) UI 中的**发布者显示名称**关联
* API 与 Web 或数据服务（PPUI 中的计划）关联

**层次结构：** （内容提供程序） 的公司拥有 Offer(s) 其具有个计划，即服务 API 向上哪些行。

### <a name="webservice-csdl-example"></a>WebService CSDL 示例
连接到将公开 Web 应用程序终结点（类似 C# 应用程序）的服务

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"    d:IsPrimaryKey="True" Type="Int32"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"    Type="Double"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"    Type="String"    Nullable="false" d:Map="./City"/>
        <Property Name="State"    Type="String"    Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime"    Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [!TIP]
> 查看[通过 CSDL 将现有 Web 服务映射到 OData 示例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)一文，可找到更多 CSDL Web 服务示例
> 
> 

### <a name="dataservice-csdl-example"></a>DataService CSDL 示例
连接到将公开数据库表或视图为终结点的服务。以下示例显示了适用于基于 API CSDL 的数据库的两个 API（可以使用视图而不是表）。

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>另请参阅
* 如果有兴趣学习和了解特定节点及其参数，请阅读[数据服务 OData 映射节点](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)一文以了解有关定义和说明、 示例和使用案例上下文。
* 如果有兴趣查看示例，请阅读[数据服务 OData 映射示例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)一文以查看示例代码，了解代码的语法和上下文。
* 要返回到用于将数据服务发布到 Azure 应用商店的指定路径，请阅读这篇文章[数据服务发布指南](marketplace-publishing-data-service-creation.md)。

