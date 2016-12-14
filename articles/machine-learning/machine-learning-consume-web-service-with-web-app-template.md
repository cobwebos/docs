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
ms.date: 10/10/2016
ms.author: garye;raymondl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c115ba524a8a87a3ba5349a668079d8bc817f794


---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>使用 Azure 机器学习 web 服务与 web 应用模板
> [!NOTE]
> 本主题介绍适用于经典 web 服务的技术。 
> 
> 

一旦使用机器学习工作室或者 R 或 Python 等工具开发了预测模型且将其部署为 Azure web 服务，即可使用 REST API 访问操作模型。

可通过多种方法来使用 REST API 和访问 web 服务。 例如，可以使用部署 Web 服务时为生成的示例代码，在 C#、R 或 Python 中编写应用程序（可以在机器学习工作室中 Web 服务仪表板上的 API 帮助页获取）。 或者，可以使用创建的示例 Microsoft Excel 工作簿（也可以在工作室中的 Web 服务仪表板获取）。

但访问 web 服务最简单快捷的方法是通过 [Azure Web 应用商店](https://azure.microsoft.com/marketplace/web-applications/all/)提供的 Web 应用模板。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Azure 机器学习 Web 应用模板
Azure 应用商店提供的 web 应用模板可创建自定义 web 应用，该应用了解 web 服务的输入数据及所需结果。 所需操作就是授予 web 应用访问 web 服务和数据的权限，余下操作将由模板完成。

提供了两个模板：

* [Azure ML 请求响应服务 Web 应用模板](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML 批处理执行服务 Web 应用模板](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

每个模板都会创建一个使用 web 服务的 API URI 和密钥的示例 ASP.NET 应用程序，并将其作为网站部署到 Azure。 请求响应服务 (RRS) 模板会创建一个 web 应用，通过应用可将单行数据发送到 web 服务，获取单个结果。 批处理执行服务 (BES) 模板可创建一个 web 应用，可通过应用发送多行数据，获取多个结果。

使用这些模板无需进行编码。 只需提供 API URI 和密钥，模板就可生成应用程序。

## <a name="how-to-use-the-request-response-service-rrs-template"></a>如何使用请求响应服务 (RRS) 模板
一旦部署 web 服务后，即可按照下方步骤使用 RRS web 应用模板，如下图所示。

![使用 RRS web 模板的过程][image1]

1. 在机器学习工作室中打开“Web 服务”选项卡，然后打开想要访问的 web 服务。 复制“API 密钥”下方列出的密钥并保存。
   
    ![API 密钥][image3]
2. 打开“请求/响应”API 帮助页面。 在帮助页面顶部的“请求”下方，复制“请求 URI”值并保存。 值如下所示：
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![请求 URI][image4]
3. 转到“Azure 门户”[](https://portal.azure.com)，“登录”，单击“新建”，搜索并选择“Azure ML 请求响应服务 Web 应用”，然后单击“创建”。 
   
   * 为 Web 应用提供唯一的名称。 将以此名称加上 `.azurewebsites.net.` 作为 web 应用的 URL 名称。例如，`http://carprediction.azurewebsites.net.`
   * 选择 Azure 订阅和要在其下运行 web 服务的服务。
   * 单击“创建” 。
     
     ![创建 Web 应用][image5]
4. Azure 完成 web 应用部署后，单击Azure 中 web 应用设置页面上的 **URL**，或者在 web 浏览器中输入该 URL。 例如 `http://carprediction.azurewebsites.net.`
5. 首次运行 web 应用时，会要求你提供 **API Post URL** 和 **API 密钥**。
   输入此前保存的值：
   
   * API 帮助页面上针对 **API Post URL** 的**请求 URI**
   * Web 服务仪表板上针对 **API 密钥**的 **API 密钥**。
     
     单击“提交”。
     
     ![输入 Post URI 和 API 密钥][image6]
6. Web 应用以当前 web 服务设置显示其“Web 应用配置”页面。 可在此处更改 web 应用使用的设置。
   
   > [!NOTE]
   > 在此处只能更改此 web 应用的设置。 不会更改 web 服务的默认设置。 例如，如果更改了此处的“描述”，机器学习工作室中 web 服务仪表板上显示的描述不会发生改变。
   > 
   > 
   
    完成后，单击“保存更改”，然后单击“转到主页”。
7. 在主页上，可输入要发送到 web 服务的值，单击“提交”，然后会返回结果。

如果希望返回“配置”页，请转到 web 应用的 `setting.aspx` 页。 例如：`http://carprediction.azurewebsites.net/setting.aspx.`。会提示你再次输入 API 密钥 - 你需要密钥来访问该页面和更新设置。

与其他 web 应用一样，你可以在 Azure 门户暂停、重启或删除 web 应用。 只要应用正在运行，就可以浏览至主页网址并输入新值。

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>如何使用批处理执行服务 (BES) 模板
可以通过与 RRS 模板相同的方法使用 BES web 应用模板，但通过创建的 web 应用可提交多行数据并接收多个结果。

来自批处理执行 web 服务的结果存储在 Azure 存储容器中；可输入 Azure 存储或本地文件的值。
因此，需要一个 Azure 存储容器来保存 web 应用程序返回的结果，你需要准备好的输入数据。

![使用 BES web 模板的过程][image2]

1. 按照与 RRS 模板相同的步骤来创建 BES web 应用，除了：
   
   * 从**批处理执行**API 帮助页面获取针对 web 服务的**请求 URI**。
   * 转到“Azure ML 批处理执行服务 Web 应用模板”[](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)打开 Azure 应用商店上的 BES 模板，然后单击“创建 Web 应用”。
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

[图片1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[图片5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[图片6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png



<!--HONumber=Nov16_HO3-->


