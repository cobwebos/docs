---
title: "数据目录开发人员概念 | Microsoft Docs"
description: "Azure 数据目录概念模型中关键概念的简介，如目录 REST API 中所示。"
services: data-catalog
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
tags: 
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/19/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9591d7e2dd743e44d228205163ad55cb47992283
ms.lasthandoff: 12/08/2016


---
# <a name="azure-data-catalog-developer-concepts"></a>Azure 数据目录开发人员概念
Microsoft **Azure 数据目录** 是一种完全托管的云服务，提供了数据源发现和众包数据源元数据的功能。 开发人员可通过其 REST API 使用此服务。 了解在服务中实现的概念对于开发人员能成功与 **Azure 数据目录**集成非常重要。

## <a name="key-concepts"></a>关键概念
**Azure 数据目录**概念模型基于四个关键概念：**目录**、**用户**、**资产****批注**。

![概念][1]

*图 1 - Azure 数据目录简化概念模型*

### <a name="catalog"></a>目录
**目录**是组织存储的所有元数据的顶级容器。 每个 Azure 帐户允许具有一个**目录**。 目录绑定到 Azure 订阅，虽然一个帐户可具有多个订阅，但仅能为给定 Azure 帐户创建一个**目录**。

目录包含**用户**和**资产**。

### <a name="users"></a>用户
用户是安全主体，具有在目录中执行操作的权限（搜索目录；添加、编辑或删除项等）。

一个用户可具有多个不同的角色。 有关角色的信息，请参阅“角色和授权”部分。

可添加单个用户和安全组。

Azure 数据目录使用 Azure Active Directory 标识和访问管理。 每个目录用户必须是帐户的 Active Directory 的成员。

### <a name="assets"></a>资产
**目录**包含数据资产。 **资产** 是由目录管理的粒度的单位。

资产的粒度因数据源而改变。 对于 SQL Server 或 Oracle 数据库，资产可以是表或视图。 对于 SQL Server Analysis Services，资产可以是度量值、维度或关键绩效指标 (KPI)。 对于 SQL Server Reporting Services，资产是报表。

**资产**可从目录添加或删除。 它为你从“搜索”中得到的结果的单位。

**资产**由名称、位置、类型和批注（进一步描述）构成。

### <a name="annotations"></a>批注
批注是表示有关资产的元数据的项。

批注的示例包括说明、标记、架构、文档等。资产类型和批注类型的完整列表位于资产对象模型部分。

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>众包批注和用户角度（观点的多样性）
Azure 数据目录的一个重要方面是它如何在系统中支持元数据的众包。 与 Wiki 方法不同（只存在一个观点且以最后写入者为准），Azure 数据目录模型允许多个观点在系统中共同存在。

此方法反映了企业数据的真实世界，其中不同的用户对于给定资产可拥有不同的观点：

* 数据库管理员可能提供有关服务级别协议的信息，或批量 ETL 操作的可用处理窗口
* 数据专员可能提供有关应用资产的业务流程或已应用业务的分类的信息
* 财务分析人员可能会提供有关如何在期终报表期间使用数据的信息

若要支持此示例，每个用户（DBA、数据专员和分析师）都可将说明添加到已在目录中注册的单个表中。 所有说明都保留在系统中，并在 Azure 数据目录门户中显示所有的说明。

此模式适用于对象模型中的大多数项，因此 JSON 有效负载中的对象类型通常为属性的数值（预期为单一属性）。

例如，资产根下为说明对象的数组。 数组属性名为“说明”。 说明对象都具有一个属性 - 说明。 该模式为键入说明的每个用户获得由用户提供的为值创建的说明对象。

然后 UX 可选择如何显示该组合。 有三种不同的显示模式。

* 最简单的模式为“全部显示”。 在此模式中，所有对象都显示在列表视图中。 Azure 数据目录门户 UX 使用这种模式进行说明。
* 另一种模式是“合并”。 在此模式中，不同用户的所有值都合并在一起（删除副本）。 Azure 数据目录门户 UX 中此模型的示例为标记和专家属性。
* 第三个模式是“以最后写入者为准”。 在此模式下，仅显示最后键入的值。 friendlyName 是此模式的示例。

