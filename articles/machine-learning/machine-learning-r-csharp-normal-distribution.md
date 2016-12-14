---
title: "正态分布 Web 服务套件 | Microsoft Docs"
description: "正态分布 Web 服务套件"
services: machine-learning
documentationcenter: 
author: ireiter
manager: jhubbard
editor: cgronlun
ms.assetid: aab7b92e-953b-43d8-b0af-031394406bfe
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: ireiter
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e3783df1f80fc6453d64c16f5002262fb7197309


---
# <a name="normal-distribution-suite"></a>正态分布套件
正态分布套件是一组示例 Web 服务（[生成器](https://datamarket.azure.com/dataset/aml_labs/ndg7)、[分位计算器](https://datamarket.azure.com/dataset/aml_labs/ndq5)、[概率计算器](https://datamarket.azure.com/dataset/aml_labs/ndp5)），有助于生成和处理正态分布。 这些服务允许生成任意长度的正态分布序列，计算来自给定概率的分位以及计算给定分位的概率。 每个服务都会根据所选服务发出不同输出（请参见下面的说明）。 正态分布套件基于 R 函数 qnorm、rnorm 和 pnorm，这些函数包含在 R 统计包中。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> 此 Web 服务可供用户使用 - 例如，可能通过移动应用、网站，或者甚至是本地计算机。 但 Web 服务同时也是 Azure 机器学习如何用于在 R 代码顶部创建 Web 服务的示例。 只需几行 R 代码并单击 Azure 机器学习工作室内的某个按钮，就可使用 R 代码创建实验，并作为 Web 服务发布。 Web 服务随后可发布到 Azure 应用商店，并供世界各地的用户和设备使用，无需通过 Web 服务的创作者设置基础结构。  
> 
> 

## <a name="consumption-of-web-service"></a>Web 服务的使用
正态分布套件包括以下 3 项服务。

### <a name="normal-distribution-quantile-calculator"></a>正态分布分位计算器
该服务接受普通分布的 4 个参数，并计算关联的分位。

输入参数为：

* p - 具有正态分布的事件的单个概率。 
* Mean - 正态分布平均值。
* SD - 正态分布标准差。 
* Side - L 代表较低端的分布，U 代表较高端的分布。

该服务的输出是与给定概率关联的计算所得分位。

### <a name="normal-distribution-probability-calculator"></a>正态分布概率计算器
该服务接受普通分布的 4 个参数，并计算关联的概率。

输入参数为：

* q - 具有正态分布的事件的单个分位。 
* Mean - 正态分布平均值。
* SD - 正态分布标准差。 
* Side - L 代表较低端的分布，U 代表较高端的分布。

该服务的输出是与给定分位关联的计算所得概率。

### <a name="normal-distribution-generator"></a>正态分布生成器
该服务接受正态分布的 3 个参数，并生成正态分布的随机数序列。 以下参数应在请求中提供给它：

* n - 观测次数。 
* mean - 正态分布平均值。
* sd - 正态分布标准差。 

该服务的输出是长度 n 序列和一个基于 mean 和 sd 参数的正态分布。

> 托管在 Azure 应用商店上的此服务是 OData 服务；这些可能会通过 POST 或 GET 方法进行调用。 
> 
> 

有多种方法可以自动方式使用该服务（示例应用如下：[生成器](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx)、[概率计算器](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx)、[分位计算器](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)）。

### <a name="starting-c-code-for-web-service-consumption"></a>为 Web 服务使用启动 C# 代码：
### <a name="normal-distribution-quantile-calculator"></a>正态分布分位计算器
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


### <a name="normal-distribution-probability-calculator"></a>正态分布概率计算器
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

### <a name="normal-distribution-generator"></a>正态分布生成器
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
> 此 Web 服务是使用 Azure 机器学习创建的。 有关免费试用版，以及关于创建实验和[发布 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)的介绍性视频，请参阅 [azure.com/ml](http://azure.com/ml)。 
> 
> 

下面是创建 Web 服务的实验的屏幕快照，以及实验内每个模块的示例代码。

### <a name="normal-distribution-quantile-calculator"></a>正态分布分位计算器
实验流：

![实验流][2]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### <a name="normal-distribution-probability-calculator"></a>正态分布概率计算器
实验流：

![实验流][3]

     #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### <a name="normal-distribution-generator"></a>正态分布生成器
实验流：

![实验流][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

## <a name="limitations"></a>限制
这些是关于正态分布的一些非常简单的示例。 从上面的示例代码中可看出，进行了很小的错误捕获。

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到 Azure 应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png




<!--HONumber=Nov16_HO3-->


