---

ROBOTS: NOINDEX, NOFOLLOW
redirect_url: https://gallery.cortanaintelligence.com/
translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 9c7b8e4378cbc19255e60aa177207afe3d552087
ms.lasthandoff: 05/03/2017


---
# <a name="deprecated-lexicon-based-sentiment-analysis"></a>（已弃用）基于词典的情绪分析

> [!NOTE]
> 将要停用 Microsoft DataMarket，并且此 API 已弃用。 
> 
> 你可以在 [Cortana Intelligence 库](http://gallery.cortanaintelligence.com)中找到许多有用的示例试验和 API。 有关该库的详细信息，请参阅[共享和发现 Cortana Intelligence 库中的资源](machine-learning-gallery-how-to-use-contribute-publish.md)。

如何测量用户在联机社交网络中（例如 Facebook 帖子、推文、评论等）关于品牌或主题的意见和态度？ 情绪分析提供用于分析此类问题的方法。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

通常有两种方法可用于情绪分析。 其中一种方法使用监督式学习算法，另一种方法可视为非监督式学习。 监督式学习算法通常会在大型标注语料库上生成分类模型。 它的精确度主要基于标注质量，而且训练过程通常需要很长的时间。 此外，当我们将算法应用到其他域时，效果通常并不好。 与监督式学习相比，基于词典的非监督式学习使用情绪字典，它不需要存储大型数据语料库和训练，因此可加快整个过程的速度。 

我们的[服务](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis)基于 MPQA 主观词典 (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/)，这是最常用的主观词典之一。 MPQA 中有 5097 个积极情绪词和 2533 个消极情绪词。 而且所有这些词都会标注为强极性或弱极性。 整个语料库位于 GNU 通用公共授权下方。 Web 服务可以应用到任何短句，例如推文和 Facebook 帖子。 

> 此 Web 服务可供用户使用，例如，可能通过移动应用、网站或者甚至是本地计算机。 但 Web 服务同时也是 Azure 机器学习如何用于在 R 代码顶部创建 Web 服务的示例。 只需几行 R 代码并单击 Azure 机器学习工作室内的某个按钮，就可使用 R 代码创建实验，并作为 Web 服务发布。 Web 服务随后可发布到 Azure 应用商店，并供世界各地的用户和设备使用，无需通过 Web 服务的创作者设置基础结构。
> 
> 

## <a name="consumption-of-web-service"></a>Web 服务的使用
输入数据可以是任何文本，但该 Web 服务适合在短句上使用。 输出可以是介于 -1 和 1 之间的数值。 任何小于 0 的值表示文本情绪是消极的；大于 0 的值则表示情绪是积极的。 结果的绝对值表示关联情绪的强度。 

> 托管在 Azure 应用商店上的此服务是 OData 服务；这些可能会通过 POST 或 GET 方法进行调用。 
> 
> 

有多种方法可以自动方式使用该服务（[此处](http://microsoftazuremachinelearning.azurewebsites.net/)为示例应用）。

### <a name="starting-c-code-for-web-service-consumption"></a>为 Web 服务使用启动 C# 代码：
    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();

                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



输入是“今天是美好的一天”。 输出是“1”，表示与输入句子相关联的积极情绪。 

## <a name="creation-of-web-service"></a>Web 服务的创建
> 此 Web 服务是使用 Azure 机器学习创建的。 有关免费试用版，以及关于创建实验和[发布 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)的介绍性视频，请参阅 [azure.com/ml](http://azure.com/ml)。 下面是创建 Web 服务的实验的屏幕快照，以及实验内每个模块的示例代码。
> 
> 

从 Azure 机器学习中创建了新的空白实验。 下图介绍了基于词典的情绪分析的实验流。 “sent_dict.csv”文件是 MPQA 主观词典，并已设为[执行 R 脚本][execute-r-script]的其中一个输入。 另一个输入是 Amazon 用于测试的评论数据集中的抽样评论，我们已在此数据集中执行选择、行名称修改和拆分操作。 我们使用哈希包将主观词典存储到内存中，以加快分数计算过程。 整体文本将由“tm”包标记化，并与情绪字典中的词进行比较。 最后，将通过在文本中添加每个主观词的权重来计算分数。 

### <a name="experiment-flow"></a>实验流：
![实验流][2]

#### <a name="module-1"></a>模块 1：
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame

# <a name="install-hash-package"></a>安装哈希包
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }

        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")



## <a name="limitations"></a>限制
从算法的角度来看，基于词典的情绪分析是常规情绪分析工具，该工具在特定字段中的执行效果可能不及分类方法。 否定问题不太好处理。 我们在程序中对一些否定词进行了硬编码，不过使用否定字典并生成一些规则是不错的方法。 与复杂冗长的句子（例如 Amazon 评论）相比，在短句（例如推文和 Facebook 帖子）上执行 Web 服务效果更佳。 

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到 Azure 应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/