## <a name="asset-object-model"></a>资产对象模型
如“关键概念”部分中所述，**Azure 数据目录**对象模型包括项（可为资产或批注）。 项具有属性，为可选或必须。 某些属性适用于所有项。 某些属性适用于所有资产。 某些属性仅适用于特定资产类型。

### <a name="system-properties"></a>系统属性
<table><tr><td><b>属性名称</b></td><td><b>数据类型</b></td><td><b>注释</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>最后一次修改项的时间。 此字段是由服务器在插入项以及每次更新项时生成的。 此属性的值在发布操作的输入上被忽略。</td></tr><tr><td>id</td><td>Uri</td><td>项的绝对 url（只读）。 它是项唯一可寻址 URI。  此属性的值在发布操作的输入上被忽略。</td></tr><tr><td>type</td><td>String</td><td>资产的类型（只读）。</td></tr><tr><td>etag</td><td>String</td><td>对应于该版本的项的字符串，其可用于在目录中执行更新项的操作时可用于乐观并发控制。 “*”可用于匹配任何值。</td></tr></table>

### <a name="common-properties"></a>通用属性
这些属性适用于所有根资产类型和所有批注类型。

<table>
<tr><td><b>属性名称</b></td><td><b>数据类型</b></td><td><b>注释</b></td></tr>
<tr><td>fromSourceSystem</td><td>布尔</td><td>指示项的数据是派生自源系统 （如 Sql Server 数据库、Oracle 数据库），还是由用户编写。</td></tr>
</table>

### <a name="common-root-properties"></a>常见根属性
<p>
这些属性适用于所有根资产类型。

<table><tr><td><b>属性名称</b></td><td><b>数据类型</b></td><td><b>注释</b></td></tr><tr><td>名称</td><td>String</td><td>派生自数据源位置信息的名称</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>对数据源进行唯一描述且为资产的标识符之一。 （请参阅双标识部分）。  dsl 的结构因协议和源类型而异。</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>资产类型的详细信息。</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>描述最新注册此资产的用户。  包含用户的唯一 id（upn）和显示名称（lastName 和 firstName）。</td></tr><tr><td>containerId</td><td>String</td><td>数据源容器资产的 Id。 此容器类型不支持此属性。</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>常见非单一批注属性
这些属性适用于所有非单一批注类型（每个资产可允许具有多个这样的批注）。

<table>
<tr><td><b>属性名称</b></td><td><b>数据类型</b></td><td><b>注释</b></td></tr>
<tr><td>key</td><td>String</td><td>特定于用户的密钥（在当前集合中对批注进行唯一标识）。 密钥长度不能超过 256 个字符。</td></tr>
</table>

### <a name="root-asset-types"></a>根资产类型
根资产类型表示可在目录中注册的数据资产的各种类型。 对于每个根类型，都存在一个视图，该视图描述包括在其中的资产和批注。 视图名称可在使用 REST API 发布资产时用于对应 {view_name} url 段。

<table><tr><td><b>资产类型（视图名称）</b></td><td><b>附加属性</b></td><td><b>数据类型</b></td><td><b>允许的批注</b></td><td><b>注释</b></td></tr><tr><td>表</td><td></td><td></td><td>说明<p>FriendlyName<p>标记<p>架构<p>ColumnDescription<p>ColumnTag<p> 专家<p>预览<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>文档<p></td><td>表示任意表格数据的表。  例如：SQL 表、SQL 视图、Analysis Services 表格表、Analysis Services 多维维度、Oracle 表等。   </td></tr><tr><td>度量值（“度量值”）</td><td></td><td></td><td>说明<p>FriendlyName<p>标记<p>专家<p>AccessInstruction<p>文档<p></td><td>此类型表示 Analysis Services 度量值。</td></tr><tr><td></td><td>度量值</td><td>列</td><td></td><td>描述该度量值的元数据</td></tr><tr><td></td><td>isCalculated </td><td>布尔</td><td></td><td>指定是否计算度量值。</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>度量值的物理容器</td></tr><td>KPI（“kpis”）</td><td></td><td></td><td>说明<p>FriendlyName<p>标记<p>专家<p>AccessInstruction<p>文档</td><td></td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>度量值的物理容器</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>返回 KPI 的目标值的 MDX 数值表达式或计算。</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td></td><td>返回 KPI 的实际值的 MDX 数值表达式。</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td></td><td>表示特定时间点 KPI 状态的 MDX 表达式。</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td></td><td>评估一段时间内 KPI 值的 MDX 表达式。 趋势可为任意基于时间的条件，其可用于特定业务上下文中。</td>
<tr><td>报表</td><td></td><td></td><td>说明<p>FriendlyName<p>标记<p>专家<p>AccessInstruction<p>文档<p></td><td>此类型表示 SQL Server Reporting Services 报表 </td></tr><tr><td></td><td>assetCreatedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>String</td><td></td><td></td></tr><tr><td>容器</td><td></td><td></td><td>说明<p>FriendlyName<p>标记<p>专家<p>AccessInstruction<p>文档<p></td><td>此类型表示其他资产的容器，例如 SQL 数据库、Azure Blob 容器或 Analysis Services 模型。</td></tr></table>

