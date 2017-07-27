---
title: "指南：使用 Azure BizTalk 服务处理 EDIFACT 发票 | Microsoft Docs"
description: "如何创建并配置 Box 连接器或 API 应用，以及在 Azure App Service 中的逻辑应用中使用它"
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b7ad7b91c6b836f26b45959ef65a99666a4bf69a
ms.contentlocale: zh-cn
ms.lasthandoff: 12/08/2016

---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>指南：使用 Azure BizTalk 服务处理 EDIFACT 发票

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

可使用 BizTalk 服务门户配置和部署 X12 和 EDIFACT 协议。 在本教程中，我们着重介绍如何创建用于贸易合作伙伴之间交换发票的 EDIFACT 协议。 本教程是围绕一个端到端业务解决方案编写的，该解决方案涉及两个互换 EDIFACT 消息的贸易合作伙伴（Northwind 和 Contoso）。  

## <a name="sample-based-on-this-tutorial"></a>基于本教程的示例
本教程围绕一个示例编写，即**使用 BizTalk 服务发送 EDIFACT 发票**，可从 [MSDN 代码库](http://go.microsoft.com/fwlink/?LinkId=401005)下载该示例。 可使用该示例，并浏览本教程以了解其生成方法。 也可使用本教程从头创建自己的解决方案。 本教程主要针对第二种方法，介绍如何生成此解决方案。 此外，本教程尽量与示例保持一致，并对各项目（例如架构、转换）使用与示例中相同的名称。  

> [!NOTE]
> 由于此解决方案涉及从 EAI 桥向 EDI 桥发送消息，因此会重复使用 [BizTalk Services Bridge chaining sample](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104)（BizTalk 服务桥链接示例）示例。  
> 
> 

## <a name="what-does-the-solution-do"></a>此解决方案有哪些功能？
在此解决方案中，Northwind 从 Contoso 接收 EDIFACT 发票。 这些发票未采用标准 EDI 格式。 因此，在向 Northwind 发送该发票前，必须将它转换为 EDIFACT 发票（又称 INVOIC）文档。 接收时，Northwind 必须处理 EDIFACT 发票，并向 Contoso 返回控制消息（又称 CONTRL）。

![][1]  

为实现此业务方案，Contoso 使用 Microsoft Azure BizTalk 服务提供的功能。

* Contoso 使用 EAI 桥将原始发票转换为 EDIFACT INVOIC。
* 然后，EAI 桥将消息发送到（作为 BizTalk 服务门户中所配置协议的一部分部署的）EDI 发送桥。
* EDI 发送桥处理 EDIFACT INVOIC 并将它路由到 Northwind。
* 收到发票后，Northwind 将 CONTRL 消息返回到作为协议的一部分部署的 EDI 接收桥。  

> [!NOTE]
> （可选）此解决方案还可演示如何使用批处理分批发送发票，而不单独发送每张发票。  
> 
> 

为完成该方案，我们使用服务总线队列将发票从 Contoso 发送到 Northwind 或接收来自 Northwind 的回单。 可使用客户端应用程序创建这些队列，此应用程序可供下载且包含在作为本教程一部分的示例包中。  

## <a name="prerequisites"></a>先决条件
* 必须具有一个服务总线命名空间。 有关创建命名空间的说明，请参阅[操作方法：创建或修改服务总线服务命名空间](https://msdn.microsoft.com/library/azure/hh674478.aspx)。 假定你已预配服务总线命名空间，名为 **edifactbts**。
* 必须具有 BizTalk 服务订阅。 有关说明，请参阅[使用 Azure 经典门户创建 BizTalk 服务](http://go.microsoft.com/fwlink/?LinkID=302280)。 本教程假定用户已拥有 BizTalk 服务订阅，名为 **contosowabs**。
* 在 BizTalk 服务门户中注册 BizTalk 服务订阅。 有关说明，请参阅[在 BizTalk 服务门户中注册 BizTalk 服务部署](https://msdn.microsoft.com/library/hh689837.aspx)
* 必须安装 Visual Studio。
* 必须安装 BizTalk 服务 SDK。 可从 [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057) 下载该 SDK  

## <a name="step-1-create-the-service-bus-queues"></a>步骤 1：创建服务总线队列
此解决方案使用服务总线队列在贸易合作伙伴之间交换消息。 Contoso 和 Northwind 从 EAI 和/或 EDI 桥使用消息的位置将消息发送到队列。 此解决方案需要三个服务总线队列：

* **northwindreceive** - Northwind 通过此队列接收来自 Contoso 的发票。
* **contosoreceive** - Contoso 通过此队列接收来自 Northwind 的回单。
* **suspended** - 所有挂起的消息将路由到此队列。 如果消息在处理期间失败，则将被挂起。

可使用示例包中的客户端应用程序创建这些服务总线队列。  

1. 从下载示例的位置，打开“教程：使用 BizTalk 服务 EDI Bridges.sln 发送发票”。
2. 按 **F5** 生成并启动“教程客户端”应用程序。
3. 在此屏幕中，输入服务总线 ACS 命名空间、颁发者名称和颁发者密钥。
   
   ![][2]  
4. 将出现消息框提示：将在服务总线命名空间中创建三个队列。 单击“确定”。
5. 教程客户端开始运行。 打开教程客户端，单击“服务总线” > “服务总线命名空间” > “队列”，验证是否已创建三个队列。  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>步骤 2：创建并部署贸易合作伙伴协议
创建 Contoso 与 Northwind 之间的贸易合作伙伴协议。 贸易合作伙伴协议定义两个业务合作伙伴之间的贸易合同，例如，定义使用的消息架构和使用的消息传送协议等等。贸易合作伙伴协议包括两个 EDI 桥，一个用于将消息发送到贸易合作伙伴（称为 **EDI 发送桥**，另一个用于接收来自贸易合作伙伴的消息（称为 **EDI 接收桥**。

在本解决方案的上下文中，EDI 发送桥对应协议的发送端，并用于将 EDIFACT 发票从 Contoso 发送到 Northwind。 同样，EDI 接收桥对应协议的接收端，并且用于接收来自 Northwind 的回单。  

### <a name="create-the-trading-partners"></a>创建贸易合作伙伴
首先，为 Contoso 和 Northwind 创建贸易合作伙伴。  

1. 在 BizTalk 服务门户中的“合作伙伴”选项卡上，单击“添加”。
2. 在新的合作伙伴页中，输入“Contoso”作为合作伙伴名称，然后单击“保存”。
3. 重复此步骤创建第二个合作伙伴 **Northwind**。  

### <a name="create-the-agreement"></a>创建协议
贸易合作伙伴协议创建于贸易合作伙伴的的业务配置文件之间。 此解决方案使用创建合作伙伴时自动创建的默认合作伙伴配置文件。  

1. 在 BizTalk 服务门户中，单击“协议” > “添加”。
2. 在新协议的“常规设置”页上，指定值，如下图所示，然后单击“继续”。
   
   ![][3]  
   
   单击“继续”后，“接收设置”和“发送设置”两个选项卡变为可用。
3. 创建 Contoso 与 Northwind 之间的发送协议。 此协议控制 Contoso 向 Northwind 发送 EDIFACT 发票的发送方式。
   
   1. 单击“发送设置”。
   2. 保留“入站 URL”、“转换”和“批处理”选项卡上的默认值。
   3. 在“协议”选项卡上的“架构”部分下，上传“EFACT_D93A_INVOIC.xsd”架构。 示例包中提供此架构。
      
      ![][4]  
   4. 在“传输”选项卡上，指定服务总线队列的详细信息。 发送端协议使用 **northwindreceive** 队列向 Northwind 发送 EDIFACT 发票，并使用 **suspended** 队列来路由处理期间失败且已挂起的任何消息。 已在本主题的“步骤 1：创建服务总线队列”中创建了这些队列。
      
      ![][5]  
      
      在“传输设置”>“传输类型” 和“消息挂起设置”>“传输类型”下，选择 Azure 服务总线并提供值，如图所示。
4. 创建 Contoso 与 Northwind 之间的接收协议。 此协议控制 Contoso 如何接收来自 Northwind 的回单。
   
   1. 单击“接收设置”。
   2. 保留“传输”和“转换”选项卡上的默认值。
   3. 在“协议”选项卡上的“架构”部分下，上传“EFACT_4.1_CONTRL.xsd”架构。 示例包中提供此架构。
   4. 在“路由”选项卡上，创建筛选器以确保仅将来自 Northwind 的回单路由到 Contoso。 在“路由设置”下，单击“添加”以创建路由筛选器。
      
      ![][6]  
      
      1. 为“规则名称”、“路由规则”和“路由目标”提供值，如图所示。
      2. 单击“保存” 。
   5. 还是在“路由”选项卡上，指定将挂起的回单（处理期间失败的回单）路由到的位置。 将传输类型设置为 Azure 服务总线、路由目标类型设置为“队列”、向“共享访问签名”(SAS) 对类型进行身份验证、为服务总线命名空间提供 SAS 连接字符串，然后输入队列名称“suspended”。
5. 最后，单击“部署”对协议进行部署。 记录在其中部署发送和接收协议的终结点。
   
   * 在“发送设置”选项卡上，注意“入站 URL”下的终结点。 若要使用 EDI 发送桥将消息从 Contoso 发送到 Northwind，必须向此终结点发送消息。
   * 在“接收设置”选项卡上，注意“传输”下的终结点。 若要使用 EDI 接收桥将消息从 Northwind 发送到 Contoso，则必须向此终结点发送一条消息。  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>步骤 3：创建并部署 BizTalk 服务项目
在上一步中，部署了用于处理 EDIFACT 发票和回单的 EDI 发送和接收协议。 这些协议仅能处理符合标准 EDIFACT 消息架构的消息。 但是，此解决方案的情况是，Contoso 通过内部专有架构将发票发送到 Northwind。 因此，在消息发送到 EDI 发送桥之前，必须先将其从内部架构转换为标准 EDIFACT 发票架构。 BizTalk 服务 EAI 项目将执行此操作。

用于转换消息的 BizTalk 服务项目 **InvoiceProcessingBridge** 也包含在下载的示例中。 该项目包括以下项目：

* **INHOUSEINVOICE.XSD** - 发送到 Northwind 的内部发票的架构。
* **EFACT_D93A_INVOIC.XSD** - 标准 EDIFACT 发票的架构。
* **EFACT_4.1_CONTRL.XSD** - Northwind 发送到 Contoso 的 EDIFACT 回单的架构。
* **INHOUSEINVOICE_to_D93AINVOIC.TRFM** - 将内部发票架构映射到标准 EDIFACT 发票架构的转换。  

### <a name="create-the-biztalk-services-project"></a>创建 BizTalk 服务项目
1. 在 Visual Studio 解决方案中，展开 InvoiceProcessingBridge 项目，然后打开“MessageFlowItinerary.bcs”文件。
2. 单击画布上的任意位置，并设置属性框中的“BizTalk 服务 URL”，以指定 BizTalk 服务订阅名称。 例如，`https://contosowabs.biztalk.windows.net`。
   
   ![][7]  
3. 从工具箱中，将“Xml 单向桥”拖至画布。 将该桥的“实体名称”和“相对地址”属性设置为“ProcessInvoiceBridge”。 双击“ProcessInvoiceBridge”打开桥配置图面。
4. 在“消息类型”框中，单击加号 (**+**) 按钮指定传入消息的架构。 由于 EAI 桥的传入消息始终是内部发票，因此将其设置为“INHOUSEINVOICE”。
   
   ![][8]  
5. 单击“Xml 转换”图形，然后在属性框中，单击“映射”属性的省略号 (**...**) 按钮。 在“映射选择”对话框中，选择“INHOUSEINVOICE_to_D93AINVOIC”转换文件，并单击“确定”。
   
   ![][9]  
6. 返回“MessageFlowItinerary.bcs”，然后从工具箱中将“双向外部服务终结点”拖至“ProcessInvoiceBridge”的右侧。 将其“实体名称”属性设置为“EDIBridge”。
7. 在解决方案资源管理器中，展开“MessageFlowItinerary.bcs”并双击“EDIBridge.config”文件。 用以下内容替换“EDIBridge.config”的内容。
   
   > [!NOTE]
   > 为何需要编辑 .config 文件？ 添加到桥设计器画布的外部服务终结点代表之前部署的 EDI 桥。 EDI 桥是双向桥，具有发送和接收端。 但添加到桥设计器的 EAI 桥是单向桥。 因此，若要处理两个桥的不同消息交换模式，需通过将其设置包含在 .config 文件中来使用自定义桥行为。 此外，自定义行为还处理对 EDI 发送桥终结点的身份验证。此自定义行为可在 [BizTalk 服务桥链接示例 - EAI 到 EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) 中用作单独示例。 此解决方案重复使用该示例。  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. 更新 EDIBridge.config 文件以包括配置详细信息
   
   * 在 *<behaviors>* 下，提供与 BizTalk 服务订阅关联的 ACS 命名空间和密钥。
   * 在 *<client>* 下，提供在其中部署 EDI 发送协议的的终结点。
   
   保存更改并关闭配置文件。
9. 单击工具箱中的“连接器”，并加入“ProcessInvoiceBridge”和“EDIBridge”组件。 选择该连接器，并在“属性”框中，将“筛选条件”设置为“匹配全部”。 此操作可确保将 EAI 桥处理的所有消息路由到 EDI 桥。
   
   ![][10]  
10. 将更改保存到解决方案。  

### <a name="deploy-the-project"></a>部署项目
1. 在创建 BizTalk 服务项目的计算机上，下载并安装 BizTalk 服务订阅的 SSL 证书。 在 BizTalk 服务下，依次单击“仪表板”、“下载 SSL 证书”。 双击该证书，并按照提示完成安装。 确保在“受信任的根证书颁发机构”证书存储下安装该证书。
2. 在 Visual Studio 解决方案资源管理器中，右键单击“InvoiceProcessingBridge”**项目，然后单击“部署”**。
3. 如图所示提供值，然后单击“部署”。 单击“连接信息”，可从 BizTalk 服务仪表板中获取 BizTalk 服务的 ACS 凭据。
   
   ![][11]  
   
   从输出窗格中，复制在其中部署 EAI 桥的终结点，例如 `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`。 稍后需要此终结点 URL。  

## <a name="step-4-test-the-solution"></a>步骤 4：测试此解决方案
在本主题中，我们了解了如何使用示例中提供的“教程客户端”应用程序测试解决方案。  

1. 在 Visual Studio 中，按 F5 启动“教程客户端”。
2. 执行创建服务总线的步骤时必须已向屏幕中填充了值。 单击“下一步”
3. 在下个窗口中，提供 BizTalk 服务订阅的 ACS 凭据及在其中部署 EAI 和 EDI（接收）桥的终结点。
   
   上一步中复制了 EAI 桥终结点。 对于 EDI 接收桥终结点，在 BizTalk 服务门户中，转到“协议”>“接收设置”>“传输”>“终结点”。
   
   ![][12]  
4. 在下个窗口中，单击“Contoso”下的“发送内部发票”按钮。 在文件对话框中，打开 INHOUSEINVOICE.txt 文件。 检查文件的内容，然后单击“确定”发送该发票。
   
   ![][13]  
5. 几秒钟后，Northwind 接收到该发票。 单击“查看消息”链接，查看 Northwind 接收的发票。 请注意，Northwind 接收的发票采用标准 EDIFACT 架构，而 Contoso 发送的发票采用内部架构。
   
   ![][14]  
6. 选择该发票，然后单击“发送回单”。 请注意，弹出对话框显示接收的发票与发送的回单中交换 ID 相同。 在“发送回单”对话框中单击“确定”。
   
   ![][15]  
7. 几秒钟后，Contoso 成功接收该回单。
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>步骤 5（可选）：分批发送 EDIFACT 发票
BizTalk 服务 EDI 桥还支持分批处理传出消息。 此功能对于希望分批接收（符合特定条件的）消息而非接收单条消息的合作伙伴很有用。

使用批处理时的最重要方面在于该批次的实际释放，也称为释放条件。 释放条件可能基于合作伙伴所希望的接收消息方式。 如果启用了批处理，仅当满足释放条件时，EDI 桥才会向接收合作伙伴发送传出消息。 例如，如果批处理基于消息大小，则仅当批处理了 n 条消息后它才会发送某一批次。 也可以基于时间设置批处理条件，以便在每天固定时间发送一个批次。 此解决方案使用基于消息大小的条件。

1. 在 BizTalk 服务门户中，单击之前创建的协议。 单击“发送设置”>“批处理”>“添加批次”。
2. 对于批次名称，输入“InvoiceBatch”、提供说明，然后单击“下一步”。
3. 指定批次条件，用于定义必须进行批处理的消息。 此解决方案将批处理所有消息。 因此，选择“使用高级定义”选项，并输入“1 = 1”。 这是一个始终为 true 的条件，因此将对所有消息进行批处理。 单击“下一步”。
   
   ![][17]  
4. 指定批次释放条件。 从下拉框中，选择“MessageCountBased”并针对“计数”指定“3”。 这意味着，将向 Northwind 发送包含三条消息的一个批次。 单击“下一步”
   
   ![][18]  
5. 查看摘要，然后单击“保存”。 单击“部署”以重新部署协议。
6. 返回“教程客户端”，单击“发送内部发票”，并按照提示发送发票。 将会发现 Northwind 未收到发票，因为不满足批次大小。 再重复此步骤两次，以确保向 Northwind 发送三条发票消息。 这样便可满足 3 条消息的批次释放条件，现在应可在 Northwind 处看到发票。

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG


