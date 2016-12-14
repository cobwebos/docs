---
title: "使用机器学习 Web 服务 | Microsoft Docs"
description: "一旦部署机器学习服务后，即可将可用的 RESTFul Web 服务用作请求响应服务或批处理执行服务。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 369b7c8775388f641727cb889b406ac60be13d16


---
# <a name="how-to-consume-an-azure-machine-learning-web-service-that-has-been-deployed-from-a-machine-learning-experiment"></a>如何使用从机器学习实验部署的 Azure 机器学习 Web 服务
## <a name="introduction"></a>介绍
部署为 Web 服务时，Azure 机器学习实验会提供可用于各种设备和平台 REST API 和 JSON 格式的信息。 Azure 机器学习门户会提供可用于调用 R、C# 和 Python 中 Web 服务的代码。 

可通过满足以下三个条件的任意语言和平台调用服务：

* 拥有网络连接
* 具有执行 HTTPS 请求的 SSL 功能
* 可以分析 JSON（手动分析或通过支持库分析）

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

可通过两种方法使用 Azure 机器学习 Web 服务：作为请求响应服务或批处理执行服务。 一旦部署实验后，即可使用 RESTFul Web 服务，每个方案中的功能均通过该服务提供。

> [!TIP]
> 了解如何简单创建 web 应用，访问预测 Web 服务，请参阅 [Consume an Azure Machine Learning Web service with a web app template](machine-learning-consume-web-service-with-web-app-template.md)（使用 Azure 机器学习 web 服务与 web 应用模板）。
> 
> 

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Web service endpoints using the REST API, see **Azure machine learning Web service endpoints**.
-->

了解有关如何创建和部署 Azure 机器学习 Web 服务的信息，请参阅 [Deploy an Azure Machine Learning Web service]（部署 Azure 机器学习 Web 服务）[发布]。 了解创建和部署机器学习的步骤演练，请参阅 [Develop a predictive solution by using Azure Machine Learning]（使用 Azure 机器学习开发预测解决方案）[演练]。

## <a name="request-response-service-rrs"></a>请求响应服务 (RRS)
请求响应服务 (RRS) 是一种低延迟、高度可缩放的 Web 服务，针对从机器学习工作室实验创建和部署的无状态模型提供接口。 使用应用程序可通过该服务收到实时响应。

RRS 接受单行或多行输入参数，并且可生成单行或多行输出。 输出行可包含多列。

RRS 的一个用处就是验证应用程序的可靠性。 在这种情况下，可能出现数百到数以百万计的应用程序的安装。 启动应用程序后，会通过相关输入调用 RRS 服务。 然后应用程序会从该服务收到验证响应，允许或阻止应用程序运行。

## <a name="batch-execution-service-bes"></a>批处理执行服务 (BES)
批处理执行服务 (BES) 是一种可处理一批数据记录的大容量、异步评分的服务。 BES 的输入包括一批来自各种源的记录，如 blob、Azure 中的表、SQL Azure、HDInsight（比如 Hive 查询结果）和 HTTP 源。 BES 的输出包含评分结果。 结果将输出到 Azure blob 存储中的文件，存储终结点的数据将在响应中返回。

无需立即响应的情况下，BES 十分有用，例如可用于为个人或物联网设备定期进行评分。

## <a name="examples"></a>示例
为展示 RRS 和 BES 的工作方式，我们采用示例 Azure Web 服务。 此服务可用于 IOT（物联网）方案。 为简单起见，我们的设备只会发送一个值，`cog_speed`，会收到单个回复。

部署实验后，调用 RRS 或 BES 服务需要四条信息。

* 服务 **API 密钥**或**主密钥**
* 服务**请求 URI**
* 预期 API **请求标题**和**正文**
* 预期 API **响应标题**和**正文**

查找这些信息的方式取决于部署的服务类型：新 Web 服务或传统 Web 服务。

### <a name="information-location-in-the-azure-machine-learning-web-services-portal"></a>信息在 Azure 机器学习 Web 服务门户中的位置
查找所需信息：

1. 登录到 [Azure 机器学习 Web 服务] [webservicesportal]门户。
2. 单击“Web 服务”或“传统 Web 服务”。
3. 单击正在使用的 Web 服务。 
4. 如果正在使用传统 Web 服务，单击正在使用的终结点。

信息位于以下页面：

* **主密钥**位于“使用”页
* **请求 URI** 位于“使用”页 
* 预期 API **请求标题**、**响应标题**和**正文**位于“Swagger API”页

