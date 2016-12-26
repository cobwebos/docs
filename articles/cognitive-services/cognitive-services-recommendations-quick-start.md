---
title: "快速入门指南：机器学习建议 API | Microsoft Docs"
description: "Azure 机器学习建议 - 快速入门指南"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 7f90fe5e-c0bf-4c16-9624-49118adf9069
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 2acb7a96b04ba457fefe811765b17ee869de52bc
ms.openlocfilehash: 86d297fd902eb1eb8af159bc684ed119feef2b30


---
# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>认知服务建议 API 快速入门指南
本文档介绍如何登记你的服务或应用程序，以使用[建议 API](http://go.microsoft.com/fwlink/?LinkId=759710)。
可以在[此处](http://go.microsoft.com/fwlink/?LinkId=759709)找到有关建议 API 和其他认知服务的更多详细信息。 在学习本指南的过程中，你也可能会发现 [建议 API 参考](http://go.microsoft.com/fwlink/?LinkId=759348)和[建议 UI 快速入门指南](cognitive-services-recommendations-ui-intro.md)非常有用。

<a name="Overview"></a>

### <a name="general-overview"></a>一般概述
本文档是一份循序渐进的指南， 旨在引导你完成训练模型所要执行的步骤，并提供一些资源的链接，以帮助你在生产环境中使用该模型。

本练习耗时大约 30 分钟。

若要使用[建议 API](http://go.microsoft.com/fwlink/?LinkId=759710)，需要执行以下步骤：

1. 创建模型 - 模型是使用情况数据、目录数据和建议模型的容器。
2. 导入目录数据 - 目录包含有关项目的元数据信息。
3. 导入使用情况数据 - 可以使用 2 种方法中的一种或两种来上载使用情况数据：
   * 上载包含使用情况数据的文件。
   * 发送数据获取事件。
     通常，上载使用情况文件的目的是创建初始建议模型（启动），并一直使用此模型，直到系统收集了采用数据获取格式的足够数据为止。
4. 构建建议模型 - 这是一个异步操作，在此过程中，建议系统将收集所有使用情况数据并创建建议模型。 根据数据大小和版本配置参数，此操作可能需要几分钟或几小时。 当触发构建时，你将获取一个版本 ID。 在开始使用建议之前，可以使用此 ID 来查看构建过程何时结束。
5. 使用建议 - 获取针对特定项目或项目列表的建议。

<a name="GetStarted"></a>

### <a name="lets-get-started"></a>让我们开始吧！
首先构建一个建议模型。 然后我们将引导你使用模型生成的结果针对某个电子商务站点提供建议。

<a name="Ex1Task1"></a>

#### <a name="task-1---signing-up-for-the-recommendations-api"></a>任务 1 - 注册建议 API
在此任务中，你将注册建议 API 服务并创建建议模型。

1. 转到 **Azure 门户** ([http://portal.azure.com/](http://portal.azure.com/)) 并使用你的 Azure 帐户登录。
2. 单击“**+ 新建**”。
3. 选择“**智能**”选项。
4. 选择“**认知服务 API**”产品。
   本产品使你能够启动任一认知服务 API（面部、文本分析、计算机影像等）的订阅。 今天我们将重点放在建议 API 上。
5. 在认知服务 API 登录页面上，输入建议订阅的**帐户名**。 （例如“MyRecommendations”）。 此名称不应包含任何空格。
6. 在“**API 类型**”中，选择“**建议**”。
7. 在“**定价层**”中，可以选择一个计划。 你可以选择**免费**层，每月可以免费完成 10,000 笔交易。 这是一个免费计划，因此很适合用于开始试用此系统。 转到生产环境后，我们建议你根据请求数量，相应地更改计划类型。 （请注意：一次只能获得一个免费层订阅）
8. 选择“**资源组**”；如果你还没有资源组，请新建一个。
9. 你可以更改“创建”对话框中的其他元素。 应该注意的是，目前仅支持美国数据中心的资源提供程序。
10. 完成选择后，请单击“**创建**”。
11. 等待几分钟让资源部署完成。
    部署后，你可以转到“**设置**”边栏选项卡中的“**密钥**”部分，其中显示了使用 API 时所需的主密钥和辅助密钥。  请复制主密钥，因为在创建第一个模型时需要用到它。

<a name="Ex1Task2"></a>

#### <a name="task-2---did-you-bring-your-data"></a>任务 2 - 是否已准备好数据？
建议 API 将从你的目录和交易中学习，以便提供合理的产品建议。 这意味着，你需要向它馈送有关产品的适当数据（称为**目录**文件）和一个交易集，该数据集应该足够大，使它能够找到相关的使用模式（称为**使用情况**）。

1. 通常，你会在交易数据库中查询这些信息片段。
   如果你未准备好这些数据，我们会根据 Microsoft 应用商店交易数据为你提供一些示例数据。
   
   可以从[此处](http://aka.ms/RecoSampleData)下载数据。 复制 MsStoreData.Zip 并将其解压缩到本地计算机上的某个文件夹。
   
   > [!NOTE]
   > 将在任务 3 中下载并运行的示例代码中已经嵌入了示例数据，因此，此任务是可选的。  尽管如此，此任务可让你下载更真实的数据集，并让你更好地了解建议 API 的输入。
   > 
   > 
2. 现在让我们看一下目录文件。 导航到复制数据的位置。
   在**记事本**中打开目录文件。
   
   你会发现，目录文件相当简单。 其格式为 `<itemid>,<item name>,<product category>`
   
   > AAA-04294,OfficeLangPack 2013 32/64 E34 Online DwnLd,Office <br>
   > AAA-04303,OfficeLangPack 2013 32/64 ET Online DwnLd,Office  <br>
   > C9F-00168,KRUSELL Kiruna Flip Cover for Nokia Lumia 635 - Camel,Accessories
   > 
   > 
   
   我们有必要指出的是，目录文件的内容可能更丰富，例如，可以在其中添加有关产品的元数据（称为*项目特征*）。 有关目录格式的更多详细信息，请参阅“API Reference”（API 参考）中的 [catalog format](http://go.microsoft.com/fwlink/?LinkID=760716)（目录格式）部分。
3. 同样，让我们了解使用情况数据。 你会发现，使用情况数据的格式为 `<User Id>,<Item Id>,<Time Stamp>,<Event>`。
   
   > 00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase 0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase 0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase 00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase 0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase 00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase
   > 
   > 

请注意，前三个元素是必需的。 事件类型是可选的。 有关此主题的详细信息，请参阅 [usage format](http://go.microsoft.com/fwlink/?LinkID=760712)（使用情况数据的格式）。

> **需要多少数据？**
> 
> <p>
> 事实上，这取决于使用情况数据本身。 系统将在用户购买不同项目时进行学习。 对于 FBT 等某些版本，必须知道在相同的交易中购买了哪些项目。 （我们称之为*共同项目*）。 一个良好的经验法则是让大多数项目出现在 20 个或更多的交易中，因此，如果你的目录中有 10,000 个项目，则我们建议提供的交易数是该交易数的 20 倍，即大约 200,000 个交易。
> 再次强调，这只是一条经验法则。 你需要使用你的数据进行试验。
> </p>
> 
> 

<a name="Ex1Task3"></a>

#### <a name="task-3---creating-a-recommendations-model"></a>任务 3 - 创建建议模型
现在你已准备好帐户和数据，接下来让我们创建第一个模型。

在此任务中，你将使用示例应用程序来构建第一个模型。

1. 首先应该查看 [Recommendations API Reference](http://go.microsoft.com/fwlink/?LinkId=759348)（建议 API 参考）。
2. 将[示例应用程序](http://go.microsoft.com/fwlink/?LinkID=759344)下载到本地文件夹。
3. 在 Visual Studio 中打开位于 **C#** 文件夹中的 **RecommendationsSample.sln** 解决方案。
4. 打开 **SampleApp.cs** 文件。 请注意该文件中的以下步骤：
   
   * 创建模型
   * 添加目录文件
   * 添加使用情况文件
   * 触发模型生成
   * 基于一对项目来获取建议
     <p></p>
5. 将 **AccountKey** 字段的值替换为任务 1 中的密钥。
6. 逐步执行该解决方案，你将体会到模型的创建方式。
7. 尝试替换刚刚下载的目录和使用情况文件，以便为 Microsoft 应用商店或书籍建议创建新模型。 你还需要更改模型名称，以及要为其请求建议的项目。
8. 创建模型时，请记下**模型 ID**，因为在生产环境中请求建议时需要用到它。

> 在[此处](cognitive-services-recommendations-buildtypes.md)详细了解生成类型，以及如何评估生成质量。
> 

<a name="Ex1Task4"></a>

### <a name="putting-your-model-in-production"></a>将模型投放到生产环境中！
现在你了解如何创建模型和使用建议，下一步是在网站或移动应用程序中使它投入生产，或者将它集成到 CRM 或 ERP 系统。
显然，其中的每种实施都不相同。 由于建议 API 是以 Web 服务形式请求的，因此，你应该能够轻松地从其中的任一不同环境调用它。

>  [!IMPORTANT]
>  如果想要从公共客户端（例如电子商务站点）显示建议，应创建一个代理服务器来提供建议。 这一点很重要，因为这可以避免向外部（可能不受信任的）实体公开你的 API 密钥。

下面提供了有关可在哪些位置使用“建议”的思路：

### <a name="product-page"></a>产品页
**项目建议**

<p>如果已使用购买数据对模型进行训练，它可以让你的客户*发现很可能让已购买过原始项目的人感兴趣的产品*。</p>
<p>如果已使用点击数据对模型进行训练，它可以让你的客户*发现很可能让已访问过原始项目的人访问的产品*。 这种类型的模型可能返回类似的项目。</p>

**人气组合建议**

<p>人气组合版本是可以训练的，因此，你可以获取可望与此项目一起购买的项目集。</p>

### <a name="check-out-page"></a>结帐页
**项目建议**

<p>建议模型可以使用项目列表作为输入。 因此你可以传递购物篮中的所有项目作为输入来获取建议。
在此情况下，模型将提供与购物篮中所有项目特别相关的建议。
</p>

### <a name="landing-page"></a>登录页
**用户建议**

<p>
建议模型可以使用用户 ID 作为输入。  这将使用该用户的交易历史记录，为指定的用户提供个性化建议。
</p>

请参阅 [Get Item Recommendations](http://go.microsoft.com/fwlink/?LinkID=760719)（获取项目建议）文档。

<a name="Ex1Task6"></a>

### <a name="whats-next"></a>后续步骤
祝贺你顺利完成本教程！ 若要了解详细信息，可以访问完整的 [Recommendations API Reference](http://go.microsoft.com/fwlink/?LinkId=759348)（建议 API 参考）。如有疑问，请联系我们：mlapi@microsoft.com




<!--HONumber=Nov16_HO5-->


