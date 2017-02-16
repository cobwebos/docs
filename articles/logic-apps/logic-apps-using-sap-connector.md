---
title: "在 Azure 逻辑应用中将 SAP 连接器与本地数据网关配合使用 | Microsoft Docs"
description: "通过逻辑应用可轻松连接到包含在工作流中的本地 SAP 系统。"
services: logic-apps
documentationcenter: dev-center-name
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 12584e9aa9c1400aba040320bd3a5f3ba3017470
ms.openlocfilehash: a483f527d15372c94fe7fe813dc49c3d6423e1e3


---
# <a name="get-started-with-the-sap-connector"></a>SAP 连接器入门 

混合云连接是逻辑应用的核心。 通过本地数据网关可管理数据并从逻辑应用安全访问本地资源。 本文演示如何通过一个简单方案连接到本地 SAP 系统：在 HTTP 上请求 IDOC 并发送回响应。    

 > [!NOTE]
 > 此 SAP 连接器支持以下系统 阻止请求超过 90 秒的逻辑应用中当前存在超时限制 文件选取器中显示的字段数当前存在限制（可手动添加路径）
 >
 >

## <a name="prerequisites"></a>先决条件
- 安装并配置最新的[本地数据网关](https://www.microsoft.com/en-us/download/details.aspx?id=53127)。  

    如果尚未安装，请安装最新的本地数据网关版本 1.15.6150.1 或更高版本。 有关说明，请参阅[此文](http://aka.ms/logicapps-gateway)。 网关必须先安装在本地计算机上，然后你才能继续执行剩余步骤。

- 在安装有通用网关的同一计算机上下载并安装最新的 SAP 客户端库

## <a name="use-sap-connector"></a>使用 SAP 连接器

1. 创建 HTTP 请求触发器，然后通过在搜索字段中键入“SAP”选择 SAP 连接器操作。    
 ![搜索 SAP](media/logic-apps-using-sap-connector/picture1.png)

2. 选择“SAP”（ApplicationHost 或 MessagingHost，具体取决于 SAP 设置），然后使用通用网关为其创建连接。
 - 如果没有现有连接，系统会提示创建连接。
 - 勾选“通过本地数据网关连接”选项，勾选此复选框后会出现其他字段。
 - 指定连接名称字符串
 - 通过选择上一步中安装的网关或选择“安装网关”，安装新网关。   
 ![向 SAP 添加连接字符串](media/logic-apps-using-sap-connector/picture2.png)   
  
  > [!NOTE]
  > 存在两种不同的 SAP 连接，一种适用于[应用程序主机](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)，另一种适用于[消息主机](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)
  >
  >

3. 提供所有详细信息后，单击“创建”。 逻辑应用将配置并测试连接，确保其可正常工作。 一切正常就绪后，可看到先前选择的卡的选项，使用文件选取器找到正确的 IDOC 类别或手动键入路径，然后在正文字段中选择 HTTP 响应。    
 ![SAP 操作](media/logic-apps-using-sap-connector/picture3.png)

4. 通过添加新操作创建 HTTP 响应，响应消息应来自 SAP 输出

5. 保存逻辑应用，并通过 HTTP 触发器 URL 发送 IDOC 对其进行测试

6. 发送 IDOC 后，请等待逻辑应用的响应   

  > [!TIP]
  > 查看如何[监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。
  >
  >

7. 所有操作完成后，使用 SAP 连接器的逻辑应用可正常运行。 可开始浏览其提供的其他功能：
  - BAPI
  - RFC

## <a name="next-steps"></a>后续步骤
- 了解 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)。 
- 创建逻辑应用的[本地连接](../logic-apps/logic-apps-gateway-connection.md)。


<!--HONumber=Jan17_HO3-->