### <a name="information-locations-in-machine-learning-studio-classic-web-service-only"></a>信息在机器学习工作室中的位置（仅针对传统 Web 服务）
可从两个位置找到所需信息：机器学习工作室或 Azure 机器学习 Web 服务门户。

在机器学习工作室中查找所需信息：

1. 登录到[机器学习工作室] [mlstudio]。
2. 单击屏幕左侧的“WEB 服务”。
3. 单击正在使用的 Web 服务。 

信息位于以下页面：

* **API 密钥**位于服务**仪表板** 
* **请求 URI** 位于 API 帮助页
* 预期 API **请求标题**、**响应标题**和**正文**位于 API 帮助页

若要访问 API 帮助页，视任务情况单击“请求/响应”或“批处理执行”。

在 Azure 机器学习 Web 服务门户查找所需信息：

1. 登录到 [Azure 机器学习 Web 服务] [webservicesportal]门户。
2. 单击“经典 Web 服务”。
3. 单击正在使用的 Web 服务。 
4. 单击正在使用的终结点。

信息位于以下页面：

* **主密钥**位于“使用”页
* **请求 URI** 位于“使用”页 
* 预期 API **请求标题**、**响应标题**和**正文**位于“Swagger API”页

在下方两个示例中，使用 C# 语言分析所需代码。

### <a name="rrs-example"></a>RRS 示例
下方示例请求表示 API 为示例服务的 API 调用输入有效负载。 对于传统 Web 服务，可以在机器学习 Web 服务门户上的“API 帮助”页或“Swagger API”页找到有效负载示例。 对于新 Web 服务，可以在机器学习 Web 服务门户上的“Swagger API”页找到有效负载示例。

**示例请求**

    {
      "Inputs": {
        "input1": {
          "ColumnNames": [
            "cog_speed"
          ],
          "Values": [
            [
              "0"
            ],
            [
              "1"
            ]
          ]
        }
      },
      "GlobalParameters": {}
    }


同样，下方示例表示服务的 API 响应。

**示例响应**

    {
      "Results": {
        "output1": {
          "type": "DataTable",
          "value": {
            "ColumnNames": [
              "cog_speed"
            ],
            "ColumnTypes": [
              "Numeric"
            ].
          "Values": [
            [
              "0"
            ],
            [
              "1"
            ]
          ]
        }
      },
      "GlobalParameters": {}
    }

下方为运行 C# 的代码示例。 对于传统 Web 服务，可在 **API 帮助页**或“使用”页底部找到代码示例。 对于新 Web 服务，可在“使用”页找到代码示例。

**C# 中的示例代码**

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Formatting;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;

    namespace CallRequestResponseService
    {
        public class StringTable
        {
            public string[] ColumnNames { get; set; }
            public string[,] Values { get; set; }
        }

        class Program
        {
            static void Main(string[] args)
            {
                InvokeRequestResponseService().Wait();
            }

            static async Task InvokeRequestResponseService()
            {
                using (var client = new HttpClient())
                {
                    var scoreRequest = new
                    {
                        Inputs = new Dictionary<string, StringTable> () {
                            {
                                "input1",
                                new StringTable()
                                {
                                    ColumnNames = new string[] {"cog_speed"},
                                    Values = new string[,] {  { "0"},  { "1"}  }
                                }
                            },
                        GlobalParameters = new Dictionary<string, string>() { }
                    };

                    const string apiKey = "abc123"; // Replace this with the API key for the Web service
                    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);

                    client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");

                    // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
                    // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
                    // For instance, replace code such as:
                    //      result = await DoSomeTask()
                    // with the following:
                    //      result = await DoSomeTask().ConfigureAwait(false)

                    HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                    if (response.IsSuccessStatusCode)
                    {
                        string result = await response.Content.ReadAsStringAsync();
                        Console.WriteLine("Result: {0}", result);
                    }
                    else
                    {
                        Console.WriteLine("Failed with status code: {0}", response.StatusCode);
                    }
                }
            }
        }
    }

**Java 中的示例代码**

下方示例代码显示了如何在 Java 中构建 REST API 请求。 它会假定变量（apikey 和 apiurl）含有必须的 API 详情，且变量 jsonBody 有 REST API 所需的正确 JSON 对象。 可从 github 下载完整代码 - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)。 此代码示例需要 [apache http 客户端库](https://hc.apache.org/downloads.cgi)。

    /**
     * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
      */
        /**
           * Call REST API for retrieving prediction from Azure ML 
           * @return response from the REST API
           */    
        public static String rrsHttpPost() {

            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                    // create HttpPost and HttpClient object
                    post = new HttpPost(apiurl);
                    client = HttpClientBuilder.create().build();

                    // setup output message by copying JSON body into 
                    // apache StringEntity object along with content type
                    entity = new StringEntity(jsonBody, HTTP.UTF_8);
                    entity.setContentEncoding(HTTP.UTF_8);
                    entity.setContentType("text/json");

                    // add HTTP headers
                    post.setHeader("Accept", "text/json");
                    post.setHeader("Accept-Charset", "UTF-8");

                    // set Authorization header based on the API key
                    post.setHeader("Authorization", ("Bearer "+apikey));
                    post.setEntity(entity);

                    // Call REST API and retrieve response content
                    HttpResponse authResponse = client.execute(post);

                    return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                    return e.toString();
            }

        }




