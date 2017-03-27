---
title: "（已弃用）预测 - ETS + STL - Azure | Microsoft Docs"
description: "（已弃用）预测 - ETS + STL"
services: machine-learning
documentationcenter: 
author: xueshanz
manager: jhubbard
editor: cgronlun
ms.assetid: 153eab4d-6293-45e1-9871-ec339e810dd9
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: yijichen
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: cdf6661a36e38bf7a6fca241682be796712bd5d9


---
# <a name="deprecated-forecasting---ets--stl"></a>（已弃用）预测 - ETS + STL

> [!NOTE]
> 将要停用 Microsoft DataMarket，并且此 API 已弃用。 
> 
> 你可以在 [Cortana Intelligence 库](http://gallery.cortanaintelligence.com)中找到许多有用的示例试验和 API。 有关该库的详细信息，请参阅[共享和发现 Cortana Intelligence 库中的资源](machine-learning-gallery-how-to-use-contribute-publish.md)。

此 [Web 服务](https://datamarket.azure.com/dataset/aml_labs/demand_forecast)实现季节性趋势分解 (STL) 和指数平滑 (ETS) 模型来基于用户提供的历史数据生成预测。 今年对特定产品的需求会增加吗？ 我能预测圣诞季的产品销售额以便我可以有效地规划我的库存吗？ 预测模型倾向于解决此类问题。 基于过去的数据，这些模型可找到隐藏的趋势和季节性，以预测未来趋势。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> 此 Web 服务可供用户使用 - 例如，可能通过移动应用、网站，或者甚至是本地计算机。 但 Web 服务同时也是 Azure 机器学习如何用于在 R 代码顶部创建 Web 服务的示例。 只需几行 R 代码并单击 Azure 机器学习工作室内的某个按钮，就可使用 R 代码创建实验，并作为 Web 服务发布。 Web 服务随后可发布到 Azure 应用商店，并供世界各地的用户和设备使用，无需通过 Web 服务的创作者设置基础结构。  
> 
> 

## <a name="consumption-of-web-service"></a>Web 服务的使用
该服务接受 4 个参数并计算预测。
输入参数为：

* 频率 - 指示原始数据的频率（每日/每周/每月/每季度/每年）。
* 水平 - 未来预测时间段。
* 日期 - 为时间添加新的时间系列数据。
* 值 - 添加新的时间系列数据值。

该服务的输出是计算所得的预测值。

示例输入可以是： 

* 频率 - 12
* 水平 - 12
* 日期 - 1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012; 1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013; 1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* 值 - 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511; 3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

> 托管在 Azure 应用商店上的此服务是 OData 服务；这些可能会通过 POST 或 GET 方法进行调用。 
> 
> 

有多种方法可以自动方式使用该服务（[此处](http://microsoftazuremachinelearning.azurewebsites.net/StlEtsForecasting.aspx)为示例应用）。

### <a name="starting-c-code-for-web-service-consumption"></a>为 Web 服务使用启动 C# 代码：
    public class Input
    {
            public string frequency;
            public string horizon;
            public string date;
            public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };         var json = JsonConvert.SerializeObject(input);
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

从 Azure 机器学习中创建了新的空白实验。 示例输入数据已随预定义数据架构一起上载。 链接到数据架构是一种[执行 R 脚本][execute-r-script]模块，该模块通过使用 R 中的“stl”、“ets”和“forecast”函数来生成 STL 和 ETS 预测模型。 

### <a name="experiment-flow"></a>实验流：
![实验流][2]

#### <a name="module-1"></a>模块 1：
    # Add in the CSV file with the data in the format shown below 
![样本数据][3]    

#### <a name="module-2"></a>模块 2：
    # Data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)

    # Preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]

    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)

    # Fit a time series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- stl(train_ts,  s.window="periodic")
    train_model <- forecast(fit1, h = data$horizon, method = 'ets')
    plot(train_model)

    # Produce forcasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")

    # Data output
    maml.mapOutputPort("data.forecast");

## <a name="limitations"></a>限制
这是一个非常简单的 ETS + STL 预测示例。 从上面的代码示例中可看出，没有进行错误捕获，并且该服务假定所有变量都是连续/正值，频率应为大于 1 的整数。 日期和值向量的长度应该相同，而时间序列的长度应大于 2 倍的频率。 日期变量应遵循格式 mm/dd/yyyy。

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到 Azure 应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img1.png
[2]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img2.png
[3]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




<!--HONumber=Jan17_HO2-->


