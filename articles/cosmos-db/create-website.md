---
title: "使用模板部署 Web 应用 - Azure Cosmos DB | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 模板部署 Azure Cosmos DB 帐户、Azure 应用服务 Web 应用以及示例 Web 应用程序。"
services: cosmos-db, app-service\web
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 087d8786-1155-42c7-924b-0eaba5a8b3e0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 424889a00a20c8daf770098a43576783b813340c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板部署 Azure Cosmos DB 和 Azure 应用服务 Web 应用
本教程说明如何使用 Azure Resource Manager 模板来部署和集成 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 应用以及示例 Web 应用程序。

使用 Azure Resource Manager 模板，你可以轻松自动化 Azure 资源的部署和配置。  本教程演示如何部署 Web 应用程序，以及自动配置 Azure Cosmos DB 帐户的连接信息。

完成本教程后，你将能够回答以下问题：  

* 如何使用 Azure Resource Manager 模板部署和集成 Azure Cosmos DB 帐户与 Azure 应用服务中的 Web 应用？
* 如何使用 Azure Resource Manager 模板部署和集成 Azure Cosmos DB 帐户、应用服务 Web 应用中的 Web 应用以及 Webdeploy 应用程序？

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>先决条件
> [!TIP]
> 虽然本教程不会假设先前有使用 Azure Resource Manager 模板或 JSON 的经验，但是，如果你想修改引用的模板或部署选项，则需要有其中每个领域的知识。
> 
> 

在按照本教程中的说明操作之前，请确保已有下列各项：

* Azure 订阅。 Azure 是基于订阅的平台。  有关获取订阅的详细信息，请参阅[购买选项](https://azure.microsoft.com/pricing/purchase-options/)、[成员优惠](https://azure.microsoft.com/pricing/member-offers/)或[免费试用](https://azure.microsoft.com/pricing/free-trial/)。

## <a id="CreateDB"></a>步骤 1：下载模板文件
让我们从下载模板文件开始，我们将在本教程中使用这些文件。

1. 将[创建 Azure Cosmos DB 帐户、Web 应用和部署演示应用程序示例](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json)模板下载到本地文件夹（例如 C:\Azure Cosmos DBTemplates）。 此模板将部署 Azure Cosmos DB 帐户、应用服务 Web 应用和 Web 应用程序。  它还会自动配置 Web 应用程序，以连接到 Azure Cosmos DB 帐户。
2. 将[创建 Azure Cosmos DB 帐户和 Web 应用示例](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json)模板下载到本地文件夹（例如 C:\Azure Cosmos DBTemplates）。 此模板将部署 Azure Cosmos DB 帐户、应用服务 Web 应用，并修改站点的应用程序设置以便轻松地呈现 Azure Cosmos DB 连接信息，但不包含 Web 应用程序。  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>步骤 2：部署 Azure Cosmos DB 帐户、应用服务 Web 应用和演示应用程序示例
现在让我们来部署第一个模板。

> [!TIP]
> 该模板不会验证下面输入的 Web 应用名称和 Azure Cosmos DB 帐户名称是否 a) 有效以及 b) 可用。  强烈建议在提交部署之前，先确认你打算提供的名称的可用性。
> 
> 

1. 登录到 [Azure 门户](https://portal.azure.com)，单击“新建”并搜索“模板部署”。
    ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment1.png)
2. 选择模板部署项目，然后单击 **创建**
    ![模板部署 UI 的屏幕快照](./media/create-website/TemplateDeployment2.png)
3. 单击“编辑模板”，粘贴 DocDBWebsiteTodo.json 模板文件的内容，然后单击“保存”。
   ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment3.png)
4. 单击“编辑参数”，为每个必需参数提供值，然后单击“确定”。  参数如下：
   
   1. SITENAME：指定应用服务 Web 应用名称，并用来构造你将用于访问 Web 应用的 URL（例如，如果你指定“mydemodocdbwebsite”，则将用于访问 Web 应用的 URL 会是 mydemodocdbwebsite.azurewebsites.net）。
   2. HOSTINGPLANNAME：指定要创建的应用服务托管计划的名称。
   3. LOCATION：指定要在其中创建 Azure Cosmos DB 和 Web 应用资源的 Azure 位置。
   4. DATABASEACCOUNTNAME：指定要创建的 Azure Cosmos DB 帐户的名称。   
      
      ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment4.png)
5. 选择现有的资源组或提供名称以创建新的资源组，然后选择资源组的位置。

    ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment5.png)
6. 依次单击“查看法律条款”、“购买”和“创建”以开始部署。  选择“固定到仪表板”，让生成的部署轻松显示在 Azure 门户的主页上。
   ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment6.png)
7. 部署完成后，将会打开“资源组”边栏选项卡。
   ![资源组边栏选项卡的屏幕截图](./media/create-website/TemplateDeployment7.png)  
8. 若要使用应用程序，只需导航到 Web 应用 URL（上述示例中的 URL 是 http://mydemodocdbwebapp.azurewebsites.net）。  你会看到下列 Web 应用程序：
   
   ![示例待办事项应用程序](./media/create-website/image2.png)