### <a name="annotation-types"></a>批注类型
批注类型表示可分配给目录中其他类型的元数据类型。

<table>
<tr><td><b>批注类型（嵌套视图名称）</b></td><td><b>附加属性</b></td><td><b>数据类型</b></td><td><b>注释</b></td></tr>

<tr><td>说明</td><td></td><td></td><td>此属性包含资产的说明。 系统的每个用户都可添加自己的说明。  只允许该用户编辑描述对象。  （管理员和资产所有者可删除描述对象，但无法编辑它）。 系统会单独维护用户的说明。  因此每个资产上都有一个说明的数组：为每个对资产贡献知识的用户提供的数组和一个包含派生自数据源的信息的数组（可能具有）。</td></tr>
<tr><td></td><td>description</td><td>字符串</td><td>资产的简短说明（2-3 行）</td></tr>

<tr><td>标记</td><td></td><td></td><td>此属性定义资产的标记。 系统的每个用户都可为资产添加多个标记。  只有创建了标记对象的用户可以编辑它们。  （管理员和资产所有者可删除标记对象，但无法编辑它）。 系统会单独维护用户的标记。  这样，每个资产都会有一个标记对象的数组。</td></tr>
<tr><td></td><td>标记</td><td>字符串</td><td>描述资产的标记。</td></tr>

<tr><td>FriendlyName（“friendlyName”）</td><td></td><td></td><td>此属性包含资产的友好名称。 FriendlyName 是单一批注 - 仅可向一个资产添加一个 FriendlyName。  只有创建 FriendlyName 对象的用户可以编辑它。 （管理员和资产所有者可删除 FriendlyName 对象，但无法编辑它）。 系统会单独维护用户的友好名称。</td></tr>
<tr><td></td><td>friendlyName</td><td>字符串</td><td>资产的友好名称。</td></tr>

<tr><td>架构</td><td></td><td></td><td>架构描述数据的结构。  它将列出属性（列、属性、字段等）名称、类型以及其他元数据。  此信息全部派生自数据源。  架构是单一批注 - 仅可向一个资产添加一个架构。</td></tr>
<tr><td></td><td>列</td><td>列[]</td><td>列对象的数组。 它们使用派生自数据源的信息对列进行描述。</td></tr>

<tr><td>ColumnDescription（“columnDescriptions”）</td><td></td><td></td><td>此属性包含列的说明。  系统的每个用户都可为多个列添加自己的说明（最多每列一个说明）。 只有创建了 ColumnDescription 对象的用户可以编辑它们。  （管理员和资产所有者可删除 ColumnDescription 对象，但无法编辑它）。 系统会单独维护用户列说明。  因此每个资产上都有一个 ColumnDescription 对象的数组：为每个对列贡献知识的用户提供的数组（每列）和一个包含派生自数据源的信息的数组（可能具有）。  ColumnDescription 松散绑定到架构，因此可不同步。 ColumnDescription 可能描述不再存在于架构的列。  写入器负责让说明和架构同步。  数据源还可能具有列说明信息且它们为将在运行工具时创建的其他 ColumnDescription 对象。</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>此说明指代的列名称。</td></tr>
<tr><td></td><td>description</td><td>String</td><td>列的简短说明（2-3 行）</td></tr>

