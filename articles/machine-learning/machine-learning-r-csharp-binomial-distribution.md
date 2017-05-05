---

ROBOTS: NOINDEX, NOFOLLOW
redirect_url: https://gallery.cortanaintelligence.com/
translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: d719d01ae1b3f748d3f3ebc5587f9f0f969dad21
ms.lasthandoff: 05/03/2017


---
# <a name="deprecated-binomial-distribution-suite"></a>（已弃用）二项式分布套件

> [!NOTE]
> 将要停用 Microsoft DataMarket，并且此 API 已弃用。 
> 
> 你可以在 [Cortana Intelligence 库](http://gallery.cortanaintelligence.com)中找到许多有用的示例试验和 API。 有关该库的详细信息，请参阅[共享和发现 Cortana Intelligence 库中的资源](machine-learning-gallery-how-to-use-contribute-publish.md)。

二项式分布套件是一组示例 Web 服务（[二项式生成器](https://datamarket.azure.com/dataset/aml_labs/bdg5)、[概率计算器](https://datamarket.azure.com/dataset/aml_labs/bdp4)、[分位计算器](https://datamarket.azure.com/dataset/aml_labs/bdq5)），有助于生成和处理二项式分布。 这些服务允许生成任意长度的二项式分布序列，计算超出给定概率的分位以及计算给定分位的概率。 每个服务都会根据所选服务发出不同输出（请参见下面的说明）。 二项式分布套件基于 R 函数 qbinom、rbinom 和 pbinom，这些函数包含在 R 统计包中。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> 这些 Web 服务可供用户使用 - 例如，可能通过移动应用、网站甚至是本地计算机直接在应用商店使用。 但 Web 服务同时也是 Azure 机器学习如何用于在 R 代码顶部创建 Web 服务的示例。 只需几行 R 代码并单击 Azure 机器学习工作室内的某个按钮，就可使用 R 代码创建实验，并作为 Web 服务发布。 Web 服务随后可发布到 Azure 应用商店，并供世界各地的用户和设备使用，无需通过 Web 服务的创作者设置基础结构。
> 
> 

## <a name="consumption-of-web-service"></a>Web 服务的使用
二项式分布套件包括以下 3 项服务。

### <a name="binomial-distribution-quantile-calculator"></a>二项式分布分位计算器
该服务接受普通分布的 4 个参数，并计算关联的分位。
输入参数为：

* p - 多次实验的单个聚合概率。  
* size - 实验次数。
* prob - 实验成功的概率。
* Side - L 代表较低端的分布，U 代表较高端的分布。 

该服务的输出是与给定概率关联的计算所得分位。

### <a name="binomial-distribution-probability-calculator"></a>二项式分布概率计算器
该服务接受二项式分布的 4 个参数，并计算关联的概率。
输入参数为：

* q - 具有二项式分布的事件的单个分位。 
* size - 实验次数。
* prob - 实验成功的概率。
* Side - L 代表较低端的分布，U 代表较高端的分布，或者，E 等于成功的单个数字。

该服务的输出是与给定分位关联的计算所得概率。

### <a name="binomial-distribution-generator"></a>二项式分布生成器
该服务接受二项式分布的 3 个参数，并生成二项式分布的随机数序列。 以下参数应在请求中提供给它：

* n - 观测次数。 
* size - 实验次数。
* prob - 成功的概率。

该服务的输出是长度 n 序列和一个基于 size 和 prob 参数的二项式分布。

> 托管在 Azure 应用商店上的此服务是 OData 服务；这些可能会通过 POST 或 GET 方法进行调用。 
> 
> 

有多种方法可以自动方式使用该服务（示例应用如下：[生成器](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx)、[概率计算器](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx)、[分位计算器](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)）。 

### <a name="starting-c-code-for-web-service-consumption"></a>为 Web 服务使用启动 C# 代码：
### <a name="binomial-distribution-quantile-calculator"></a>二项式分布分位计算器
    public class Input
    {
            public string p;
            public string size;
            public string prob;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void main()
    {
            var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

### <a name="binomial-distribution-probability-calculator"></a>二项式分布概率计算器
    public class Input
    {
            public string q;
            public string size;
            public string prob;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


### <a name="binomial-distribution-generator"></a>二项式分布生成器
    public class Input
    {
            public string n;
            public string size;
            public string p;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
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

### <a name="binomial-distribution-quantile-calculator"></a>二项式分布分位计算器
![创建工作区][4]

#### <a name="module-1"></a>模块 1：
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
#### <a name="module-2"></a>模块 2：
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }

    if (param$prob < 0 ) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 0
    } else if (param$prob > 1) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
    band=subset(df,x>quantile)
    } else if (param$side =='L') {
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
    band=subset(df,x<=quantile)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(quantile)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


### <a name="binomial-distribution-probability-calculator"></a>二项式分布概率计算器
![创建工作区][5]

#### <a name="module-1"></a>模块 1：
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


#### <a name="module-2"></a>模块 2：
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
    prob = 1 - prob
    band=subset(df,x>param$q)
    } else if (param$side =='E') {
    prob = prob.eq
    band=subset(df,x==param$q)
    } else if (param$side =='L') {
    prob = prob
    band=subset(df,x<=param$q)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### <a name="binomial-distribution-generator"></a>二项式分布生成器
![创建工作区][6]

#### <a name="module-1"></a>模块 1：
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

#### <a name="module-2"></a>模块 2：
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

## <a name="limitations"></a>限制
这些是关于二项式分布的一些非常简单的示例。 从上面的示例代码中可看出，进行了很小的错误捕获。

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到 Azure 应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png


