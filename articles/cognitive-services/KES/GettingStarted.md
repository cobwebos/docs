---
title: 知识探索服务入门 | Microsoft Docs
description: 使用知识探索服务 (KES) 在 Microsoft 认知服务的学术出版物中创建交互式搜索体验引擎。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 02dc9368eef02d6fa507335ef3171e923412acca
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365771"
---
<a name="getting-started"></a>
# <a name="get-started-with-the-knowledge-exploration-service"></a>知识探索服务入门
在本演练中，使用知识探索服务 (KES) 来创建针对学术出版物的交互式搜索体验引擎。 可以安装命令行工具 [`kes.exe`](CommandLine.md) 以及来自[知识探索服务 SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488) 的所有示例文件。

学术出版物示例包含 Microsoft 研究人员发表的 1000 篇学术论文样本。  每篇论文都与标题、出版年份、作者以及关键词相关联。 每个作者在发布时都通过 ID、姓名和附属关系进行表示。 每个关键字可能与一组同义词相关联（例如，关键字“支持向量机”可能与同义词“svm”相关联）。

<a name="defining-schema"></a>
## <a name="define-the-schema"></a>定义架构
架构描述域中对象的属性结构。 它指定每个属性 JSON 文件格式的名称和数据类型。 以下示例是 Academic.schema 文件的内容。

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

可以在此处将 Title、Year 和 Keyword 分别定义为字符串、整数和字符串属性。 由于作者是由 ID、名称和附属关系表示，因此可以将 Author 定义为具有三个子属性的复合属性：Author.Id、Author.Name 和 Author.Affiliation。

默认情况下，属性支持其数据类型可用的所有操作，包括 equals、starts_with 和 is_between。 由于作者 ID 仅在内部作为标识符使用，因此请重写默认值，并将 equals 指定为唯一索引操作。

对于 Keyword 属性，允许通过在属性定义中指定同义词文件 Keyword.syn 使同义词匹配规范关键字值。 此文件包含规范值对和同义词值对列表：

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

有关架构定义的其他信息，请参阅[架构格式](SchemaFormat.md)。

