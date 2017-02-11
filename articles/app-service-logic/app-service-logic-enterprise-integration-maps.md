---
title: "映射 Enterprise Integration Pack 概述 | Microsoft Docs"
description: "了解如何将映射与 Enterprise Integration Pack 和逻辑应用一起使用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 542278b38584d8e87a5dbf88af020dee3941cfa1


---
# <a name="learn-about-maps-and-the-enterprise-integration-pack"></a>了解映射和 Enterprise Integration Pack
## <a name="overview"></a>概述
企业集成使用映射将 XML 数据从一种格式转换为另一种格式。 

## <a name="what-is-a-map"></a>什么是映射？
映射是一个 XML 文档，用于定义文档中的哪些数据应转换为另一种格式。 

## <a name="why-use-maps"></a>为何使用映射？
我们假设你从对日期使用 YYYMMDD 格式的客户定期收到 B2B 订单或发票。 但是在组织中，你采用 MMDDYYY 格式存储日期。 在将订单或发票详细信息存储在客户活动数据库中之前，可以使用映射将 YYYMMDD 日期格式转换为 MMDDYYY。

## <a name="how-do-i-create-a-map"></a>如何创建映射？
通过 Visual Studio 2015 的 [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "了解 enterprise integration pack") 可以创建 Biztalk 集成项目。  通过创建集成映射文件可以直观地在两个 XML 架构文件之间映射项目。  在生成此项目之后，会输出 XSLT 文档。

## <a name="how-to-upload-a-map"></a>如何上传映射？
通过 Azure 门户：  

1. 选择“浏览”  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. 在筛选器搜索框中输入**集成**，然后从结果列表中选择“集成帐户”     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. 选择要将映射添加到其中的**集成帐户**  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. 选择“映射”磁贴  
   ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)  
5. 在打开的“映射”边栏选项卡中，选择“添加”按钮  
   ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)  
6. 为映射输入“名称”，然后，若要上传映射文件，请选择“映射”文本框右侧的文件夹图标。 上传过程完成之后，选择“确定”按钮。  
   ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)  
7. 映射现在将添加到集成帐户中。 你会收到指示映射文件添加成功或失败的屏幕上通知。 收到该通知之后，选择“映射”磁贴，你随后会在“映射”边栏选项卡中看到新添加的映射：    
   ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)  

## <a name="how-to-edit-a-map"></a>如何编辑映射？
若要编辑映射，必须上传包含所需更改的新映射文件。 可以先下载映射，然后编辑它。 

按照以下步骤可上传用于替换现有映射的新映射：  

1. 选择“映射”磁贴  
2. 在“映射”边栏选项卡打开时，选择要编辑的映射  
3. 在“映射”边栏选项卡中，选择“更新”链接  
   ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)   
4. 使用打开的文件选取器对话框选择要上传的映射文件，然后在文件选取器中选择“打开”   
   ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)   
5. 上传映射之后，你会收到通知弹出窗口。    

## <a name="how-to-delete-a-map"></a>如何删除映射？
1. 选择“映射”磁贴  
2. 在“映射”边栏选项卡打开时，选择要删除的映射  
3. 选择“删除”链接    
   ![](./media/app-service-logic-enterprise-integration-maps/delete.png)   
4. 确认你确实要删除映射。  
   ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)   

## <a name="next-steps"></a>后续步骤
* [了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md "了解 Enterprise Integration Pack")  
* [了解有关协议的详细信息](app-service-logic-enterprise-integration-agreements.md "了解企业集成协议")  
* [了解有关转换的详细信息](app-service-logic-enterprise-integration-transform.md "了解企业集成转换")  




<!--HONumber=Nov16_HO3-->