### <a name="bes-example"></a>BES 示例
与 RRS 服务不同，BES 服务为异步。 这表示 BES API 会对要执行的作业进行简单排队，调用方会轮询作业状态查看作业完成的时间。 此处为批处理作业当前支持的操作：

1. 创建（提交）批处理作业
2. 启动此批处理作业
3. 获取批处理作业状态或结果
4. 取消正在运行的批处理作业

**1.创建批处理执行作业**

为 Azure 机器学习服务创建批处理作业时，可指定几个定义批处理执行的参数：

* **输入**：表示存储批处理作业输入的 blob 引用。
* **GlobalParameters**：表示可为其实验进行定义的全局参数集。 Azure 机器学习实验可含有必需参数和可自定义服务执行的可选参数，且如果可能，调用方需要提供所有必需参数。 这些参数被指定为一系列的密钥值对。
* **输出**：如果服务定义了一个或多个输出，调用方可将任意输出重定向到 Azure blob 位置。 通过设置此参数可以将输出保存到选定位置且保存到可预测名称下，否则将会随机生成输出 blob 名。 
  
    根据输出类型，服务会将输出内容存储为受支持的格式：
  
  * 数据集输出：可另为 **.csv、.tsv、.arff** 格式
  * 训练模型输出：必须存为 **.ilearner** 格式
    
    指定输出位置会进行覆盖，作为一些列输出名或 blob 引用对。 *输出名*是用户为特定输出节点定义的名称，*blob 引用*是对 Azure blob 位置的引用，输出可重定向到此处。 服务 API 帮助页显示有*输出名*。

所有作业创建参数均为可选，具体取决于服务性质。 例如，未定义输出节点的服务无需 *输出* 参数。 同样，输出位置覆盖功能也是可选的，另一种情况是将输出存储在为 Azure 机器学习工作区设置的默认存储帐户。 下方示例为服务请求有效负载，其中之提供了输入请求：

**示例请求**

    {
      "Input": {
        "ConnectionString":     
        "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
        "RelativeLocation": "/mycontainer/mydatablob.csv",
        "BaseLocation": null,
        "SasBlobToken": null
      },
      "Outputs": null,
      "GlobalParameters": null
    }

批处理作业创建 API 的响应是唯一的作业 ID，该 ID 与作业相关联。 此 ID 十分重要，因为要在系统中为其他操作引用此作业，只能通过它提供的方法。  

**示例响应**

    "539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2.启动批处理执行作业**

创建批处理作业后，会在系统中进行注册并将其置于*未启动*状态。 若要切实执行该作业，可调用**开始** API，该 API 在服务终结点帮助页或 Web 服务 Swagger API 页中有所描述，并提供创建作业时获取的作业 ID。

**3.获取批处理执行作业的状态**

将作业 ID 传送到 *GetJobStatus* API 可随时轮询异步批处理作业的状态。 API 响应包含作业当前状态指示器和表明是否已成功完成作业的批处理结果。 如果出错，会在*详情*属性中返回有关错误具体原因的详细信息，如下所示：

**响应有效负载**

    {
        "StatusCode": STATUS_CODE,
        "Results": RESULTS,
        "Details": DETAILS
    }

*状态代码*可以是以下值之一：

* 未启动
* 正在运行
* 已失败
* 已取消
* 已完成

作业成功完成后会填充*结果*属性（否则属性为 **null**）。一旦作业完成后，如果服务至少拥有一个定义的输出节点，则将会返回作为一系列 *[输出名，blob 引用]* 对的结果，其中 blob 引用是对包含结果的 blob 的 SAS 只读引用。