<a name="generating-data"></a>
## <a name="generate-data"></a>生成数据
数据文件描述要索引的出版物列表，每一行都以 [JSON 格式](http://json.org/)指定论文的属性值。  下面的示例是来自数据文件 Academic.data 的单行，其格式设置具备可读性：

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

在此代码段中，分别将论文的 Title 和 Year 属性指定为 JSON 字符串和数字。 使用 JSON 数组表示多个值。 因为 Author 是复合属性，每个值使用一个由其子属性组成的 JSON 对象来表示。 缺失值的属性，例如本例中的 Keyword，可能会从 JSON 表示形式中排除。

若要区分不同论文的可能性，请通过使用内置的 logprob 属性指定相对对数概率。 如果概率 p 在 0 和 1 之间，则将对数概率作为 log(p) 计算，其中 log() 是自然对数函数。

有关详细信息，请参阅[数据格式](DataFormat.md)。

<a name="building-index"></a>
## <a name="build-a-compressed-binary-index"></a>生成压缩的二进制索引
获得架构文件和数据文件后，可以通过使用 [`kes.exe build_index`](CommandLine.md#build_index-command) 构建数据对象的压缩二进制索引。 在此示例中，从输入架构文件 Academic.schema 和数据文件 Academic.data 生成索引文件 Academic.index。 请使用以下命令：

`kes.exe build_index Academic.schema Academic.data Academic.index`

对于 Azure 之外的快速原型制作，[`kes.exe build_index`](CommandLine.md#build_index-command) 可以从包含多达 10,000 个对象的数据文件构建本地小型索引。 对于更大的数据文件，可以在 [Azure 中的 Windows VM](../../../articles/virtual-machines/windows/quick-create-portal.md) 中运行命令，也可以在 Azure 中执行远程生成。 有关详细信息，请参阅[向上扩展](#scaling-up)。

<a name="authoring-grammar"></a>
## <a name="use-an-xml-grammar-specification"></a>使用 XML 语法规范
语法指定服务可以解释的自然语言查询集，以及这些自然语言查询如何转换为语义查询表达式。 在此示例中，将使用 academic.xml 中指定的语法：

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

有关语法规范语法的详细信息，请参阅[语法格式](GrammarFormat.md)。

<a name="compiling-grammar"></a>
## <a name="compile-the-grammar"></a>编译语法
在获得 XML 语法规范后，可以通过使用 [`kes.exe build_grammar`](CommandLine.md#build_grammar-command) 将其编译为二进制语法。 注意，如果语法导入一个架构，那么该架构文件需要位于与语法 XML 相同的路径中。 在此示例中，从输入 XML 语法文件 Academic.xml 中生成二进制语法文件 Academic.grammar。 请使用以下命令：

`kes.exe build_grammar Academic.xml Academic.grammar`

<a name="hosting-index"></a>
## <a name="host-the-grammar-and-index-in-a-web-service"></a>在 Web 服务中承载语法和索引
对于快速原型制作，可以使用 [`kes.exe host_service`](CommandLine.md#host_service-command) 在本地计算机的 Web 服务中承载语法和索引。 然后，可以通过 [Web API](WebAPI.md) 来访问服务以验证数据的正确性以及语法设计。 在此示例中，将语法文件 Academic.grammar 和索引文件 Academic.index 承载在 http://localhost:8000/ 中。 请使用以下命令：

`kes.exe host_service Academic.grammar Academic.index --port 8000`

这将启动 Web 服务的本地实例。 可以通过从浏览器访问 `http::localhost:<port>` 以交互方式测试服务。 有关详细信息，请参阅[测试服务](#testing-service)。

可以直接调用各种 [Web API](WebAPI.md) 来测试自然语言解释、查询完成情况、结构化的查询评估和直方图计算。 若要停止服务，请向 `kes.exe host_service` 命令提示符输入“quit”或按 Ctrl+C。 下面是一些示例：

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

在 Azure 外部，[`kes.exe host_service`](CommandLine.md#host_service-command) 限制为最多索引 10,000 个对象。 其他限制包括每秒 10 个请求的 API 速率，和在进程自动终止之前的总共 1000 个请求。 若要绕过这些限制，请从 [Azure 中的 Windows VM](../../../articles/virtual-machines/windows/quick-create-portal.md) 运行命令，或通过使用 [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) 命令部署到 Azure 云服务。 有关详细信息，请参阅[部署服务](#deploying-service)。

<a name="scaling-up"></a>
## <a name="scale-up-to-host-larger-indices"></a>向上扩展以承载更大索引
在 Azure 外部运行 `kes.exe` 时，索引限制为 10,000 个对象。 可以通过使用 Azure 生成和承载更大索引。 注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。 或者，如果是 Visual Studio 或 MSDN 订户，则可以[激活订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 每月都将提供一些 Azure 额度。

若要允许 `kes.exe` 访问 Azure 帐户，请从 Azure 门户[下载 Azure 发布设置文件](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)。 如果出现系统提示，则登录到所需的 Azure 帐户。 在 `kes.exe` 运行的工作目录中，将文件另存为 AzurePublishSettings.xml。

有两种方法来生成和承载大型索引。 第一种是在 Azure 的 Windows VM 中准备架构和数据文件。 然后运行 [`kes.exe build_index`](#building-index) 以在 VM 上本地生成索引，而不受任何大小限制。 生成的索引可以本地承载在 VM 上，方法是将 [`kes.exe host_service`](#hosting-service) 用于快速原型制作，同样没有任何限制。 有关详细步骤，请参阅 [Azure VM 教程](../../../articles/virtual-machines/windows/quick-create-portal.md)。

第二种方法是使用带 `--remote` 参数的 [`kes.exe build_index`](CommandLine.md#build_index-command) 远程执行 Azure 生成。 这指定了 Azure VM 大小。 当指定了 `--remote` 参数时，命令会创建一个此大小的临时 Azure VM。 然后，它在 VM 上生成索引，将索引上传到目标 blob 存储器中，并在完成时删除 VM。 在生成索引时，Azure 订阅将收取 VM 费用。

此远程 Azure 生成功能允许在任何环境中运行 [`kes.exe build_index`](CommandLine.md#build_index-command)。 在执行远程生成时，输入架构和数据参数可以是本地文件路径，也可以是 [Azure blob存储](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) URL。 输出索引参数必须是 blob 存储 URL。 若要创建 Azure 存储帐户，请参阅[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)。 若要有效地将文件复制到 blob 存储和从中复制文件，请使用 [AzCopy](../../storage/common/storage-use-azcopy.md) 实用工具。

在本例中，可以假设已经创建了以下 blob 存储容器：http://*account&lt;*&gt;.blob.core.windows.net/&lt;container&gt;/。 它包含架构 Academic.schema、引用的同义词文件 Keywords.syn 和全面的数据文件 Academic.full.data。 可以通过以下命令远程生成完整索引：

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

请注意，预配一个临时 VM 来生成索引可能需要 5-10 分钟。 对于快速原型制作，可以：
- 在任何计算机上本地开发一个较小的数据集。
- 手动[创建 Azure VM](../../../articles/virtual-machines/windows/quick-create-portal.md)，通过远程桌面[连接到它](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)，安装[知识探索服务 SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488)，并在 VM 中运行 [`kes.exe`](CommandLine.md)。

分页会减慢生成过程。 为了避免分页，请使用一个 VM，其大小为 RAM 的三倍，作为生成索引的输入数据文件大小。 使用 RAM 比承载的索引大小大 1 GB 的 VM。 对于可用的 VM 大小列表，请参阅[虚拟机大小](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)。

<a name="deploying-service"></a>
## <a name="deploy-the-service"></a>部署服务
在拥有语法和索引之后，便可将这项服务部署到 Azure 云服务了。 若要创建一个新的 Azure 云服务，请参阅[如何创建和部署云服务](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)。 此时不要指定部署包。  

在创建云服务后，可以使用 [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) 来部署此项服务。 Azure 云服务有两个部署槽位：生产槽和过渡槽。 对于接收实时用户流量的服务，应首先部署到过渡槽。 等待服务启动并初始化服务本身。 然后，可以发送一些请求来验证部署，并验证它是否通过了基本测试。

将过渡槽的内容与生产槽进行[交换](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md)，这样实时流量就会被定向到新部署的服务。 可以在使用新数据部署更新后的服务版本时重复这一过程。 与所有其他 Azure 云服务一样，可以选择使用 Azure 门户来配置[自动缩放](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md)。

在本例中，将学术索引部署到 <vm_size> VM 中现有云服务的过渡槽中。 请使用以下命令：

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

对于可用的 VM 大小列表，请参阅[虚拟机大小](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)。

在部署服务后，可以调用各种 [Web API](WebAPI.md) 来测试自然语言解释、查询完成情况、结构化的查询评估和直方图计算。  

<a name="testing-service"></a>
## <a name="test-the-service"></a>测试服务
要调试实时服务，请从 Web 浏览器浏览主机。 对于通过 [host_service](#hosting-service) 部署的本地服务，请访问 `http://localhost:<port>/`。  对于通过 [deploy_service](#deploying-service) 部署的 Azure 云服务，请访问 `http://<serviceName>.cloudapp.net/`。

该页面包含一个关于基本 API 调用统计信息的链接，以及在该服务中承载的语法和索引。 该页面还包含一个交互式搜索界面，演示了 Web API 的使用。 在搜索框中输入查询，以查看[解释](interpretMethod.md)、[评估](evaluateMethod.md)和 [calchistogram](calchistogramMethod.md) API 调用的结果。 该页面的基础 HTML 源代码也可以作为一个示例，说明如何将 Web API 集成到一个应用中，从而创建丰富的、交互式搜索体验。


