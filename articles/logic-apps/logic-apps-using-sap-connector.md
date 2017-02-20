---
title: "在 Azure 逻辑应用中连接到本地 SAP 系统 | Microsoft Docs"
description: "使用本地数据网关在逻辑应用工作流中连接到本地 SAP 系统"
services: logic-apps
documentationcenter: 
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 72ac4936c656847fa07f1c1a37d6ddbf4ec1acf4
ms.openlocfilehash: 62b30acf324a5ed6a1b817157c86575d83b4104e


---
# <a name="use-the-sap-connector-in-your-logic-app"></a>在逻辑应用中使用 SAP 连接器 

通过本地数据网关可管理数据并安全访问本地资源。 使用本主题，连接到本地 SAP 系统以通过 HTTP 请求 IDOC 并发送回响应。    

 > [!NOTE]
> 当前限制：
 > - 如果请求超过 90 秒，则逻辑应用超时。 在此方案中，可能会阻止请求。 
 > - 文件选取器未显示所有可用字段。 在此方案中，可以手动添加路径。

## <a name="prerequisites"></a>先决条件
- 安装并配置最新的[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)版本1.15.6150.1 或更高版本。 [如何在逻辑应用中连接到本地数据网关](http://aka.ms/logicapps-gateway)中列出了相关步骤。 必须先在本地计算机上安装网关，然后才能继续后续步骤。

- 在安装有数据网关的同一计算机上下载并安装最新的 SAP 客户端库。 使用下方任意 SAP 版本： 
    - SAP Server
        - SAP ECC 6.0 Unicode
        - SAP ECC 6.0 Unicode（含 EHP 4.0）
        - SAP ECC 6.0（含 EHP 7.0）和所有 EHP 早期版本
 
    - SAP 客户端
        - SAP RFC SDK 7.20 UNICODE
        - SAP.NET 连接器 (NCo) 3.0

## <a name="add-the-sap-connector"></a>添加 SAP 连接器

SAP 连接器包含操作。 但不包含任何触发器。 因此，请在工作流开始时使用其他触发器。 

1. 添加请求/响应触发器，然后选择“新建步骤”。
2. 选择“添加操作”，然后通过在搜索字段中键入 `SAP` 选择 SAP 连接器：    
 ![选择 SAP 应用程序服务器或 SAP 消息服务器](media/logic-apps-using-sap-connector/picture1.png)

3. 根据 SAP 设置，选择 **SAP** [ 应用程序服务器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)或[消息服务器](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。 如果没有现有连接，系统会提示创建连接： 
    1. 选择“通过本地数据网关连接”，然后输入 SAP 系统的详细信息：   
 ![向 SAP 添加连接字符串](media/logic-apps-using-sap-connector/picture2.png)  
    2. 选择现有**网关**。 或者，选择“安装网关”以安装新网关：    
 ![安装新网关](media/logic-apps-using-sap-connector/install-gateway.png)
  
    3. 输入所有详细信息后，选择“创建”。 逻辑应用将配置并测试连接，确保其可正常工作。

4. 输入 SAP 连接的名称。

5. 现在可以使用不同的 SAP 选项。 使用文件选取器查找 IDOC 类别。 或者手动键入路径，然后在“正文”字段中选择 HTTP 响应：     
 ![SAP 操作](media/logic-apps-using-sap-connector/picture3.png)

6. 通过添加新操作创建 HTTP 响应。 响应消息应来自 SAP 输出。

7. 保存逻辑应用。 通过 HTTP 触发器 URL 发送 IDOC 对其进行测试。 发送 IDOC 后，请等待逻辑应用的响应：   

  > [!TIP]
  > 查看如何[监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

现在，SAP 连接器已添加到逻辑应用，开始探索其他功能：

  - BAPI
  - RFC

## <a name="next-steps"></a>后续步骤
- 了解如何在 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 中验证、转换，以及其他类似 BizTalk 的功能。 
- 创建逻辑应用的[本地连接](../logic-apps/logic-apps-gateway-connection.md)。



<!--HONumber=Feb17_HO1-->