**示例响应**

    {
        "Status Code": "Finished",
        "Results":
        {
            "dataOutput":
            {              
                "ConnectionString": null,
                "RelativeLocation": "outputs/dataOutput.csv",
                "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
                "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
            },
            "trainedModelOutput":
            {              
                "ConnectionString": null,
                "RelativeLocation": "models/trainedModel.ilearner",
                "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
                "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
            },           
        },
        "Details": null
    }

**4.取消批处理执行作业**

通过调用指定的 *CancelJob* API 并传入作业 ID，可随时取消正在运行的批处理作业。 可基于各种原因取消作业，例如完成作业时间过长。

#### <a name="using-the-bes-sdk"></a>使用 BES SDK
[BES SDK Nuget 包](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/)可简化调用 BES 在批处理模式中进行评分。 若要安装 Nuget 包，在“工具”菜单的 Visual Studio 中，选择“Nuget 包管理器”并单击“包管理器控制台”。

部署为 Web 服务的 Azure 机器学习实验可包括 Web 服务输入模块。 这表示 Web 服务的输入通过 Web 调用提供，其形式为对 blob 位置的引用。 还可以选择不使用 Web 服务输入模块，而是使用“导入数据”模块。 这种情况下，“导入数据”模块在运行时使用查询从 SQL DB 等数据源进行读取。 Web 服务参数可用于动态指向其他服务器或表等。SDK 支持这两种模式。

下方代码示例展示了如何使用 BES SDK 在提交并监视针对 Azure 机器学习服务的批处理作业。 备注中包含设置和调用详情。

#### <a name="sample-code"></a>**示例代码**
    // This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
    // Instructions for doing this in Visual Studio:
    // Tools -> Nuget Package Manager -> Package Manager Console
    // Install-Package Microsoft.Azure.MachineLearning

      using System;
      using System.Collections.Generic;
      using System.Threading.Tasks;

      using Microsoft.Azure.MachineLearning;
      using Microsoft.Azure.MachineLearning.Contracts;
      using Microsoft.Azure.MachineLearning.Exceptions;

    namespace CallBatchExecutionService
    {
        class Program
        {
            static void Main(string[] args)
            {                
                InvokeBatchExecutionService().Wait();
            }

            static async Task InvokeBatchExecutionService()
            {
                // First collect and fill in the URI and access key for your Web service endpoint.
                // These are available on your service's API help page.
                var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
                string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

                // Create an Azure Machine Learning runtime client for this endpoint
                var runtimeClient = new RuntimeClient(endpointUri, accessKey);

                // Define the request information for your batch job. This information can contain:
                // -- A reference to the AzureBlob containing the input for your job run
                // -- A set of values for global parameters defined as part of your experiment and service
                // -- A set of output blob locations that allow you to redirect the job's results

                // NOTE: This sample is applicable, as is, for a service with explicit input port and
                // potential global parameters. Also, we choose to also demo how you could override the
                // location of one of the output blobs that could be generated by your service. You might
                // need to tweak these features to adjust the sample to your service.
                //
                // All of these properties of a BatchJobRequest shown below can be optional, depending on
                // your service, so it is not required to specify all with any request.  If you do not want to
                // use any of the parameters, a null value should be passed in its place.

                // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

                // If applicable, you can also set the global parameters for your service
                var globalParameters = new Dictionary<string, string>
                {
                    { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
                };

                var jobRequest = new BatchJobRequest
                {
                    Input = inputBlob,
                    GlobalParameters = globalParameters,
                    Outputs = outputLocations
                };

                try
                {
                    // Register the batch job with the system, which will grant you access to a job object
                    BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

                    // Start the job to allow it to be scheduled in the running queue
                    await job.StartAsync();

                    // Wait for the job's completion and handle the output
                    BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
                    if (jobStatus.JobState == JobState.Finished)
                    {
                        // Process job outputs
                        Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
                        foreach (var output in jobStatus.Results)
                        {
                            Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
                        }
                    }
                    else if (jobStatus.JobState == JobState.Failed)
                    {
                        // Handle job failure
                        Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
                    }
                }
                catch (ArgumentException aex)
                {
                    Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
                }
                catch (RuntimeException runtimeError)
                {
                    Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
                    Console.WriteLine("Error details:");
                    foreach (var errorDetails in runtimeError.Details)
                    {
                        Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
                }
            }
        }
    }

#### <a name="sample-code-in-java-for-bes"></a>Java 中的 BES 示例代码
批处理执行服务 REST API 采用包含对输入示例 csv 和输出示例 csv 的引用的 JSON（如下示例所示），并在 Azure ML 中创建一项作业完成批处理预测。 可在 [Github](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp) 中查看完整代码。 此代码示例需要 [apache http 客户端库](https://hc.apache.org/downloads.cgi)。 

    { "GlobalParameters": {}, 
        "Inputs": { "input1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=Q8kkieg==", 
            "RelativeLocation": "myContainer/sampleinput.csv" } }, 
        "Outputs": { "output1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
            "RelativeLocation": "myContainer/sampleoutput.csv" } } 
    } 


##### <a name="create-a-bes-job"></a>创建 BES 作业
        /**
         * Call REST API to create a job to Azure ML 
         * for batch predictions
         * @return response from the REST API
         */    
        public static String besCreateJob() {

            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(apiurl);
                client = HttpClientBuilder.create().build();

                // setup output message by copying JSON body into 
                // apache StringEntity object along with content type
                entity = new StringEntity(jsonBody, HTTP.UTF_8);
                entity.setContentEncoding(HTTP.UTF_8);
                entity.setContentType("text/json");

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                // note a space after the word "Bearer " - don't miss that
                post.setHeader("Authorization", ("Bearer "+apikey));
                post.setEntity(entity);

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll("\"", "");


                return jobId;

            }
            catch (Exception e) {

                return e.toString();
            }

        }

