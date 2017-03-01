---
title: "（已弃用）群集模型 - Azure | Microsoft Docs"
description: "（已弃用）群集模型"
services: machine-learning
documentationcenter: 
author: FrancescaLazzeri
manager: jhubbard
editor: cgronlun
ms.assetid: 51b8d012-ed44-4312-920c-9c808dfd4ff6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: lazzeri
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: 84c94b83878f851c2ac281fe33afe53e02f8456c


---
# <a name="deprecated-cluster-model"></a>（已弃用）群集模型

> [!NOTE]
> 将要停用 Microsoft DataMarket，并且此 API 已弃用。 
> 
> 你可以在 [Cortana Intelligence 库](http://gallery.cortanaintelligence.com)中找到许多有用的示例试验和 API。 有关该库的详细信息，请参阅[共享和发现 Cortana Intelligence 库中的资源](machine-learning-gallery-how-to-use-contribute-publish.md)。

我们如何预测信用卡持有人的行为组，以降低信用卡发卡机构的坏帐风险？ 我们如何定义员工的人格特征组，以提高其工作效率？ 医生如何根据患者的疾病特征将患者分组？ 原则上，所有这些问题都可以通过群集分析得到解答。   

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

群集分析基于变量组合将一组观察值分类为两个或多个未知互斥组。 群集分析的目的是发现一个系统，将观察值（通常为用户或其特征）编成组，其中的组成员共享共有属性。 此[服务](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model)使用 K-Means 方法（一种常用的群集技术），将任意数据聚集成组。 此 Web 服务将数据和 k 个群集用作输入，并生成各个观察值属于 K 个组中的哪一个组的预测。 

> 此 Web 服务可供用户使用 - 例如，可能通过移动应用、网站，或者甚至是本地计算机。 但 Web 服务同时也是 Azure 机器学习如何用于在 R 代码顶部创建 Web 服务的示例。 只需几行 R 代码并单击 Azure 机器学习工作室内的某个按钮，就可使用 R 代码创建实验，并作为 Web 服务发布。 Web 服务随后可发布到 Azure 应用商店，并供世界各地的用户和设备使用，无需通过 Web 服务的创作者设置基础结构。  
> 
> 

## <a name="consumption-of-web-service"></a>Web 服务的使用
此 Web 服务将数据分组为一组 k 个组，并输出每行的组分配。 Web 服务需要最终用户以字符串的形式输入数据，其中以逗号 (,) 分隔行，以分号 (;) 分隔列。 该 Web 服务要求一次 1 行。 示例数据集可如下所示：

![样本数据][1]

假设用户想要将此数据分成 3 个互斥组。 上述数据集的数据输入将如下所示：value = “10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4”; k=”3”。 输出是每行的预测组成员身份。

> 托管在 Azure 应用商店上的此服务是 OData 服务；这些可能会通过 POST 或 GET 方法进行调用。 
> 
> 

有多种方法可以自动方式使用该服务（[此处](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx)为示例应用）。

### <a name="starting-c-code-for-web-service-consumption"></a>为 Web 服务使用启动 C# 代码：
    public class Input
    {
            public string value;
            public string k;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

在 Azure 机器学习中，已创建新的空白实验，并将两个[执行 R 脚本][execute-r-script]模块提取到工作区。 已使用简单的[执行 R 脚本][execute-r-script]创建数据架构。 然后，将数据架构链接到群集模型部分，再次使用[执行 R 脚本][execute-r-script]创建此数据架构。 在用于群集模型的[执行 R 脚本][execute-r-script]中，Web 服务利用“k-means”函数，该函数已预生成到 Azure 机器学习的[执行 R 脚本][execute-r-script]。    

![实验流][3]

#### <a name="module-1"></a>模块 1：
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)

    maml.mapOutputPort("mydata");     


#### <a name="module-2"></a>模块 2：
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");


## <a name="limitations"></a>限制
这是聚集 Web 服务的一个非常简单的示例。 从上述的示例代码中可看出，未实现错误捕获，服务会假定所有内容都是连续变量（不允许分类功能），因为在创建此 Web 服务过程中，该服务只输入数字值。 此外，由于 Web 服务调用的请求/响应性质，以及每次调用 Web 服务时模型才适用，所以当前该服务只能处理有限的数据大小。 

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到 Azure 应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




<!--HONumber=Jan17_HO2-->


