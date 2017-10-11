---
title: "使用 XSLT 映射转换 XML - Azure 逻辑应用 | Microsoft 文档"
description: "添加 XSLT 映射以使用 Azure 逻辑应用和 Enterprise Integration Pack 转换 XML 数据"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4445a84a6c6425110e7d705019a28b5cc5447046
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="add-maps-for-xml-data-transform"></a>添加映射以进行 XML 数据转换

企业集成使用映射将 XML 数据在格式之间转换。 映射是一个 XML 文档，用于定义文档中应转换为另一种格式的数据。 

## <a name="why-use-maps"></a>为何使用映射？

假设定期从日期使用 YYYMMDD 格式的客户接收 B2B 订单或发票。 但组织采用 MMDDYYY 格式存储日期。 在将订单或发票详细信息存储在客户活动数据库中之前，可以使用映射将 YYYMMDD 日期格式转换为 MMDDYYY。

## <a name="how-do-i-create-a-map"></a>如何创建映射？

可以使用 Visual Studio 2015 的 [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "了解 enterprise integration pack") 创建 BizTalk 集成项目。 然后可以创建允许您以可视方式映射项两个 XML 架构文件之间的集成映射文件。 生成此项目后，会获得 XSLT 文档。

## <a name="how-do-i-add-a-map"></a>如何添加映射？

1. 在 Azure 门户中，选择“浏览”。

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. 在筛选器搜索框中输入“集成”，并从结果列表中选择“集成帐户”。

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. 选择要将映射添加到的集成帐户。

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. 选择“映射”磁贴。

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. 在“映射”边栏选项卡打开后，选择“添加”。

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. 为映射输入**名称**。 若要上传映射文件，请选择“映射”文本框右侧的文件夹图标。 上传过程完成之后，选择“确定”。

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Azure 将映射添加到集成帐户后，会在屏幕上显示消息，说明是否已添加映射文件。 看到此消息后，请选择“映射”磁贴，以便查看新添加的映射。

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)

## <a name="how-do-i-edit-a-map"></a>如何编辑映射？

必须上传包含所需更改的新映射文件。 可以先下载映射以进行编辑。

若要上传用于替换现有映射的新映射，请执行以下步骤。

1. 选择“映射”磁贴。

2. 在“映射”边栏选项卡打开后，选择要编辑的映射。

3. 在“映射”边栏选项卡上，选择“更新”。

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. 在文件选取器中，选择要上载的映射文件，并选择“打开”。

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>如何删除映射？

1. 选择“映射”磁贴。

2. 在“映射”边栏选项卡打开后，选择要删除的映射。

3. 选择“删除”。

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. 确认要删除该映射。

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>后续步骤
* [了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")  
* [了解有关协议的详细信息](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  
* [了解有关转换的详细信息](logic-apps-enterprise-integration-transform.md "了解企业集成转换")  