##### <a name="start-a-previously-created-bes-job"></a>启动以前创建的 BES 作业
        /**
         * Call REST API for starting prediction job previously submitted 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */    
        public static String besStartJob(String job){
            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
                client = HttpClientBuilder.create().build();

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }

                return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                return e.toString();
            }
        }
##### <a name="cancel-a-previously-created-bes-job"></a>取消以前创建的 BES 作业
        /**
         * Call REST API for canceling the batch job 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */    
        public static String besCancelJob(String job) {
            HttpDelete post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpDelete(startJobUrl+job);
                client = HttpClientBuilder.create().build();

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }
                return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                return e.toString();
            }
        }

### <a name="other-programming-environments"></a>其他编程环境
按 [swagger.io](http://swagger.io/) 网站给出的指示还可生成许多其他语言的代码。 对于传统 Web 服务可以通过以下方式获取 swagger 文档：

* 从 API 帮助页获取 
* 通过调用机器学习 Web 服务门户 Swagger API 页上终结点的“获取 API 文档”获取。 

转到 [swagger.io](http://swagger.io/swagger-codegen/) 并按说明下载 swagger 代码、java 和 apache mvn。 

下表总结了关于为其他编程环境设置 swagger 的说明。

* 请确保已安装 Java 7 或更高版本
* 安装 apache mvn（在 ubuntu 上，可使用 apt-get 安装 mvn）
* 转到 github 获取 swagger 并将 swagger 项目下载为 zip 文件
* 解压缩 swagger
* 从 swagger 源目录运行 mvn 包 生成 swagger 工具

现在可以使用所有 swagger 工具。 以下是生成 Java 客户端代码的说明。 

* 转到 Azure ML API 帮助页（[此处](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs)为示例）
* 查找 Azure ML REST API 的swagger.json 的 URL（API 帮助页基顶部倒数第二个要点）
* 单击 swagger 文档链接（[此处](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)为示例）
* 使用 [swagger 自述文件](https://github.com/swagger-api/swagger-codegen/blob/master/README.md)中的如下命令生成客户端代码

**生成客户端代码的示例命令行**

    java -jar swagger-codegen-cli.jar generate\
     -i https://ussouthcentral.services.azureml.net:443/workspaces/\
    fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
     -l java -o /home/username/sample

* 将 basePath and "/swagger.json" 字段合并到如下示例 swagger [API 帮助页](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)，以构建用于命令行之前的 URL。

**示例 API 帮助页**

    {
      "swagger": "2.0",
      "info": {
        "version": "2.0",
        "title": "Sample 5: Binary Classification with Web service: Adult Dataset [Predictive Exp.]",
        "description": "No description provided for this Web service.",
        "x-endpoint-name": "default"
      },
      "host": "ussouthcentral.services.azureml.net:443",
      "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
      "schemes": [
        "https"
      ],
      "consumes": [
        "application/json"
      ],
      "produces": [
        "application/json"
      ],
      "paths": {
        "/swagger.json": {
          "get": {
            "summary": "Get swagger API document for the Web service",
            "operationId": "getSwaggerDocument",

<!-- Relative Links -->

[发布]: machine-learning-publish-a-machine-learning-web-service.md
[演练]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- External Links -->
[webservicesportal]: https://services.azureml.net/
[mlstudio]: https://studio.azureml.net



<!--HONumber=Nov16_HO3-->


