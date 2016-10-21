<properties 
   pageTitle="在 Azure App Service 中创建贸易合作伙伴协议 |Microsoft Azure" 
   description="创建贸易合作伙伴协议" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
	ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# 创建贸易合作伙伴协议   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

贸易合作伙伴是 B2B（企业到企业）通信中涉及的实体。当两个合作伙伴建立关系时，将其称为*协议*。定义的协议基于两个合作伙伴希望实现的通信，是特定的协议或传输。Azure App Service 支持的多种 B2B 协议和传输包括：

- AS2（适用性声明 2）
- EDIFACT（联合国/行政、商业和运输电子数据交换 (UN/EDIFACT)）
- X12 (ASC X12)

### 支持 B2B 方案的 BizTalk API 应用
下面的 API 应用支持这些功能在 Azure 门户中使用丰富而直观的体验：


## BizTalk 贸易合作伙伴管理 (TPM)
- 创建和管理合作伙伴、配置文件和标识
- 存储和管理 EDI 架构
- 存储和管理证书（在 AS2 协议中使用）
- 创建和管理 AS2 协议
- 创建和管理 EDIFACT 协议（包括发送端的批处理）
- 创建和管理 X12 协议（包括发送端的批处理）

![][1]


## AS2 连接器
- 按照相关 TPM API 应用实例中的定义执行 AS2 协议
- 显示 AS2 处理/跟踪信息以进行故障排除


## BizTalk EDIFACT
- 按照相关 TPM API 应用实例中的定义执行 EDIFACT 协议
- 显示 EDIFACT 处理/跟踪信息以进行故障排除
- 按照相关 TPM API 应用实例的 EDIFACT 协议中的定义提供批量状态（启动和停止）管理


## BizTalk X12
- 按照相关 TPM API 应用实例中的定义执行 X12 协议
- 显示 X12 处理/跟踪信息以进行故障排除
- 按照相关 TPM API 应用实例的 X12 协议中的定义提供批量状态（启动和停止）管理

如前面所述，AS2、X12 和 EDIFACT API 应用需要使用 TPM API 应用才能按预期运行。


## 入门
要创建贸易合作伙伴协议，请执行以下操作：

1. 为 **BizTalk 贸易合作伙伴管理**连接器创建一个实例。这需要使用空的 SQL 数据库才能正常运行。开始之前，确保有一个空的可供使用的数据库。
2. 按照协议的要求上载架构和证书。为此，请浏览创建的 TPM 实例并单步执行“架构”和/或“证书”部分
3. 浏览到创建的 TPM 实例并单步执行“**合作伙伴**”部分
4. 根据需要创建合作伙伴。还要适当地编辑配置文件并添加所需的标识
5. 现在，使用“**协议**”部分创建协议。创建协议时，必须选择将使用的协议。剩余的配置选项基于所选的协议。

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 

<!---HONumber=AcomDC_0921_2016-->