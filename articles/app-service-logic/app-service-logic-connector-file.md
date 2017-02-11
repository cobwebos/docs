---
title: "在逻辑应用中使用 File 连接器 | Microsoft Docs"
description: "如何创建并配置文件连接器或 API 应用，以及在 Azure App Service 中的逻辑应用中使用它"
author: rajeshramabathiran
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07ceb81a-f8f6-4901-a7a2-06a9ac47efd5
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: rajram
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 61b3644214a7cb067dbb59e8fbd1464af78ae3e1


---
# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>文件连接器入门及将其添加到逻辑应用
> [!NOTE]
> 此文章版本适用于 2014-12-01 预览架构版的逻辑应用。
> 
> 

连接到文件系统以对主机上的文件执行上载、下载和更多操作。 逻辑应用可以基于各种数据源触发，并提供连接器来获取和处理数据。 可以在逻辑应用中将文件连接器添加到业务工作流，并在此工作流中处理数据。 

文件连接器对与主机文件系统的混合连接使用混合连接管理器。

## <a name="creating-a-file-connector-for-your-logic-app"></a>为逻辑应用创建文件连接器
若要使用文件连接器，需要首先创建文件连接器 API 应用的一个实例。 可以按以下步骤来完成：

1. 使用 Azure 门户左侧的“+ 新建”选项打开 Azure 应用商店。
2. 搜索“文件连接器”。
3. 从搜索结果中选择“文件连接器（预览版）”。
4. 单击“创建”按钮。
5. 配置此文件连接器，如下：  
   ![][1]
   
   * **名称** — 为文件连接器命名
   * **包设置**
     * **根文件夹** — 指定主机上的根文件夹路径。 例如 D:\FileConnectorTest
     * **服务总线连接字符串** — 提供服务总线连接字符串。 请确保服务总线命名空间类型为标准和非基本，以允许使用服务总线中继。  服务总线中继用于连接混合连接管理器。
   * **应用服务计划** — 选择或创建应用服务计划
   * **定价层** — 选择连接器的定价层
   * **资源组** — 选择或创建连接器所在的资源组
   * **订阅** — 选择要在其中创建连接器的订阅
   * **位置** — 选择要将连接器部署到的地理位置
6. 单击“创建”。 即可创建一个新的文件连接器

## <a name="configure-hybrid-connection-manager"></a>配置混合连接管理器
创建 API 应用实例后，浏览到其仪表板。  此操作可通过以下方式完成：单击“浏览”>“API 应用”，然后选择文件连接器 API 应用。  此处需配置混合连接管理器。
有关配置混合连接管理器和对其进行故障排除的详细信息，请参阅[使用混合连接管理器]。

## <a name="using-the-file-connector-in-your-logic-app"></a>在逻辑应用中使用文件连接器
创建 API 应用后，就可以在逻辑应用中将文件连接器用作操作。 若要实现此目的，需要：

1. 创建新的逻辑应用，并选择具有文件连接器的同一资源组。 按照[创建新的逻辑应用]中的说明。
2. 在创建的逻辑应用中打开“触发器和操作”，以打开“逻辑应用设计器”并配置流。
3. 文件连接器显示在右侧的库中的“此资源组中的 API 应用”部分。
4. 单击“文件连接器”可将文件连接器 API 应用放到编辑器中。 文件连接器公开一个触发器和 4 个操作：  
   ![][5]
5. 上述各项公开特定属性。 下图列出了触发器和获取文件操作的属性：  
   ![][6]
6. 配置了这些属性后，就可以在流中使用触发器和操作。 同样，还可以配置其他操作。

> [!NOTE]
> 从文件夹成功读取文件后，文件触发器将删除该文件。
> 
> 

## <a name="file-connector-rest-apis"></a>文件连接器 REST API
若要使用逻辑应用外部的连接器，则可以利用该连接器公开的 REST API。 可以通过“浏览”->“API 应用”->“文件连接器”查看此 API 定义。 现在单击摘要部分下面的 API 定义镜头可查看此连接器公开的所有 API：  
![][7]

可以在[文件连接器 API 定义]中找到 API 的详细信息。

## <a name="do-more-with-your-connector"></a>使用连接器执行更多操作
现在已经创建了连接器，可以将其添加到使用逻辑应用的业务工作流中。 请参阅[什么是逻辑应用？](app-service-logic-what-are-logic-apps.md)。

> [!NOTE]
> 若要在注册 Azure 帐户前开始使用 Azure 逻辑应用，请转至[试用逻辑应用](https://tryappservice.azure.com/?appservice=logic)，可立即在应用服务中创建临时简易版逻辑应用。 你不需要使用信用卡，也不需要做出承诺。
> 
> 

请查看 [Connectors and API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766)（连接器和 API 应用参考）中的 Swagger REST API 参考。

你还可以查看连接器的性能统计信息和控制安全性。 请参阅[管理和监视内置 API 应用和连接器](app-service-logic-monitor-your-connectors.md)。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[创建新的逻辑应用]: app-service-logic-create-a-logic-app.md
[文件连接器 API 定义]: https://msdn.microsoft.com/library/dn936296.aspx
[使用混合连接管理器]: app-service-logic-hybrid-connection-manager.md



<!--HONumber=Nov16_HO3-->


