---
title: "架构和 Enterprise Integration Pack 概述 | Microsoft Docs"
description: "了解如何将架构与 Enterprise Integration Pack 和逻辑应用一起使用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 2ad6ad4dcab9a7296dfc6fb6227c510daee85222


---
# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>了解架构和 Enterprise Integration Pack
## <a name="why-use-a-schema"></a>为什么要使用架构？
使用架构可确认收到的 XML 文档是否有效，即是否包含采用预定义格式的预期数据。 架构用于验证在 B2B 方案中交换的消息。

## <a name="add-a-schema"></a>添加架构
通过 Azure 门户：  

1. 选择“更多服务”。  
   ![Azure 门户的屏幕截图，其中突出显示了“更多服务”](media/logic-apps-enterprise-integration-schemas/overview-11.png)    
2. 在筛选器搜索框中输入**集成**，然后从结果列表中选择“集成帐户”。     
   ![筛选器搜索框的屏幕截图](media/logic-apps-enterprise-integration-schemas/overview-21.png)  
3. 选择要将架构添加到其中的**集成帐户**。    
   ![集成帐户列表的屏幕截图](media/logic-apps-enterprise-integration-schemas/overview-31.png)  
4. 选择“架构”磁贴。  
   ![IEP 集成帐户的屏幕截图，其中突出显示了“架构”](media/logic-apps-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>添加小于 2 MB 的架构文件
1. 在打开的“架构”边栏选项卡（采用之前的步骤打开）中，选择“添加”。  
   ![“架构”边栏选项卡的屏幕截图，其中突出显示了“添加”按钮](media/logic-apps-enterprise-integration-schemas/schema-21.png)  
2. 输入架构的名称。 然后，若要上传架构文件，请选择“架构”文本框旁的文件夹图标。 上传过程完成之后，选择“确定”。    
   ![“添加架构”的屏幕截图，其中突出显示了“小型文件”](media/logic-apps-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>添加大于 2 MB 的架构文件（最大为 8 MB）
这一过程取决于 blob 容器的访问级别：**公共**或**没有匿名访问**。 若要确定此访问级别，请在“Blob 容器”下的“Azure 存储资源管理器”中，选择所需 blob 容器。 选择“安全”，然后选择“访问级别”选项卡。

1. 如果 blob 安全访问级别为“公共”，请按下列步骤操作。  
   ![Azure 存储资源管理器屏幕截图，其中突出显示了“Blob 容器”、“安全”和“公共”](media/logic-apps-enterprise-integration-schemas/blob-public.png)  
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 将架构上传到存储并复制该 URI。  
    ![存储帐户的屏幕截图，其中突出显示了 URI](media/logic-apps-enterprise-integration-schemas/schema-blob.png)  
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“添加架构”中选择“大型文件”，并在“内容 URI”文本框中输入 URI。  
    ![“架构”屏幕截图，其中突出显示了“添加”按钮和“大型文件”](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)  
2. 如果 blob 安全访问级别为“没有匿名访问”，请按下列步骤操作。  
   ![Azure 存储资源管理器屏幕截图，其中突出显示了“Blob 容器”、“安全”和“没有匿名访问”](media/logic-apps-enterprise-integration-schemas/blob-1.png)  
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 将架构上传到存储。  
    ![存储帐户屏幕截图](media/logic-apps-enterprise-integration-schemas/blob-3.png)
   
    b.保留“数据库类型”设置，即设置为“共享”。 为架构生成共享访问签名。  
    ![存储帐户屏幕截图，其中突出显示了“共享访问签名”选项卡](media/logic-apps-enterprise-integration-schemas/blob-2.png)
   
    c. 在“添加架构”中选择“大型文件”，并在“内容 URI”文本框中输入共享访问签名 URI。  
    ![“架构”屏幕截图，其中突出显示了“添加”按钮和“大型文件”](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)  
3. 在 EIP 集成帐户的“架构”边栏选项卡中，应会出现新添加的架构。  
   ![ 集成帐户的屏幕截图，其中突出显示了“架构”和新架构](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>编辑架构
1. 选择“架构”磁贴。  
2. 从打开的“架构”边栏选项卡中选择要编辑的架构。
3. 在“架构”边栏选项卡上，选择“编辑”。  
   ![“架构”边栏选项卡的屏幕截图](media/logic-apps-enterprise-integration-schemas/edit-12.png)    
4. 使用打开的文件选取器对话框选择要编辑的架构文件。
5. 在文件选取器中选择“打开”。  
   ![文件选取器的屏幕截图](media/logic-apps-enterprise-integration-schemas/edit-31.png)  
6. 你会收到指示上传成功的通知。  

## <a name="delete-schemas"></a>删除架构
1. 选择“架构”磁贴。  
2. 从打开的“架构”边栏选项卡中选择要删除的架构。  
3. 在“架构”边栏选项卡上，选择“删除”。
   ![“架构”边栏选项卡的屏幕截图](media/logic-apps-enterprise-integration-schemas/delete-12.png)  
4. 选择“是”确认选择。  
   ![“删除架构”确认消息的屏幕截图](media/logic-apps-enterprise-integration-schemas/delete-21.png)  
5. 最后请注意，“架构”边栏选项卡中的架构列表会刷新，且不再列出删除的架构。  
   ![EIP 集成帐户的屏幕截图，其中突出显示了“架构”](media/logic-apps-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>后续步骤
* [了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "Learn about the enterprise integration pack")。  




<!--HONumber=Jan17_HO3-->


