---
title: "使用建议 UI 构建模型 | Microsoft Docs"
description: "Azure 机器学习建议 - 使用建议 UI 构建模型"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: b264fe44-f94e-40ae-9754-60ad7d6cfeb9
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 74cba2d29dec0c302fb969371f7b8fa58e4e4603


---
# <a name="building-a-model-with-the-recommendations-ui"></a>使用建议 UI 构建模型
本文档是一份循序渐进的指南， 旨在引导你完成使用[建议 UI](https://recommendations-portal.azurewebsites.net/) 训练模型要执行的步骤。
通过完成此练习，你可以在以编程方式执行之前了解构建模型的过程。 这还有助于你熟悉此 UI，当你开始使用本服务时此 UI 就会派上用场。

此练习大约需要 30 分钟。

<a name="Step1"></a>

## <a name="step-1---sign-in-to-the-recommendations-ui"></a>步骤 1 — 登录到建议 UI
1. 如果你尚未这么做，则需要[注册](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)新的[建议 API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) 订阅。 在 **Azure** ([http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)) 上注册服务，并使用你的 Azure 帐户登录。 [入门指南](cognitive-services-recommendations-quick-start.md)的*任务 1* 中提供了注册过程的详细说明 
2. 获取建议 API 订阅的**密钥**后，请转到[建议 UI](https://recommendations-portal.azurewebsites.net/)。 
3. 在“**帐户密钥**”字段中输入你的密钥以登录到门户，然后单击“**登录**”按钮。
   
    ![建议 UI：登录对话框。][reco_signin]

<a name="Step2"></a>

## <a name="step-2---lets-gather-some-training-data"></a>步骤 2 — 收集一些训练数据
在你创建版本之前，引擎需要两条信息：一个目录文件和一组使用情况文件。 

目录文件包含要提供给客户的项目的相关信息。 使用情况文件包含有关这些项目的使用方式或者企业中事务的信息。

通常，你会在存储数据库中查询这些信息片段。 今天，我们提供了一些示例数据，以便了解如何使用建议 API。

可以从该网站下载数据：[http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData)。 复制 **Books.Zip** 文件并将其解压缩到本地计算机上的某个文件夹。 例如，**c:\data**。

可以在[收集数据以训练模型](cognitive-services-recommendations-collecting-data.md)一文中找到有关目录和使用情况文件架构的详细信息。

对于此练习，我们将使用一个小文件，使你不必为训练等待太长时间。 如果你想要尝试更加真实的文件，我们还在[相同的位置](http://aka.ms/RecoSampleData)放置了 **MsStoreData.zip**，其中包含来自 Microsoft 应用商店的示例事务。

<a name="Step3"></a>

## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>步骤 3 — 创建一个项目并上载目录和使用情况数据
登录到[建议 UI](https://recommendations-portal.azurewebsites.net/) 时，你会看到“项目”页。 如果你之前已创建了项目，应在此处看到它们。
项目（在 API 参考中也称为*模型*）是目录和使用情况数据的容器。 可以在项目中创建多个*版本*。 我们将通过接下来的步骤引导你完成此过程。

1. 若要创建新项目，请在文本框中键入名称（类似于“MyFirstModel”可以使用），然后单击“**添加项目**”。
   
    ![建议 UI：项目页。][reco_projects]
2. 创建项目后，请单击“**添加目录文件**”部分中的“**浏览文件**”按钮。 
   上载你在步骤 2 中获取的目录。 如果将其保存在 *c:\data*，需要导航到该文件夹。
   
     ![建议 UI：将数据添加到项目。][reco_firstmodel]
3. 上载目录后，请单击“**添加使用情况文件**”部分中的“**浏览文件**”按钮。 添加 usage_large.txt 文件。

> **如果我的使用情况数据文件较大怎么办？**
> 
> 仅可以上载小于 200 MB 的使用情况文件。 也就是说，系统可保留最多 2 GB 的事务数据，如有必要，你可以上载多个文件。
> 你可能不需要这么多数据就能生成一个不错的模型，不仅是数据大小，数据质量也很重要。 使用情况数据中的大多数事务仅与几个常用项目有关，并且“没有其他项的信号”，这是很常见的。
> 
> 

<a name="Step4"></a>

## <a name="step-4---lets-do-some-training"></a>步骤 4 — 进行一些培训！
现在你已上载目录和使用情况数据，我们已经准备好训练引擎，以便它可以了解数据的模式。

1. 单击“**新建生成**”按钮。
2. 选择“**建议**”作为生成类型。 请注意，我们支持排名生成和 FBT（经常一起购买）生成类型。
   
   ![建议 UI：生成对话框。][reco_build_dialog.png]

    FBT 生成可以帮助你识别同一事务中通常购买/使用的产品的模式。
    排名生成用于识别感兴趣的功能。 
    我们在本次研讨会中不会深入讨论 FBT 或排名生成，但如果你感兴趣，应查看[生成类型和模型质量文档页](cognitive-services-recommendations-buildtypes.md)。

1. 单击“**生成**”按钮。 如果你使用了 Books 数据，则生成过程大约需要五分钟。 大型数据集所花的时间更长。

<a name="Step5"></a>

## <a name="step-5---lets-find-out-what-the-machine-learned"></a>步骤 5 — 找出机器学习的内容！
完成生成后，你会注意到生成列表中有新的生成版本。 可以用此生成版本查询项和用户建议。

1. 完成生成后，请单击“**分数**”。 这样一来，你可以试用该模型并查看建议的项目。
   
    ![建议 UI：评分按钮][reco_score_button]
2. 选择一项以查看哪些项是作为该项的建议返回的。 请注意，如果没有足够的事务来预测某个特定项目的建议，则系统不会返回该项目的任何建议。  如果由于某种原因项目未返回建议，请尝试对其他项评分。

<a name="Step6"></a>

## <a name="step-6---next-steps"></a>步骤 6 — 后续步骤
祝贺你！ 你已经训练模型并获取了该模型的建议。  建议 UI 是一个有用的工具，可以让你查看项目和生成版本的状态。 

现在你有一个模型，可能还想要了解如何以编程方式执行上述所有步骤。 若要了解以编程方式调用该 API，你必须查看[建议 API 参考](http://go.microsoft.com/fwlink/?LinkId=759348)并下载 [Recommendations Sample Application](http://go.microsoft.com/fwlink/?LinkID=759344)（建议示例应用程序）。

[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png



<!--HONumber=Nov16_HO3-->


