---

ROBOTS: NOINDEX, NOFOLLOW
redirect_url: https://gallery.cortanaintelligence.com/
translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 38a086281ec9a93961caf492036aeed27091a462
ms.lasthandoff: 05/03/2017


---
# <a name="deprecated-survival-analysis"></a>（已弃用）生存分析

> [!NOTE]
> 将要停用 Microsoft DataMarket，并且此 API 已弃用。 
> 
> 你可以在 [Cortana Intelligence 库](http://gallery.cortanaintelligence.com)中找到许多有用的示例试验和 API。 有关该库的详细信息，请参阅[共享和发现 Cortana Intelligence 库中的资源](machine-learning-gallery-how-to-use-contribute-publish.md)。

在许多情况下，主要评估结果是所感兴趣事件的时间。 换言之，询问的问题是“何时将发生此事件？” 。 例如，考虑数据描述所感兴趣事件（疾病复发、取得博士学位、刹车片失灵）发生前已用时间（天、年、里程数等）的情形。 数据中的每个实例代表特定对象（病患、学生、汽车等）。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

此 [Web 服务](https://datamarket.azure.com/dataset/aml_labs/survivalanalysis)回答了“对象 x 在时间 n 前发生所感兴趣事件的概率是多少？”这个问题 通过提供生存分析模型，此 Web 服务可让用户提供数据来训练模型并进行测试。 实验的主题是要在感兴趣的事件发生前对所用时间长度建模。 

> 此 Web 服务可供用户使用 - 例如，可能通过移动应用、网站，或者甚至是本地计算机。 但 Web 服务同时也是 Azure 机器学习如何用于在 R 代码顶部创建 Web 服务的示例。 只需几行 R 代码并单击 Azure 机器学习工作室内的某个按钮，就可使用 R 代码创建实验，并作为 Web 服务发布。 Web 服务随后可发布到 Azure 应用商店，并供世界各地的用户和设备使用，无需通过 Web 服务的创作者设置基础结构。  
> 
> 

## <a name="consumption-of-web-service"></a>Web 服务的使用
Web 服务的输入数据架构如下表所示。 需要以下六条信息作为输入：训练数据、测试数据、感兴趣的时间、“时间”维度的索引、“事件”维度的索引和变量类型（连续或因素）。 训练数据用字符串表示，其中以逗号分隔行，以分号分隔列。 数据的特征数是灵活的。 输入字符串中的所有元素必须都是数字。 在训练数据中，“时间”维度表示自研究的起点（病患接受药物治疗方案、学生开始攻读博士学位、汽车开始发动等），到发生感兴趣的事件（病患再度用药、学生取得博士学位、汽车刹车片失灵等）所经过的时间单位数（天、年、里程数等）。 “事件”维度指示感兴趣的事件是否发生在研究结束时。 “事件 = 1”的值表示感兴趣的事件发生在“时间”维度所指示的时间；而“事件 = 0”表示到“时间”维度所指示的时间为止都未发生感兴趣的事件。

* trainingdata - 字符串。 行由逗号分隔，列由分号分隔。 每个行包含“时间”维度、“事件”维度和预测器变量。
* testingdata - 包含特定对象预测器变量的一行数据。
* time_of_interest - 感兴趣的 n 所用时间。
* index_time -“时间”维度的列索引（从 1 开始）。
* index_event -“事件”维度的列索引（从 1 开始）。
* variable_types - 用分号作为分隔符的字符串。 0 表示连续变量，而 1 表示因素变量。

输出是在特定时间前发生事件的概率。 

> 托管在 Azure 应用商店上的此服务是 OData 服务；这些可能会通过 POST 或 GET 方法进行调用。 
> 
> 

有多种方法可以自动方式使用该服务（[此处](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)为示例应用）。 

### <a name="starting-c-code-for-web-service-consumption"></a>为 Web 服务使用启动 C# 代码：
    public class Input
    {
            public string trainingdata;
            public string testingdata;
            public string timeofinterest;
            public string indextime;
            public string indexevent;
            public string variabletypes;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




此测试的解释如下。 假定数据目标是要对在接受两种治疗方案之一的病患再次用药所经过的时间建模。 Web 服务的输出如下所示：病患目前 35 岁，先前接受药物治疗 2 次，采用长期住院治疗方案，吸食海洛因和可卡因，在第 500 天前再次用药的概率是 95.64%。

## <a name="creation-of-web-service"></a>Web 服务的创建
> 此 Web 服务是使用 Azure 机器学习创建的。 有关免费试用版，以及关于创建实验和[发布 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)的介绍性视频，请参阅 [azure.com/ml](http://azure.com/ml)。 下面是创建 Web 服务的实验的屏幕快照，以及实验内每个模块的示例代码。
> 
> 

在 Azure 机器学习内，创建了新的空白实验，并将两个[执行 R 脚本][execute-r-script]模块提取到工作区。 数据架构使用简单的[执行 R 脚本][execute-r-script]进行创建，这定义了 Web 服务的输入数据架构。 然后，此模块会链接到第二个[执行 R 脚本][execute-r-script]模块，这将执行主要工作。 此模块执行数据预处理、模型生成和预测。 在数据预处理步骤中，长字符串所表示的输入数据会转换为数据帧。 在模型生成步骤中，将先安装外部 R 包“survival_2.37-7.zip”以进行生存分析。 然后，“coxph”函数在一系列数据处理任务之后执行。 可以从 R 文档读取生存分析的“coxph”函数详细信息。 在预测步骤中，会使用“surfit”函数将测试实例提供给训练模型，并且此测试实例的生存曲线会生成为“curve”变量。 最后，获取所感兴趣时间的概率。 

### <a name="experiment-flow"></a>实验流：
![实验流][1]

#### <a name="module-1"></a>模块 1：
    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"

    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port

#### <a name="module-2"></a>模块 2：
    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




## <a name="limitations"></a>限制
此 Web 服务只能采用数值作为特征变量（列）。 “事件”列可能采用值 0 或 1。 “时间”列必须是正整数。

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到 Azure 应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/


