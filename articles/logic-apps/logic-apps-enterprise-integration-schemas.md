---
title: "XML 验证的架构 - Azure 逻辑应用 | Microsoft 文档"
description: "使用 Azure 逻辑应用和 Enterprise Integration Pack 的架构验证 XML 文档"
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
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 7884032fd4b5a72ca7e2f2dc2830b0a7c2220e94
ms.openlocfilehash: 5d8ddd4449d3b160dbca7496c801e75dcf4a44e5


---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>使用 Azure 逻辑应用和 Enterprise Integration Pack 的架构验证 XML

架构可确认收到的 XML 文档有效且包含采用预定义格式的预期数据。 架构还可帮助验证在 B2B 方案中交换的消息。

## <a name="add-a-schema"></a>添加架构

1. 在 Azure 门户中，选择“更多服务”。

    ![Azure 门户，“更多服务”](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. 在筛选器搜索框中输入**集成**，然后从结果列表中选择“集成帐户”。

    ![筛选器搜索框](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. 选择要将架构添加到的**集成帐户**。

    ![集成帐户列表](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. 选择“架构”磁贴。

    ![示例集成帐户，“架构”](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>添加小于 2 MB 的架构文件

1. 在打开的“架构”边栏选项卡（在前面的步骤中打开）中，选择“添加”。

    ![“架构”边栏选项卡，“添加”](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. 输入架构的名称。 通过选择“架构”框旁的文件夹图标上载架构文件。 上载过程完成之后，选择“确定”。

    ![“添加架构”的屏幕截图，其中突出显示了“小型文件”](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>添加大于 2 MB 的架构文件（最大为 8 MB）

这些步骤因 blob 容器的访问级别（**公共**或**没有匿名访问**）而有所不同。

**确定此访问级别**

1.  打开 **Azure 存储资源管理器**。 

2.  在“Blob 容器”下，选择所需的 blob 容器。 

3.  选择“安全性”、“访问级别”。

如果 blob 安全访问级别为“公共”，请按下列步骤操作。

![Azure 存储资源管理器，其中突出显示了“Blob 容器”、“安全性”和“公共”](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. 将架构上载到存储帐户并复制该 URI。

    ![存储帐户，其中突出显示了 URI](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. 在“添加架构”中选择“大型文件”，并在“内容 URI”文本框中输入 URI。

    ![架构，其中突出显示了“添加”按钮和“大型文件”](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

如果 blob 安全访问级别为“没有匿名访问”，请按下列步骤操作。

![Azure 存储资源管理器，其中突出显示了“Blob 容器”、“安全性”和“没有匿名访问”](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. 将架构上载到存储帐户。

    ![存储帐户](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. 为架构生成共享访问签名。

    ![存储帐户，其中突出显示了“共享访问签名”选项卡](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. 在“添加架构”中选择“大型文件”，并在“内容 URI”文本框中输入共享访问签名 URI。

    ![架构，其中突出显示了“添加”按钮和“大型文件”](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. 在集成帐户的“架构”边栏选项卡中，应会出现新添加的架构。

    ![集成帐户，其中突出显示了“架构”和新架构](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>编辑架构

1. 选择“架构”磁贴。

2. 在“架构”边栏选项卡打开后，选择要编辑的架构。

3. 在“架构”边栏选项卡上，选择“编辑”。

    ![“架构”边栏选项卡](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. 选择要编辑的架构文件，然后选择“打开”。

    ![打开要编辑的架构文件](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure 会显示一条消息，指示该架构已成功上载。

## <a name="delete-schemas"></a>删除架构

1. 选择“架构”磁贴。

2. 在“架构”边栏选项卡打开后，选择要删除的架构。

3. 在“架构”边栏选项卡上，选择“删除”。

    ![“架构”边栏选项卡](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. 若要确认想要删除所选架构，请选择“是”。

    ![“删除架构”确认消息](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    在“架构”边栏选项卡中，将刷新架构列表，其中不再包含已删除的架构。

    ![集成帐户，其中突出显示了“架构”](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>后续步骤
* [了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "Learn about the enterprise integration pack")。  




<!--HONumber=Feb17_HO1-->


