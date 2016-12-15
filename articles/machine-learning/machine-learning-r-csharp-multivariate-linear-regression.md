---
title: "多变量线性回归 | Microsoft Docs"
description: "多变量线性回归"
services: machine-learning
documentationcenter: 
author: jaymathe
manager: jhubbard
editor: cgronlun
ms.assetid: 2fb78220-ced9-4564-a439-9e5df6772994
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jaymathe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 01d90591f61920769dbdca360f69156281145517


---
# <a name="multivariate-linear-regression"></a>多变量线性回归
假设拥有一个数据集，并希望根据独立变量快速预测每个个体 (i) 的相关变量 (y)。 线性回归是一种常用的统计方法，用于此类预测。 此处因变量 y 假定为连续值。  

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

简单的方案可能是研究人员尝试基于个体身高 (x) 预测个体 (y) 的体重。 更高级的方案可能是研究人员拥有个体的其他信息（如体重、性别、种族）并尝试预测个体的体重。 此 [Web 服务](https://datamarket.azure.com/dataset/aml_labs/multivariate_regression)适用于数据的线性回归模型，并输出数据中每个观测值的预测值 (y)。

> 此 Web 服务可供用户使用 - 例如，可能通过移动应用、网站，或者甚至是本地计算机。 但 Web 服务同时也是 Azure 机器学习如何用于在 R 代码顶部创建 Web 服务的示例。 只需几行 R 代码并单击 Azure 机器学习工作室内的某个按钮，就可使用 R 代码创建实验，并作为 Web 服务发布。 Web 服务随后可发布到 Azure 应用商店，并供世界各地的用户和设备使用，无需通过 Web 服务的创作者设置基础结构。  
> 
> 

## <a name="consumption-of-web-service"></a>Web 服务的使用
此 Web 服务为所有观测值提供了基于独立变量的相关变量的预测值。 Web 服务需要最终用户以字符串的形式输入数据，其中以逗号 (,) 分隔行，以分号 (;) 分隔列。 Web 服务每次需要 1 个行，并且第一列要是相关变量。 示例数据集可如下所示：

![样本数据][1]

对于 y，不具有相关变量的观测值应输入为“NA”。 上述数据集的数据输入将是以下字符串：“10;5;2,18;1;6,6;5.3;2.1,7;5;5,22;3;4,12;2;1,NA;3;4”。 输出是基于独立变量的每行的预测值。 

> 托管在 Azure 应用商店上的此服务是 OData 服务；这些可能会通过 POST 或 GET 方法进行调用。 
> 
> 

有多种方法可以自动方式使用该服务（[此处](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx)为示例应用）。

### <a name="starting-c-code-for-web-service-consumption"></a>为 Web 服务使用启动 C# 代码：
    public class Input
    {
            public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
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

在 Azure 机器学习内，创建了新的空白实验，并将两个[执行 R 脚本][execute-r-script] 模块提取到工作区。 此 Web 服务使用基础 R 脚本运行 Azure 机器学习实验。 此实验有 2 个部分：架构定义和训练模型 + 评分。 第一个模块定义输入数据集的预期结构，其中第一个变量为相关变量，其他变量为独立变量。 第二个模块适用于输入数据的泛型线性回归模型。  

![实验流][3]

#### <a name="module-1"></a>模块 1：
#### <a name="schema-definition"></a>架构定义
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

#### <a name="module-2"></a>模块 2：
#### <a name="lm-modeling"></a>LM 建模
    data <- maml.mapInputPort(1) # class: data.frame  

    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  

## <a name="limitations"></a>限制
这是关于多个线性回归 Web 服务的一个非常简单的示例。 从上述的示例代码中可看出，未实现错误捕获，服务会假定所有内容都是连续变量（不允许分类功能），因为在创建此 Web 服务过程中，该服务只输入数字值。 此外，由于 Web 服务调用的请求/响应性质，以及每次调用 Web 服务时模型才适用，所以当前该服务只能处理有限的数据大小。 

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到 Azure 应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




<!--HONumber=Nov16_HO3-->


