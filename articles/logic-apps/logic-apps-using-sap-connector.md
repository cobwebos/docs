---
title: "在 Azure 逻辑应用中连接到本地 SAP 系统 | Microsoft Docs"
description: "通过本地数据网关从逻辑应用工作流连接到本地 SAP 系统"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3fea93f558d5a4ef62550fd1f6486903cb812930
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>使用 SAP 连接器从逻辑应用连接到本地 SAP 系统 

通过本地数据网关可管理数据并安全访问本地资源。 本主题说明如何将逻辑应用连接到本地 SAP 系统。 在此示例中，逻辑应用通过 HTTP 请求 IDOC 并返回响应。    

> [!NOTE]
> 当前限制： 
> - 如果未在[请求超时限制](./logic-apps-limits-and-config.md)所规定的时间内完成响应所需的所有步骤，逻辑应用将超时。 在此方案中，可能会阻止请求。 
> - 文件选取器未显示所有可用字段。 在此方案中，可以手动添加路径。

## <a name="prerequisites"></a>先决条件

- 安装并配置最新的[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)版本1.15.6150.1 或更高版本。 [如何在逻辑应用中连接到本地数据网关](http://aka.ms/logicapps-gateway)中列出了相关步骤。 必须先在本地计算机上安装网关，才能继续后续步骤。

- 在安装有数据网关的同一计算机上下载并安装最新的 SAP 客户端库。 使用下方任意 SAP 版本： 
    - SAP Server
        - 支持 .NET 连接器 (NCo) 3.0 的任何 SAP Server
 
    - SAP 客户端
        - SAP.NET 连接器 (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>添加用于连接到 SAP 系统的触发器和操作

SAP 连接器包含操作，但不包含触发器。 因此，必须在工作流开始时使用其他触发器。 

1. 添加请求/响应触发器，并选择“新建步骤”。

2. 选择“添加操作”，并通过在搜索字段中键入 `SAP` 选择 SAP 连接器：    

     ![选择 SAP 应用程序服务器或 SAP 消息服务器](media/logic-apps-using-sap-connector/sap-action.png)

3. 根据 SAP 设置，选择 [SAP 应用程序服务器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)或 [SAP 消息服务器](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。 如果没有现有连接，系统会提示创建连接。

   1. 选择“通过本地数据网关连接”，并输入 SAP 系统的详细信息：   

       ![向 SAP 添加连接字符串](media/logic-apps-using-sap-connector/picture2.png)  

   2. 在“网关”下，选择现有网关，若要安装新网关，请选择“安装网关”。

        ![安装新网关](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. 输入所有详细信息后，选择“创建”。 
   逻辑应用会配置和测试连接，以确保连接正常运行。

4. 输入 SAP 连接的名称。

5. 现在可以使用不同的 SAP 选项。 若要查找 IDOC 类别，请从列表中选择。 或者手动键入路径，并在“正文”字段中选择 HTTP 响应：

     ![SAP 操作](media/logic-apps-using-sap-connector/picture3.png)

6. 添加用于创建 **HTTP 响应**的操作。 响应消息应来自 SAP 输出。

7. 保存逻辑应用。 通过 HTTP 触发器 URL 发送 IDOC 对其进行测试。 发送 IDOC 后，请等待逻辑应用的响应：   

     > [!TIP]
     > 查看如何[监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

现在，SAP 连接器已添加到逻辑应用，开始探索其他功能：

- BAPI
- RFC

## <a name="get-help"></a>获取帮助

若要提问、解答问题和了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

为了帮助我们改进 Azure 逻辑应用和连接器，敬请在 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)上投票或发表看法。

## <a name="next-steps"></a>后续步骤

- 了解如何在 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 中验证、转换，以及其他类似 BizTalk 的功能。 
- 从逻辑应用[连接到本地数据](../logic-apps/logic-apps-gateway-connection.md)
