---
title: "连接到机器学习 Web 服务 | Microsoft Docs"
description: "通过 C# 或 Python，使用授权密钥连接到 Azure 机器学习 Web 服务。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 59b07bab-b60f-48c4-a385-a162e50ec7c2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: garye
ROBOTS: NOINDEX
redirect_url: /azure/machine-learning/machine-learning-consume-web-services
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: 52153526fb5b127823316b86fa05c0528151e18f
ms.openlocfilehash: 2dfcdf2207d1437a917c493075e3245bd58381ac
ms.contentlocale: zh-cn
ms.lasthandoff: 01/10/2017


---
# <a name="connect-to-an-azure-machine-learning-web-service"></a>连接到 Azure 机器学习 Web 服务
Azure 机器学习开发人员体验是一种 Web 服务 API，可从输入数据进行实时预测或成批量预测。 可使用 Azure 机器学习工作室创建预测和部署 Azure 机器学习 Web 服务。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

了解如何使用机器学习工作室创建和部署机器学习 Web 服务：

* 有关如何在机器学习工作室中创建试验的教程，请参阅[Create your first experiment](machine-learning-create-experiment.md)（创建你的第一个实验）。
* 有关如何部署 Web 服务的详细信息，请参阅 [Deploy a Machine Learning web service](machine-learning-publish-a-machine-learning-web-service.md)（部署机器学习 Web 服务）。
* 有关机器学习的概括信息，请访问[机器学习文档中心](https://azure.microsoft.com/documentation/services/machine-learning/)。

## <a name="azure-machine-learning-web-service"></a>Azure 机器学习 Web 服务
外部应用程序可使用 Azure 机器学习 Web 服务实时与机器学习服务工作流评分模型通信。 机器学习 Web 服务调用将预测结果返回到外部应用程序。 若要执行机器学习 Web 服务调用，可以传递部署 Web 服务时创建的 API 密钥。 机器学习 Web 服务基于 REST（流行的 Web 编程项目体系结构）。

Azure 机器学习有两种类型的服务：

* 请求响应服务 (RRS) - 低延迟、高度可缩放的服务，针对从机器学习工作室创建和部署的无状态模型提供接口。
* 批处理执行服务 (BES) - 为一批数据记录进行评分的异步服务。

有关机器学习 Web 服务的详细信息，请参阅 [Deploy a Machine Learning Web service](machine-learning-publish-a-machine-learning-web-service.md)（部署机器学习 Web 服务）。

## <a name="get-an-azure-machine-learning-authorization-key"></a>获取 Azure 机器学习授权密钥
部署实验时，会为 Web 服务生成 API 密钥。 可从多个位置检索密钥。

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>从 Microsoft Azure 机器学习 Web 服务门户检索
登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net)门户。

为新的机器学习 Web 服务检索 API 密钥：

1. 在 Azure 机器学习 Web 服务门户中，单击“Web 服务”顶部菜单。
2. 单击想要为其检索密钥的 Web 服务。
3. 单击顶部菜单上的“使用”。
4. 复制并保存“主密钥”。

为经典机器学习 Web 服务检索 API 密钥：

1. 在 Azure 机器学习 Web 服务门户中，单击“经典 Web 服务”顶部菜单。
2. 单击正在使用的 Web 服务。
3. 单击想要为其检索密钥的终结点。
4. 单击顶部菜单上的“使用”。
5. 复制并保存“主密钥”。

### <a name="classic-web-service"></a>经典 Web 服务
 还可以从机器学习工作室或 Azure 经典门户来检索经典 Web 服务的密钥。

#### <a name="machine-learning-studio"></a>机器学习工作室
1. 在机器学习工作室左侧，单击“Web 服务”。
2. 单击一个 Web 服务。 “API 密钥”位于“仪表板”选项卡。

#### <a name="azure-classic-portal"></a>Azure 经典门户
1. 在左侧单击“机器学习”。
2. 单击 Web 服务所在的工作区。
3. 单击“Web 服务”。
4. 单击一个 Web 服务。
5. 单击一个终结点。 “API 密钥”降到了右下角。

## <a id="connect"></a>连接到机器学习 Web 服务
可使用支持 HTTP 请求和响应的编程语言连接到机器学习 Web 服务。 可从机器学习 Web 服务帮助页，查看 C#、Python 和 R 示例。

**机器学习 API 帮助**部署 Web 服务时会创建机器学习 API 帮助。 请参阅 [Azure Machine Learning Walkthrough- Deploy Web Service](machine-learning-walkthrough-5-publish-web-service.md)（Azure 机器学习演练 - 部署 Web 服务）。
机器学习 API 帮助包含有关预测 Web 服务的详细信息。

1. 单击正在使用的 Web 服务。
2. 单击想要为其查看 API 帮助页的终结点。
3. 单击顶部菜单上的“使用”。
4. 在请求响应或批处理执行终结点下方单击“API 帮助页”。

**查看新 Web 服务的机器学习 API 帮助**

在 Azure 机器学习 Web 服务门户：

1. 在顶部菜单单击“WEB 服务”。
2. 单击想要为其检索密钥的 Web 服务。

单击“使用”获取请求响应和批处理执行以及 C#、R 和 Python 示例代码的 URI。

单击“Swagger API”从提供的 URI 为调用的 API 获取基于 Swagger 的文档。

### <a name="c-sample"></a>C# 示例
若要连接到机器学习 Web 服务，请使用 **HttpClient** 传递 ScoreData。 ScoreData 包含 FeatureVector以及表示 ScoreData 的具有数字特征的 N 维向量。 使用 API 密钥对机器学习服务进行身份验证。

若要连接到机器学习 Web 服务，必须安装 **Microsoft.AspNet.WebApi.Client** NuGet 包。

**在 Visual Studio 中安装 Microsoft.AspNet.WebApi.Client NuGet**

1. 发布 UCI 下载数据集：成人 2 类数据集 Web 服务。
2. 单击“工具” > “NuGet 包管理器” > “包管理器控制台”。
3. 选择 **Microsoft.AspNet.WebApi.Client 安装包**。

**运行代码示例**

1. 发布“示例 1：从 UCI 下载数据集：成人 2 类数据集”实验、机器学习示例集合的一部分。
2. 使用 Web 服务密钥分配 API 密钥。 请参阅上方的**获取 Azure 机器学习授权密钥**。
3. 使用请求 URI 分配服务 URI。

### <a name="python-sample"></a>Python 示例
若要连接到机器学习 Web 服务，请使用 **urllib2** 库传递 ScoreData。 ScoreData 包含 FeatureVector以及表示 ScoreData 的具有数字特征的 N 维向量。 使用 API 密钥对机器学习服务进行身份验证。

**运行代码示例**

1. 部署“示例 1：从 UCI 下载数据集：成人 2 类数据集”实验、机器学习示例集合的一部分。
2. 使用 Web 服务密钥分配 API 密钥。 请参阅本文开始处附近的**获取 Azure 机器学习授权密钥**部分。
3. 使用请求 URI 分配服务 URI。


