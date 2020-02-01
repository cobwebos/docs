---
title: 使用模板部署 web 应用-Azure Cosmos DB
description: 了解如何使用 Azure 资源管理器模板部署 Azure Cosmos DB 帐户、Azure App Service Web 应用和示例 Web 应用程序。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 11b84877171fae8e788d3298973fd74645b35480
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901916"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板部署 Azure Cosmos DB 和 Azure App Service Web 应用
本教程介绍如何使用 Azure 资源管理器模板来部署和集成[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)、 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) web 应用和示例 web 应用程序。

使用 Azure 资源管理器模板，可以轻松地自动部署和配置 Azure 资源。  本教程演示如何部署 web 应用程序并自动配置 Azure Cosmos DB 帐户连接信息。

完成本教程后，能够回答以下问题：  

* 如何使用 Azure 资源管理器模板在 Azure App Service 中部署和集成 Azure Cosmos DB 帐户和 web 应用？
* 如何使用 Azure 资源管理器模板来部署和集成 Azure Cosmos DB 帐户、应用服务 Web 应用中的 web 应用以及 Webdeploy 应用程序？

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Prerequisites
> [!TIP]
> 虽然本教程不会假设事先具有 Azure 资源管理器模板或 JSON 的经验，但是，如果你想要修改引用的模板或部署选项，则需要了解每个领域的知识。
> 
> 

在按照本教程中的说明操作之前，请确保拥有 Azure 订阅。 Azure 是基于订阅的平台。  有关获取订阅的详细信息，请参阅[购买选项](https://azure.microsoft.com/pricing/purchase-options/)、[成员优惠](https://azure.microsoft.com/pricing/member-offers/)或[免费试用](https://azure.microsoft.com/pricing/free-trial/)。

## <a id="CreateDB"></a>步骤1：下载模板文件
首先，让我们下载本教程所需的模板文件。

1. 下载[创建 Azure Cosmos DB 帐户、Web 应用，并将演示应用程序示例模板部署](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json)到本地文件夹（例如，C:\Azure Cosmos DBTemplates）。 此模板部署 Azure Cosmos DB 帐户、应用服务 web 应用和 web 应用程序。  它还会自动配置 web 应用程序以连接到 Azure Cosmos DB 帐户。
2. 下载[创建 Azure Cosmos DB 帐户和 Web 应用示例](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json)模板到本地文件夹（例如，C:\Azure Cosmos DBTemplates）。 此模板部署 Azure Cosmos DB 帐户、应用服务 web 应用，并修改站点的应用程序设置，以便轻松地呈现 Azure Cosmos DB 的连接信息，但不包括 web 应用程序。  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>步骤2：部署 Azure Cosmos DB 帐户、应用服务 web 应用和演示应用程序示例
现在，让我们来部署第一个模板。

> [!TIP]
> 该模板不会验证在以下模板中输入的 web 应用名称和 Azure Cosmos DB 的帐户名是否都是有效的和 b）可用。  强烈建议在提交部署之前验证计划提供的名称的可用性。
> 
> 

1. 登录到[Azure 门户](https://portal.azure.com)，单击 "新建"，然后搜索 "模板部署"。
    ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment1.png)
2. 选择模板部署项，并单击 "**创建**" 模板部署 UI ![屏幕快照](./media/create-website/TemplateDeployment2.png)
3. 单击 "**编辑模板**"，粘贴 docdbwebsitetodo.json 模板文件的内容，然后单击 "**保存**"。
   ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment3.png)
4. 单击 "**编辑参数**"，为每个必需的参数提供值，然后单击 **"确定"** 。  参数如下所示：
   
   1. SITENAME：指定应用服务 web 应用名称，并用来构造用于访问 web 应用的 URL （例如，如果指定 "mydemodocdbwebsite"，则用于访问 web 应用的 URL 将 `mydemodocdbwebapp.azurewebsites.net`）。
   2. HOSTINGPLANNAME：指定要创建的应用服务托管计划的名称。
   3. LOCATION：指定要在其中创建 Azure Cosmos DB 和 web 应用资源的 Azure 位置。
   4. DATABASEACCOUNTNAME：指定要创建的 Azure Cosmos DB 帐户的名称。   
      
      ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment4.png)
5. 选择现有的资源组或提供名称以创建新的资源组，并选择资源组的位置。

    ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment5.png)
6. 单击 "**查看法律条款**"、"**购买**"，然后单击 "**创建**" 以开始部署。  选择 "**固定到仪表板**"，以便在 Azure 门户主页上轻松查看生成的部署。
   ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment6.png)
7. 部署完成后，将打开 "资源组" 窗格。
   !["资源组" 窗格的屏幕截图](./media/create-website/TemplateDeployment7.png)  
8. 若要使用应用程序，请导航到 web 应用 URL （在上面的示例中，URL 将 `http://mydemodocdbwebapp.azurewebsites.net`）。  你将看到以下 web 应用程序：
   
   ![示例 Todo 应用程序](./media/create-website/image2.png)