9. 继续在 Web 应用中创建几个任务，然后返回到 Azure 门户中的资源组边栏选项卡。 单击“资源”列表中的“Azure Cosmos DB 帐户”资源，然后单击“查询浏览器”。
    ![屏幕截图：突出显示 myotherdocumentdbwebapp web 应用的“摘要”可重用功能区](./media/create-website/TemplateDeployment8.png)  
10. 运行默认查询“SELECT * FROM c”，并检查结果。  请注意，查询已检索你在上面的步骤 7 中创建的待办事项的 JSON 表示形式。  随意尝试查询；例如，尝试运行 SELECT * FROM c WHERE c.isComplete = true，返回所有标记为完成的待办事项。
    
    ![屏幕截图：显示查询结果的“查询资源管理器”和“结果”边栏选项卡](./media/create-website/image5.png)
11. 任意体验 Azure Cosmos DB 门户，或修改示例待办事项应用程序。  当你准备好时，让我们来部署另一个模板。

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>步骤 3：部署 DocumentDB 帐户和 Web 应用示例
现在让我们来部署第二个模板。  此模板可用于演示如何以应用程序设置或自定义连接字符串的形式，将帐户终结点和主密钥等 Azure Cosmos DB 连接信息插入 Web 应用。 例如，你可能拥有自己的 Web 应用程序，并希望使用 Azure Cosmos DB 帐户部署这些应用程序，还要在部署期间自动填充连接信息。

> [!TIP]
> 该模板不会验证下面输入的 Web 应用名称和 Azure Cosmos DB 帐户名称是否 a) 有效以及 b) 可用。  强烈建议在提交部署之前，先确认你打算提供的名称的可用性。
> 
> 

1. 在 [Azure 门户](https://portal.azure.com)中，单击“新建”并搜索“模板部署”。
    ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment1.png)
2. 选择模板部署项目，然后单击 **创建**
    ![模板部署 UI 的屏幕快照](./media/create-website/TemplateDeployment2.png)
3. 单击“编辑模板”，粘贴 DocDBWebSite.json 模板文件的内容，然后单击“保存”。
   ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment3.png)
4. 单击“编辑参数”，为每个必需参数提供值，然后单击“确定”。  参数如下：
   
   1. SITENAME：指定应用服务 Web 应用名称，并用来构造你将用于访问 Web 应用的 URL（例如，如果你指定“mydemodocdbwebsite”，则将用于访问 Web 应用的 URL 会是 mydemodocdbwebsite.azurewebsites.net）。
   2. HOSTINGPLANNAME：指定要创建的应用服务托管计划的名称。
   3. LOCATION：指定要在其中创建 Azure Cosmos DB 和 Web 应用资源的 Azure 位置。
   4. DATABASEACCOUNTNAME：指定要创建的 Azure Cosmos DB 帐户的名称。   
      
      ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment4.png)
5. 选择现有的资源组或提供名称以创建新的资源组，然后选择资源组的位置。

    ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment5.png)
6. 依次单击“查看法律条款”、“购买”和“创建”以开始部署。  选择“固定到仪表板”，让生成的部署轻松显示在 Azure 门户的主页上。
   ![模板部署 UI 的屏幕截图](./media/create-website/TemplateDeployment6.png)
7. 部署完成后，将会打开“资源组”边栏选项卡。
   ![资源组边栏选项卡的屏幕截图](./media/create-website/TemplateDeployment7.png)  
8. 单击“资源”列表中的“Web 应用”资源，然后单击 **应用程序设置**
    ![资源的快照](./media/create-website/TemplateDeployment9.png)  
9. 注意，Azure Cosmos DB 终结点和每个 Azure Cosmos DB 主密钥都存在相应的应用程序设置。

    ![应用程序设置的屏幕截图](./media/create-website/TemplateDeployment10.png)  
10. 继续随意浏览 Azure 门户，或按照其中一个 Azure Cosmos DB [示例](http://go.microsoft.com/fwlink/?LinkID=402386)创建自己的 Azure Cosmos DB 应用程序。

<a name="NextSteps"></a>

## <a name="next-steps"></a>后续步骤
祝贺你！ 你已使用 Azure Resource Manager 模板部署了 Azure Cosmos DB、应用服务 Web 应用以及示例 Web 应用程序。

* 若要了解有关 Azure Cosmos DB 的详细信息，请单击[此处](http://azure.com/docdb)。
* 若要了解有关 Azure 应用服务 Web 应用的详细信息，请单击[此处](http://go.microsoft.com/fwlink/?LinkId=325362)。
* 若要了解有关 Azure Resource Manager 模板的详细信息，请单击[此处](https://msdn.microsoft.com/library/azure/dn790549.aspx)。

## <a name="whats-changed"></a>发生的更改
* 有关从网站更改为应用服务的指南，请参阅 [Azure 应用服务及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)
* 有关从旧门户更改为新门户的指南，请参阅：[有关在 Azure 经典门户中导航的参考](http://go.microsoft.com/fwlink/?LinkId=529715)

> [!NOTE]
> 如果您想要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到 [试用应用服务](http://go.microsoft.com/fwlink/?LinkId=523751)，您可以在应用服务中立即创建一个生存期较短的入门 Web 应用。 你不需要使用信用卡，也不需要做出承诺。
> 
> 


