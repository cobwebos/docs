---
title: "在 Azure 机器学习中部署新的 Web 服务 | Microsoft Docs"
description: "部署基于 ARM 的 Web 服务的工作流"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: a358b04f-0d08-4d50-820e-eeac971854cf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: v-donglo
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-publish-a-machine-learning-web-service
translationtype: Human Translation
ms.sourcegitcommit: 0813611f581a68efb8f09a1e041cfbe429bf0c5c
ms.openlocfilehash: 902be47eb59444a1214b096be10525743f406d1c


---
# <a name="deploy-a-new-web-service"></a>部署新 Web 服务
Microsoft Azure 机器学习现在提供基于 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 的 Web 服务，从而允许使用新的计费计划选项，以及将 Web 服务部署到多个区域。

使用 Microsoft Azure 机器学习 Web 服务部署 Web 服务的一般工作流如下：

* 创建预测性实验
* 部署
* 配置其名称
* 收费计划
* 测试
* 使用。

下图演示了此工作流。

![Web 服务部署工作流][1]

## <a name="deploy-web-service-from-studio"></a>从工作室部署 Web 服务
将实验部署为新 Web 服务。 登录机器学习工作室，并创建新的预测 Web 服务。 

**请注意**：如果已将实验部署为经典 Web 服务，则无法将其部署为新 Web 服务。

单击实验画布底部的“运行”，然后依次单击“部署 Web 服务”和“部署 Web 服务[新]”。 机器学习 Web 服务管理器的部署页将打开。

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>机器学习 Web 服务管理器“部署实验”页
在“部署实验”页上，输入 Web 服务的名称。
选择定价计划。 如果你有现有的定价计划，可以选择它，否则你必须为该服务创建新的定价计划。 

1. 在“定价计划”下拉菜单中选择一个现有计划，或选择“选择新计划”选项。
2. 在“计划名称”中，键入用于标识帐单上的计划的名称。
3. 从“每月计划层”中选择一个计划层。 请注意，计划层默认为默认区域的计划，并且 Web 服务将部署到该区域。

单击“部署”和“快速入门”页，打开 Web 服务。

## <a name="quickstart-page"></a>“快速入门”页
Web 服务“快速入门”页提供了有关在创建新 Web 服务后将执行的最常见任务的访问和指导。 从此处，可以轻松访问“测试”页和“使用”页。

## <a name="testing-your-web-service"></a>测试 Web 服务
在“快速入门”页上，单击常见任务下的“测试 Web 服务”。   

若要将 Web 服务作为请求-响应服务 (RRS) 测试，请执行以下步骤：

* 单击菜单栏上的“测试”。
* 单击“请求-响应”。
* 为实验的输入列输入相应值。
* 单击“测试请求-响应”。

结果将显示在页面右侧。

若要测试批处理执行服务 (BES) Web 服务，需使用 CSV 文件：

* 单击菜单栏上的“测试”。
* 单击“批处理”。
* 在输入下，单击“浏览”并导航到示例数据文件。
* 单击“测试”。

测试状态显示在“测试批处理作业”下。

## <a name="consuming-your-web-service"></a>使用 Web 服务
部署为 Web 服务时，Azure 机器学习实验提供可由各种设备和平台使用的 REST API。 这是因为简单的 REST API 使用 JSON 格式的消息接受并响应。 Azure 机器学习门户会提供可用于调用 R、C# 和 Python 中 Web 服务的代码。

在“使用”页上可找到：

* 用于在应用中使用 Web 服务的 API 密钥和 URI。
* 用于推动使用过程的 Excel 和 Web 应用模板。
* 帮助入门的 C#、python 和 R 示例代码。

有关使用 Web 服务的详细信息，请参阅[如何使用已从机器学习实验部署的 Azure 机器学习 Web 服务](machine-learning-consume-web-services.md)。

## <a name="next-steps"></a>后续步骤
有关使用 Web 服务的更多信息，请参阅：

[如何使用已从机器学习实验部署的 Azure 机器学习 Web 服务](machine-learning-consume-web-services.md)

[Azure 机器学习 Web 服务：部署和使用](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->



<!--HONumber=Jan17_HO4-->


