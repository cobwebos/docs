---
title: "了解如何使用 API 管理来管理 AzureML Web 服务 | Microsoft Docs"
description: "介绍如何使用 API 管理来管理 AzureML Web 服务的指南。"
keywords: "机器学习, API 管理"
services: machine-learning
documentationcenter: 
author: roalexan
manager: jhubbard
editor: 
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: roalexan
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 53a6b18fb74db46ccb66c7c70851a9bf364e927c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>了解如何使用 API 管理来管理 AzureML Web 服务
## <a name="overview"></a>概述
本指南介绍如何快速使用 API 管理来管理 AzureML Web 服务。

## <a name="what-is-azure-api-management"></a>什么是 Azure API 管理？
Azure API 管理是一项 Azure 服务，支持通过定义用户访问权限、使用限制和仪表板监视管理 REST API 终结点。 单击[此处](https://azure.microsoft.com/services/api-management/)获取有关 Azure API 管理的详细信息。 单击[此处](../../api-management/api-management-get-started.md)获取如何使用 Azure API 管理的指南。 这份额外指南（作为本指南的依据）涵盖更多主题，包括通知配置、层定价、响应处理、用户身份验证、创建产品、开发人员订阅和使用仪表板。

## <a name="what-is-azureml"></a>AzureML 是什么？
AzureML 是用于机器学习的 Azure 服务，支持轻松生成、部署和共享先进的分析解决方案。 单击[此处](https://azure.microsoft.com/services/machine-learning/)了解有关 AzureML 的详细信息。

## <a name="prerequisites"></a>先决条件
若要完成本指南，需要：

* 一个 Azure 帐户。 如果没有 Azure 帐户，请单击[此处](https://azure.microsoft.com/pricing/free-trial/)了解有关如何创建免费试用版帐户的详细信息。
* AzureML 帐户。 如果没有 AzureML 帐户，请单击[此处](https://studio.azureml.net/)了解有关如何创建免费测试版帐户的详细信息。
* AzureML 实验的工作区、服务和 api_key 均部署为 Web 服务。 单击[此处](create-experiment.md)了解如何创建 AzureML 实验的详细信息。 单击[此处](publish-a-machine-learning-web-service.md)了解如何将 AzureML 实验部署为 Web 服务的详细信息。 另外，附录 A 说明了如何创建并测试简单 AzureML 实验并将其部署为 Web 服务。

## <a name="create-an-api-management-instance"></a>创建 API 管理实例
下面是使用 API 管理来管理 AzureML Web 服务的步骤。 首先创建服务实例。 登录[经典门户](https://manage.windowsazure.com/)，并依次单击“新建” > “应用服务” > “API 管理” > “创建”。

![create-instance](./media/manage-web-service-endpoints-using-api-management/create-instance.png)

指定唯一 **URL**。 本指南使用 **demoazureml**，因为需要选择不同命令。 为服务实例选择所需的**订阅**和**区域**。 做出选择后，单击下一步按钮。

![create-service-1](./media/manage-web-service-endpoints-using-api-management/create-service-1.png)

为“组织名称”指定值。 本指南使用 **demoazureml**，因为需要选择不同命令。 在“管理员电子邮件”字段中输入电子邮件地址。 此电子邮件地址用于来自 API 管理系统的通知。

![create-service-2](./media/manage-web-service-endpoints-using-api-management/create-service-2.png)

单击复选框创建服务实例。 *创建新服务最多需要三十分钟*。

## <a name="create-the-api"></a>创建 API
创建服务实例后，下一步是创建 API。 API 包含一组可以从客户端应用程序调用的操作。 API 操作代理到现有的 web 服务。 本指南创建成为现有 AzureML RRS 和 BES Web 服务代理的 API。

在 API 发布者门户（通过 Azure 经典门户访问）中创建和配置 API。 若要访问发布者门户，请选择服务实例。

![select-service-instance](./media/manage-web-service-endpoints-using-api-management/select-service-instance.png)

在 API 管理服务的 Azure 经典门户中单击“管理”。

![manage-service](./media/manage-web-service-endpoints-using-api-management/manage-service.png)

单击左侧“API 管理”菜单中的“API”，并单击“添加 API”。

![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management-menu.png)

键入“AzureML 演示 API”作为“Web API 名称”。 键入“https://ussouthcentral.services.azureml.net”作为“Web 服务 URL”。 键入“azureml-demo”作为“Web API URL 后缀”。 选中“HTTPS”作为“Web API URL”方案。 选择“简易版”作为“产品”。 完成后，单击“保存”创建 API。

![add-new-api](./media/manage-web-service-endpoints-using-api-management/add-new-api.png)

## <a name="add-the-operations"></a>添加操作
单击“添加操作”，向此 API 添加操作。

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-operation.png)

将显示“新建操作”窗口，并且在默认情况下将选择“签名”选项卡。

## <a name="add-rrs-operation"></a>添加 RRS 操作
首先为 AzureML RRS 服务创建操作。 选择“POST”作为“HTTP 谓词”。 键入“/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}”“URL 模板”。 键入“RRS 执行”作为“显示名称”。

![add-rrs-operation-signature](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

在左侧依次单击“响应” > “添加”，并选择“200 正常”。 单词“保存”，保存此操作。

![add-rrs-operation-response](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>添加 BES 操作
BES 操作不包括屏幕截图，因为这些屏幕截图与添加 RRS 操作的屏幕截图类似。

### <a name="submit-but-not-start-a-batch-execution-job"></a>提交（但不启动）批处理执行作业
单击“添加操作”，向 API 添加 AzureML BES 操作。 选择“POST”作为“HTTP 谓词”。 键入“/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}”作为“URL 模板”。 键入“BES 提交”作为“显示名称”。 在左侧依次单击“响应” > “添加”，并选择“200 正常”。 单词“保存”，保存此操作。

### <a name="start-a-batch-execution-job"></a>启动批处理执行作业
单击“添加操作”，向 API 添加 AzureML BES 操作。 选择“POST”作为“HTTP 谓词”。 键入“/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}”作为“URL 模板”。 键入“BES 启动”作为“显示名称”。 在左侧依次单击“响应” > “添加”，并选择“200 正常”。 单词“保存”，保存此操作。

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>获取批处理执行作业的状态或结果
单击“添加操作”，向 API 添加 AzureML BES 操作。 选择“GET”作为“HTTP 谓词”。 键入“/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}”作为“URL 模板”。 键入“BES 状态”作为“显示名称”。 在左侧依次单击“响应” > “添加”，并选择“200 正常”。 单词“保存”，保存此操作。

### <a name="delete-a-batch-execution-job"></a>删除批处理执行作业
单击“添加操作”，向 API 添加 AzureML BES 操作。 选择“DELETE”作为“HTTP 谓词”。 键入“/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}”作为“URL 模板”。 键入“BES 删除”作为“显示名称”。 在左侧依次单击“响应” > “添加”，并选择“200 正常”。 单词“保存”，保存此操作。

## <a name="call-an-operation-from-the-developer-portal"></a>从开发人员门户调用操作
可以直接从开发人员门户调用操作，这样可以方便地查看和测试 API 的操作。 此指南步骤将调用添加到“AzureML 演示 API”的“RRS 执行”方法。 单击经典门户右上角菜单中的“开发人员门户”。

![developer-portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

单击顶部菜单的“API”，并单击“AzureML 演示 API”，查看可用操作。

![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

为操作选择“RRS 执行”。 单击“试用”。

![try-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

对于请求参数，请为“apiversion”键入“工作区”、“服务”、“2.0”，并为“详细信息”键入“true”。 可在 AzureML Web 服务仪表板中查找“工作区”和“服务”（请参阅附录 A 中的**测试 Web 服务**）。

对于请求标头，请单击“添加标头”，并键入“Content-Type”和“application/json”，然后单击“添加标头”并键入“授权”和“持有者”**<YOUR AZUREML SERVICE API-KEY>**。 可在 AzureML Web 服务仪表板中查找“API 密钥”（请参阅附录 A 中的**测试 Web 服务**）。

键入“{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}”作为请求正文。

![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

单击“发送”。

![发送](./media/manage-web-service-endpoints-using-api-management/send.png)

调用操作后，开发人员门户将从后端服务显示“请求的 URL”、“响应状态”、“响应标头”以及任何“响应内容”。

![response-status](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>附录 A：创建并测试简单 AzureML Web 服务
### <a name="creating-the-experiment"></a>创建实验
下面是创建简单 AzureML 实验并将其部署为 Web 服务的步骤。 采用任意文本列作为输入，并返回以整数形式表示的功能集。 例如：

| 文本 | 经过哈希处理的文本 |
| --- | --- |
| 今天天气不错 |1 1 2 2 0 2 0 1 |

首先，使用所选浏览器导航到 [https://studio.azureml.net/](https://studio.azureml.net/)，并输入凭据以登录。 接下来，创建新的空白实验。

![搜索实验模板](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

将其重命名为“SimpleFeatureHashingExperiment”。 展开“保存的数据集”，并将“Amazon 中的书评”拖动至实验。

![simple-feature-hashing-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

展开“数据转换”和“操作”，并将“选择数据集中的列”拖动至实验。 将“Amazon 中的书评”连接到“选择数据集中的列”。

![select-columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

单击“选择数据集中的列”，并单击“启动列选择器”并选择“Col2”。 单击复选标记应用这些更改。

![select-columns](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

展开“文本分析”，并将“功能哈希”拖动至实验。 将“选择数据集中的列”连接到“功能哈希”。

![connect-project-columns](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

键入“3”作为“哈希位大小”。 这会创建 8 (23) 个列。

![hashing-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

此时你可能想要单击“运行”测试试验。

![run](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>创建 Web 服务
现在创建 Web 服务。 展开“Web 服务”，并将“输入”拖动至实验。 将“输入”连接到“功能哈希”。 另将“输出”拖动至实验。 将“输出”连接到“功能哈希”。

![output-to-feature-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

单击“发布 Web 服务”。

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

单击“是”发布实验。

![yes-to-publish](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>测试 Web 服务
AzureML Web 服务包含 RSS（请求/响应服务）和 BES（批处理执行服务）终结点。 RSS 适用于同步执行。 BES 适用于异步作业执行。 若要通过下面的 Python 源示例测试 Web 服务，可能需要下载并安装 Azure SDK for Python（请参阅[如何安装 Python](../../python-how-to-install.md)）。

还需要下面源示例实验的“工作区”、“服务”和“api_key”。 在 Web 服务仪表板中单击实验的“请求/响应”或“批处理执行”，查找工作区和服务。

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

在 Web 服务仪表板中单击实验，查找“api_key”。

![find-api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>测试 RRS 终结点
##### <a name="test-button"></a>“测试”按钮
测试 RRS 终结点的简单方法是在 Web 服务仪表板上单击“测试”。

![测试](./media/manage-web-service-endpoints-using-api-management/test.png)

为“col2”键入“今天天气不错”。 单击复选标记。

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

会看到类似于下面的内容

![sample-output](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>代码示例
测试 RRS 的另一种方法是从客户端代码入手。 如果在仪表板上单击“请求/响应”，并滚动到底部，会看到 C#、Python 和 R 的示例代码。还将看到 RRS 请求的语法，包括请求 URI、标头和正文。

本指南介绍了运行中的 Python 示例。 将使用实验的“工作区”、“服务”和“api_key”修改该示例。

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>测试 BES 终结点
在仪表板上单击“批处理执行”并滚动到底部。 会看到 C#、Python 和 R 的示例代码。还将看到提交作业、启动作业、获取作业状态或结果以及删除作业的 BES 请求语法。

本指南介绍了运行中的 Python 示例。 需要使用实验的“工作区”、“服务”和“api_key”修改该示例。 此外，还需要修改“存储帐户名称”、“存储帐户密钥”和“存储容器名称”。 最后，需要修改“输入文件”和“输出文件”的位置。

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()

