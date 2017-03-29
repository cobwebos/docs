---
title: "使用机器学习 Web 服务与 Web 应用模板 | Microsoft Docs"
description: "利用 Azure 应用商店中的 web 应用模板来是使用 Azure 机器学习中的预测 web 服务。"
keywords: "web 服务，操作化，REST API，机器学习"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 5d587618dd885ec30e35b13c5fd543c1a8853200
ms.lasthandoff: 03/22/2017


---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>使用 Azure 机器学习 web 服务与 web 应用模板

一旦使用机器学习工作室或者 R 或 Python 等工具开发了预测模型且将其部署为 Azure web 服务，即可使用 REST API 访问操作模型。

可通过多种方法来使用 REST API 和访问 web 服务。 例如，可以使用部署 Web 服务时生成的示例代码，在 C#、R 或 Python 中编写应用程序（可以在[机器学习 Web 服务门户](https://services.azureml.net/quickstart)或机器学习工作室中的 Web 服务仪表板上获取）。 或者，可以使用在同一时间创建的示例 Microsoft Excel 工作簿。

但访问 web 服务最简单快捷的方法是通过 [Azure Web 应用商店](https://azure.microsoft.com/marketplace/web-applications/all/)提供的 Web 应用模板。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Azure 机器学习 Web 应用模板
Azure 应用商店提供的 web 应用模板可创建自定义 web 应用，该应用了解 web 服务的输入数据及所需结果。 所需操作就是授予 web 应用访问 web 服务和数据的权限，余下操作将由模板完成。

提供了两个模板：

* [Azure ML 请求响应服务 Web 应用模板](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML 批处理执行服务 Web 应用模板](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

每个模板都会创建一个使用 web 服务的 API URI 和密钥的示例 ASP.NET 应用程序，并将其作为网站部署到 Azure。 请求响应服务 (RRS) 模板会创建一个 web 应用，通过应用可将单行数据发送到 web 服务，获取单个结果。 批处理执行服务 (BES) 模板可创建一个 web 应用，可通过应用发送多行数据，获取多个结果。

使用这些模板无需进行编码。 只需提供 API 密钥和 URI，模板即可生成应用程序。

获取 Web 服务的 API 密钥和请求 URI:

1. 对于新的 Web 服务，请在 [Web 服务门户](https://services.azureml.net/quickstart)中，单击顶部的“Web 服务”。 或者对于经典 Web 服务，单击“经典 Web 服务”。
2. 单击要访问的 Web 服务。
3. 对于经典 Web 服务，单击要访问的终结点。
4. 单击顶部的“使用”。
5. 复制“主密钥”或“辅助密钥”，并将其保存。
6. 如果要创建请求-响应服务 (RRS) 模板，请复制**请求-响应** URI 并将其保存。 如果要创建批处理执行服务 (BES) 模板，请复制**批处理请求** URI 并将其保存。


## <a name="how-to-use-the-request-response-service-rrs-template"></a>如何使用请求响应服务 (RRS) 模板
请按照下列步骤使用 RRS Web 应用模板，如下图所示。

![使用 RRS web 模板的过程][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. 转到 [Azure 门户](https://portal.azure.com)，“登录”，单击“新建”，搜索并选择“Azure ML 请求-响应服务 Web 应用”，然后单击“创建”。 
   
   * 为 Web 应用提供唯一的名称。 将以此名称加上 `.azurewebsites.net.` 作为 web 应用的 URL 名称。例如，`http://carprediction.azurewebsites.net.`
   * 选择 Azure 订阅和要在其下运行 web 服务的服务。
   * 单击“创建” 。
     
     ![创建 Web 应用][image5]

4. Azure 完成 web 应用部署后，单击Azure 中 web 应用设置页面上的 **URL**，或者在 web 浏览器中输入该 URL。 例如 `http://carprediction.azurewebsites.net.`
5. 首次运行 web 应用时，会要求你提供 **API Post URL** 和 **API 密钥**。
   输入之前保存的值（分别为“请求 URI”和“API 密钥”）。
     
     单击“提交”。
     
     ![输入 Post URI 和 API 密钥][image6]

6. Web 应用以当前 web 服务设置显示其“Web 应用配置”页面。 可在此处更改 web 应用使用的设置。
   
   > [!NOTE]
   > 在此处只能更改此 web 应用的设置。 不会更改 web 服务的默认设置。 例如，如果更改了此处的“描述”，机器学习工作室中 web 服务仪表板上显示的描述不会发生改变。
   > 
   > 
   
    完成后，单击“保存更改”，然后单击“转到主页”。

7. 可以在主页中输入要发送到 Web 服务的值。 操作完成后，单击“提交”，将返回结果。

如果希望返回“配置”页，请转到 web 应用的 `setting.aspx` 页。 例如：`http://carprediction.azurewebsites.net/setting.aspx.`。会提示你再次输入 API 密钥 - 你需要密钥来访问该页面和更新设置。

与其他 web 应用一样，你可以在 Azure 门户暂停、重启或删除 web 应用。 只要应用正在运行，就可以浏览至主页网址并输入新值。

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>如何使用批处理执行服务 (BES) 模板
可以通过与 RRS 模板相同的方法使用 BES web 应用模板，但通过创建的 web 应用可提交多行数据并接收多个结果。

批处理执行 Web 服务的输入值可来自于 Azure 存储或本地文件；其结果存储在 Azure 存储容器中。
因此，需要一个 Azure 存储容器来保存 web 应用程序返回的结果，你需要准备好的输入数据。

![使用 BES web 模板的过程][image2]

1. 请按照与 RRS 模板相同的步骤创建 BES Web 应用，不同的是需转到 [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)（Azure ML 批处理执行服务 Web 应用模板）打开 Azure 应用商店上的 BES 模板，然后单击“创建 Web 应用”。

2. 若要指定结果的存储位置，在 web 应用主页输入目标容器的信息。 还需指定 web 应用获取输入值的位置，可以是本地文件或 Azure 存储容器。
   单击“提交”。
   
    ![存储信息][image7]

Web 应用将显示含有作业状态的页面。
完成作业后，会给出结果在 Azure blob 存储中的位置。 还可以将结果下载到本地文件。

## <a name="for-more-information"></a>更多信息
详细了解以下详细内容...

* 通过机器学习工作室创建一个机器学习实验，请参阅 [Create your first experiment in Azure Machine Learning Studio](machine-learning-create-experiment.md)（在 Azure 机器学习工作室中创建你的第一个试验）
* 如何将机器学习实验部署为 web 服务，请参阅 [Deploy an Azure Machine Learning web service](machine-learning-publish-a-machine-learning-web-service.md)（部署 Azure 机器学习 web 服务）
* 访问 web 服务的其他方法，请参阅 [How to consume an Azure Machine Learning web service](machine-learning-consume-web-services.md)（如何使用 Azure 机器学习 web 服务）

[image1]: media/machine-learning-consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/machine-learning-consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/machine-learning-consume-web-service-with-web-app-template/api-key.png
[image4]: media/machine-learning-consume-web-service-with-web-app-template/post-uri.png
[image5]: media/machine-learning-consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/machine-learning-consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/machine-learning-consume-web-service-with-web-app-template/storage.png

