---
title: "比例测试的差异 | Microsoft Docs"
description: "比例测试的差异"
services: machine-learning
documentationcenter: 
author: aniedea
manager: jhubbard
editor: cgronlun
ms.assetid: 9356b821-5345-44f6-8e26-719f2dea5e6d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: aniedea
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b45b4689e6254afd603d434dc0b21dad90addd95


---
# <a name="difference-in-proportions-test"></a>比例测试的差异
两个比例是否有统计上的差异？ 假设用户想要比较两部电影，以便确定其中一部电影相较于另一部电影而言“赞”的比例是否明显较高。 如果使用大型样本，在 0.50 和 0.51 比例之间可能会有统计上的显著差异。 如果使用小型样本，可能没有足够的数据可用来确定这些比例是否确实存在差异。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

此 [Web 服务](https://datamarket.azure.com/dataset/aml_labs/prop_test)会根据用户针对 2 个比较组输入的成功次数和试验总数，进行两个比例的差异假设测试。 在某一可能的方案中，从电影比较应用内调用此 Web 服务，根据电影分级让用户知道其中一部电影是否比另一部电影确实更“受欢迎”。

> 此 Web 服务可供用户使用 - 例如，可能通过移动应用、网站，或者甚至是本地计算机。 但 Web 服务同时也是 Azure 机器学习如何用于在 R 代码顶部创建 Web 服务的示例。 只需几行 R 代码并单击 Azure 机器学习工作室内的某个按钮，就可使用 R 代码创建实验，并作为 Web 服务发布。 Web 服务随后可发布到 Azure 应用商店，并供世界各地的用户和设备使用，无需通过 Web 服务的创作者设置基础结构。
> 
> 

## <a name="consumption-of-web-service"></a>Web 服务的使用
此服务接受 4 个参数并执行比例的假设测试。

输入参数为：

* Successes1 - 样本 1 中的成功事件数。
* Successes2 - 样本 2 中的成功事件数。
* Total1 - 样本 1 的大小。
* Total2 - 样本 2 的大小。

服务的输出是假设测试的结果，以及卡方统计、df、p 值及样本 1/2 中的比例和置信区间界限。

> 托管在 Azure 应用商店上的此服务是 OData 服务；这些可能会通过 POST 或 GET 方法进行调用。 
> 
> 

有多种方法可以自动方式使用该服务（[此处](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx)为示例应用）。

### <a name="starting-c-code-for-web-service-consumption"></a>为 Web 服务使用启动 C# 代码：
    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


## <a name="creation-of-web-service"></a>Web 服务的创建
> 此 Web 服务是使用 Azure 机器学习创建的。 有关免费试用版，以及关于创建实验和[发布 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)的介绍性视频，请参阅 [azure.com/ml](http://azure.com/ml)。 下面是创建 Web 服务的实验的屏幕快照，以及实验内每个模块的示例代码。
> 
> 

在 Azure 机器学习内，已使用两个[执行 R 脚本][execute-r-script]模块创建新的空白实验。 在第一个模块中，数据架构已定义，同时第二个模块使用 R 内的 prop.test 命令执行 2 个比例的假设测试。 

### <a name="experiment-flow"></a>实验流：
![实验流][2]

#### <a name="module-1"></a>模块 1：
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port


#### <a name="module-2"></a>模块 2：
    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port


## <a name="limitations"></a>限制
这是一个非常简单的示例，用于测试 2 个比例的差异。 从上面的代码示例中可看出，未实施任何错误捕获，并且该服务假定所有变量都是连续的。

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到 Azure 应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




<!--HONumber=Dec16_HO2-->