9. 继续操作并在 web 应用中创建几个任务，并返回到 Azure 门户中的 "资源组" 窗格。 单击 "资源" 列表中的 "Azure Cosmos DB 帐户" 资源，然后单击 "**数据资源管理器**"。
10. 运行默认查询 "SELECT * FROM c"，并检查结果。  请注意，查询已检索到前面步骤7中创建的待办事项的 JSON 表示形式。  随意试用查询;例如，尝试运行 SELECT * FROM c WHERE C.iscomplete = true 以返回已标记为完成的所有 todo 项。
11. 可随时浏览 Azure Cosmos DB 门户体验，或修改示例 Todo 应用程序。  准备就绪后，让我们来部署另一个模板。

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>步骤3：部署文档帐户和 web 应用示例
现在，让我们来部署第二个模板。  此模板可用于演示如何将帐户终结点和主密钥等 Azure Cosmos DB 连接信息插入 web 应用，作为应用程序设置或自定义连接字符串。 例如，你可能有自己的 web 应用程序，希望使用 Azure Cosmos DB 帐户进行部署，并且在部署期间自动填充连接信息。

> [!TIP]
> 该模板不会验证下面输入的 web 应用名称和 Azure Cosmos DB 的帐户名称是否 a）有效以及 b）是否可用。  强烈建议在提交部署之前验证计划提供的名称的可用性。
> 
> 

1. 在[Azure 门户](https://portal.azure.com)中，单击 "新建" 并搜索 "模板部署"。
    ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment1.png)
2. 选择模板部署项，并单击 "**创建**" 模板部署 UI ![屏幕快照](./media/create-website/TemplateDeployment2.png)
3. 单击 "**编辑模板**"，粘贴 docdbwebsite.json 模板文件的内容，然后单击 "**保存**"。
   ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment3.png)
4. 单击 "**编辑参数**"，为每个必需的参数提供值，然后单击 **"确定"** 。  参数如下所示：
   
   1. SITENAME：指定应用服务 web 应用名称，并用于构造将用于访问 web 应用的 URL （例如，如果指定 "mydemodocdbwebsite"，则用于访问 web 应用的 URL 为 mydemodocdbwebapp.azurewebsites.net）。
   2. HOSTINGPLANNAME：指定要创建的应用服务托管计划的名称。
   3. LOCATION：指定要在其中创建 Azure Cosmos DB 和 web 应用资源的 Azure 位置。
   4. DATABASEACCOUNTNAME：指定要创建的 Azure Cosmos DB 帐户的名称。   
      
      ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment4.png)
5. 选择现有的资源组或提供名称以创建新的资源组，并选择资源组的位置。

    ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment5.png)
6. 单击 "**查看法律条款**"、"**购买**"，然后单击 "**创建**" 以开始部署。  选择 "**固定到仪表板**"，以便在 Azure 门户主页上轻松查看生成的部署。
   ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment6.png)
7. 部署完成后，将打开 "资源组" 窗格。
   !["资源组" 窗格的屏幕截图](./media/create-website/TemplateDeployment7.png)  
8. 单击 "资源" 列表中的 "Web 应用" 资源，然后单击 "**应用程序设置**" ![资源组的屏幕截图](./media/create-website/TemplateDeployment9.png)  
9. 请注意，Azure Cosmos DB 终结点和每个 Azure Cosmos DB 主密钥存在应用程序设置。

    ![应用程序设置的屏幕截图](./media/create-website/TemplateDeployment10.png)  
10. 请随时继续浏览 Azure 门户，或按照其中一个 Azure Cosmos DB[示例](https://go.microsoft.com/fwlink/?LinkID=402386)来创建自己的 Azure Cosmos DB 应用程序。

<a name="NextSteps"></a>

## <a name="next-steps"></a>后续步骤
祝贺你！ 已使用 Azure 资源管理器模板部署 Azure Cosmos DB、应用服务 web 应用和示例 web 应用程序。

* 若要详细了解 Azure Cosmos DB，请单击[此处](https://azure.microsoft.com/services/cosmos-db/)。
* 若要详细了解 Azure App Service Web 应用，请单击[此处](https://go.microsoft.com/fwlink/?LinkId=325362)。
* 若要了解有关 Azure 资源管理器模板的详细信息，请单击[此处](https://msdn.microsoft.com/library/azure/dn790549.aspx)。

## <a name="whats-changed"></a>更改内容
* 有关从网站更改为应用服务的指南，请参阅： [Azure App Service 及其对现有 Azure 服务的影响](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> 若要在注册 Azure 帐户之前开始使用 Azure App Service，请转到[试用应用服务](https://go.microsoft.com/fwlink/?LinkId=523751)，可以在应用服务中立即创建一个生存期较短的入门 web 应用。 无需信用卡;无承诺。
> 
> 