<tr><td>ColumnTag（“columnTags”）</td><td></td><td></td><td>此属性包含列的标记。 系统的每个用户都可为给定列添加多个标记和为多个列添加标记。 只有创建了ColumnTag 对象的用户可以编辑它们。 （管理员和资产所有者可删除 ColumnTag 对象，但无法编辑它）。 系统会单独维护用户的列标记。  这样，每个资产上都会有 ColumnTag 对象的数组。  ColumnTag 松散绑定到架构，因此可不同步。 ColumnTag 可能描述不存在于架构中的列。  写入器负责让列标记和架构同步。</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>此标记指代的列名称。</td></tr>
<tr><td></td><td>标记</td><td>String</td><td>描述列的标记。</td></tr>

<tr><td>专家</td><td></td><td></td><td>此属性包含被视为数据集方面的专家的用户。 列出说明时，专家的观点（说明）会以气泡形式置于用户体验的顶端。 每个用户都可指定自己的专家。 只允许该用户编辑专家对象。 （管理员和资产所有者可删除专家对象，但无法编辑它）。</td></tr>
<tr><td></td><td>专家</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>预览</td><td></td><td></td><td>预览包含资产前 20 行数据的快照。 预览仅对某些资产类型有意义（对表格有意义，但对度量值无意义）。</td></tr>
<tr><td></td><td>预览</td><td>对象[]</td><td>表示某一列的对象的数组。  每个对象都具有映射到列上的属性和行的该列的值。</td></tr>

<tr><td>AccessInstruction（“accessInstructions”）</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>字符串</td><td>内容的 mime 类型。</td></tr>
<tr><td></td><td>内容</td><td>字符串</td><td>如何获取数据资产访问权限的说明。 内容可以是 URL、电子邮件地址或一组指令。</td></tr>

<tr><td>TableDataProfile（“tableDataProfiles”）</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>数据集中的行数</td></tr>
<tr><td></td><td>size</td><td>long</td><td>以字节为单位的数据集大小。  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>字符串</td><td>最后一次修改架构的时间</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>字符串</td><td>上次修改数据集的时间（已添加，修改或删除数据）</td></tr>

<tr><td>ColumnsDataProfile（“columnsDataProfiles”）</td><td></td><td></td><td></td></tr>
<tr><td></td><td>列</td></td><td>ColumnDataProfile[]</td><td>列数据配置文件的数组。</td></tr>

<tr><td>ColumnDataClassification（“columnDataClassifications”）</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>此分类指代的列名称。</td></tr>
<tr><td></td><td>分类</td><td>String</td><td>此列中数据的分类。</td></tr>

<tr><td>文档</td><td></td><td></td><td>一个给定资产仅能具有与其联系的一个文档。</td></tr>
<tr><td></td><td>mimeType</td><td>字符串</td><td>内容的 mime 类型。</td></tr>
<tr><td></td><td>内容</td><td>字符串</td><td>文档内容。</td></tr>

</table>

### <a name="common-types"></a>常见类型
常见类型可用做属性的类型，但不能用作项的类型。

<table>
<tr><td><b>常见类型</b></td><td><b>属性</b></td><td><b>数据类型</b></td><td><b>注释</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>字符串</td><td>描述数据源的类型。  例如：SQL Server、Oracle 数据库等。  </td></tr>
<tr><td></td><td>objectType</td><td>字符串</td><td>描述数据源中对象的类型。 例如：表、SQL Server 视图。</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>协议</td><td>字符串</td><td>必需。 说明用于与数据源通信的协议。 例如：SQl Server 的 “tds”、Oracle 的“oracle”等。请参阅[数据源引用规范 - DSL 结构](data-catalog-dsr.md)来查看目前支持的协议列表。</td></tr>
<tr><td></td><td>地址</td><td>字典<string, object></td><td>必需。 地址是一组特定于协议的数据，用于识别引用的数据源。 地址数据作用域为特定协议，意味着如果不知道协议，其将无意义。</td></tr>
<tr><td></td><td>authentication</td><td>字符串</td><td>可选。 用于与数据源通信的身份验证方案。 例如：windows、oauth 等。</td></tr>
<tr><td></td><td>connectionProperties</td><td>字典<string, object></td><td>可选。 有关如何连接到数据源的其他信息。</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>后端在发布期间不针对 AAD 对提供的属性执行任何验证。</td></tr>
<tr><td></td><td>upn</td><td>字符串</td><td>用户的唯一电子邮件地址。 如果不提供 objectId 或 在“lastRegisteredBy”属性上下文中必须进行指定，否则为可选。</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>用户或安全组 AAD 标识。 可选。 如果未提供 upn 则必须进行指定，否则为可选。</td></tr>
<tr><td></td><td>firstName</td><td>字符串</td><td>用户的名字（用于显示）。 可选。 仅在“lastRegisteredBy”属性的上下文中有效。 为“角色”、“权限”和“专家”提供安全主体。</td></tr>
<tr><td></td><td>lastName</td><td>字符串</td><td>用户的姓氏（用于显示）。 可选。 仅在“lastRegisteredBy”属性的上下文中有效。 为“角色”、“权限”和“专家”提供安全主体。</td></tr>

<tr><td>列</td><td></td><td></td><td></td></tr>
<tr><td></td><td>名称</td><td>字符串</td><td>列或属性的名称。</td></tr>
<tr><td></td><td>type</td><td>字符串</td><td>列或属性的数据类型。 允许的类型取决于资产的数据源类型。  仅支持类型的子集。</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>列或属性允许的最大长度。 派生自数据源。 仅适用于某些源类型。</td></tr>
<tr><td></td><td>精度</td><td>字节</td><td>列或属性的精度。 派生自数据源。 仅适用于某些源类型。</td></tr>
<tr><td></td><td>isNullable</td><td>布尔</td><td>是否允许列具有 null 值。 派生自数据源。 仅适用于某些源类型。</td></tr>
<tr><td></td><td>表达式</td><td>字符串</td><td>如果值为计算的列，此字段将包括表达该值的表达式。 派生自数据源。 仅适用于某些源类型。</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>字符串</td><td>列的名称</td></tr>
<tr><td></td><td>type </td><td>字符串</td><td>列的类型</td></tr>
<tr><td></td><td>min </td><td>字符串</td><td>数据集中的最小值</td></tr>
<tr><td></td><td>max </td><td>字符串</td><td>数据集中的最大值</td></tr>
<tr><td></td><td>平均值 </td><td>double</td><td>数据集中的平均值</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>数据集的标准偏差</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>数据集中 null 值的计数</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>数据集中非重复值的计数</td></tr>


</table>

## <a name="asset-identity"></a>资产标识
Azure 数据目录使用 DataSourceLocation“dsl”属性的“地址”属性包中的“协议”和标识属性来生成资产的标识（其用于找到目录内资产的地址）。
例如，“tds”协议具有标识属性“服务器”、“数据库”、“架构”和“对象”。 协议和标识属性的组合用于生成 SQL Server 表资产的标识。
Azure 数据目录提供了多个内置数据源协议，其列于[数据源引用规范 - DSL 结构](data-catalog-dsr.md)中。
受支持的协议集可以编程的方式扩展（请参阅数据目录 REST API 参考）。 目录的管理员可注册自定义数据源协议。 下表描述了注册自定义协议的属性。

### <a name="custom-data-source-protocol-specification"></a>自定义数据源协议规范
<table>
<tr><td><b>类型</b></td><td><b>属性</b></td><td><b>数据类型</b></td><td><b>注释</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>命名空间</td><td>字符串</td><td>协议的命名空间。 命名空间的字符长度必须介于 1 到 255 个字符之间，包括由点 (.) 分隔的一个或多个非空部分。 每个部分的长度必须介于 1 到 255 个字符之间，以字母开始且只能包括字母和数字。</td></tr>
<tr><td></td><td>名称</td><td>字符串</td><td>协议的名称。 名称的字符长度必须介于 1 到 255 之间，以字母开始且只能包含字母、数字和短划线 (-) 字符。</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>标识属性列表必须包含至少 1 个且不超过 20 个属性。 例如：“服务器”、“数据库”、“架构”和“对象”为“tds”协议的标识属性。</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>标识集列表。 定义标识属性集，其表示有效资产标识。 必须包含至少 1 个且不超过 20 个集。 例如：{“server”、“数据库”、“架构”和“对象”}是“tds”协议的标识集，其定义 Sql Server 表资产的标识。</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>名称</td><td>字符串</td><td>属性的名称。 名称的长度必须介于 1 到 100 个字符之间，以字母开始且只能包括字母和数字。</td></tr>
<tr><td></td><td>type</td><td>字符串</td><td>属性类型。 支持的值：“bool”、“Boolean”、“字节”、“guid”、“int”、“整数”、“long”、“字符串”、“url”</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>指示使用属性的值时是否应忽略大小写。 只能为具有“字符串”类型的属性指定。 默认值为 false。</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>指示是否应忽略 url 路径每个段的大小写。 只能为具有“url”类型的属性指定。 默认值为 [false]。</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>名称</td><td>字符串</td><td>标识集的名称。</td></tr>
<tr><td></td><td>properties</td><td>string[]</td><td>包括在此标识集的标识属性列表。 它不能包含重复项。 由标识集引用的每个属性必须在协议的“identityProperties”列表中定义。</td></tr>

</table>

## <a name="roles-and-authorization"></a>角色和授权
Microsoft Azure 数据目录为资产和批注上的 CRUD 操作提供授权功能。

## <a name="key-concepts"></a>关键概念
Azure 数据目录使用两个授权机制：

* 基于角色的授权
* 基于权限的授权

### <a name="roles"></a>角色
有三个角色：**管理员**、**所有者**和**参与者**。  每个角色有其作用域和权限，如下表总结所示。

<table><tr><td><b>角色</b></td><td><b>范围</b></td><td><b>权限</b></td></tr><tr><td>管理员</td><td>目录（目录中的所有资产/批注）</td><td>读取、删除、ViewRoles

ChangeOwnership、ChangeVisibility、ViewPermissions</td></tr><tr><td>所有者</td><td>每个资产（根项）</td><td>读取、删除、ViewRoles

ChangeOwnership、ChangeVisibility、ViewPermissions</td></tr><tr><td>参与者</td><td>每个单独的资产和批注</td><td>（读取、更新、删除、ViewRoles）注意：如果项的读取权限从参与者处撤销，则所有权限都将撤销</td></tr></table>

> [!NOTE]
> **读取**、**更新**、**删除**、**ViewRoles** 权限适用于任何项（资产或批注），而**TakeOwnership**、**ChangeOwnership**、**ChangeVisibility** 和 **ViewPermissions** 都只适用于根资产。
> 
> **删除**权限适用于项和任何子项或其下的单个项。 例如，删除资产也会删除资产的所有注释。
> 
> 

### <a name="permissions"></a>权限
权限是访问控制项的列表。 每个访问控制项将权限集分配到安全主体。 权限仅可指定到资产（即根项）并应用于资产和任何子项。

在 **Azure 数据目录** 预览期间，仅**读取**权限在权限列表中受支持以启用方案限制资产的可见性。

默认情况下，经过身份验证的用户具有目录中任意项的**读取**权限，除非可见性仅限于权限中的主体集。

## <a name="rest-api"></a>REST API
**PUT** 和 **POST** 视图项请求可用于控制角色和权限：除项有效负载外，两个系统属性可指定**角色**和**权限**。

> [!NOTE]
> **权限**仅适用于根项。
> 
> **所有者**角色仅适用于根项。
> 
> 默认情况下，在目录中创建项时，其**参与者**会设置为当前经过验证的用户。 如果项可供每个用户更新，则当项首次发布时，**参与者**应在**角色**属性中设置为 &lt;所有人&gt; 特殊安全主体（参见以下示例）。 在项的整个使用寿命期间，都无法更改**参与者**，需将其保持不变（甚至**管理员**或**所有者**都没有权限更改**参与者**。 **参与者**的显示设置唯一的支持值为 &lt;所有人&gt;：**参与者**仅可为创建项的用户或 &lt;所有人&gt;。
> 
> 

### <a name="examples"></a>示例
**发布项时，将参与者设置为 &lt;所有人&gt;。**
特殊安全主体 &lt;所有人&gt; 具有 objectId“00000000-0000-0000-0000-000000000201”。
  **POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> 某些 HTTP 客户端实现可能会自动重新发出请求以响应来自服务器的 302，但通常会从请求中将授权标头剥离。 由于向 Azure 数据目录发出请求需要授权标头，因此必须确保向由 Azure 数据目录指定的重定向位置重新发出请求时提供授权标头。 以下示例代码演示其使用 .NET HttpWebRequest 对象。
> 
> 

**正文**

    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }

  **为现有根项分配所有者和限制可见性**：**PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }

> [!NOTE]
> 在 PUT 中，无需在正文中指定项有效负载：PUT可用于仅更新角色和/或权限。
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png

