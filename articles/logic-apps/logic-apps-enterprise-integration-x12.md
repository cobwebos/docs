---
title: "在 Azure 逻辑应用创建 X12 协议 | Microsoft 文档"
description: "了解如何为 Enterprise Integration Pack 创建 X12 协议 | Azure 逻辑应用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 1a982309fe771c0c244c691ae648bd5f4844a825
ms.openlocfilehash: f7b61b85cadfabde6637e46f0e65108a29901198


---
# <a name="enterprise-integration-with-x12"></a>企业集成与 X12

## <a name="prereqs"></a>先决条件
首先需要创建 X12 协议并将它存储在集成帐户中，然后才能交换 X12 消息。 以下步骤将逐步引导你完成创建 X12 协议的过程。

* 在 Azure 订阅中定义了[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)  
* 在企业标识下至少有两个配置了 EDI X12 限定符的[合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。   
* 要上载到[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)的所需[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)

## <a name="create-an-x12-agreement"></a>创建 X12 协议
登录到 [Azure 门户](http://portal.azure.com "Azure 门户")之后：  

1. 登录 [Azure 门户](http://portal.azure.com "Azure 门户")
2. 选择“更多服务”，然后在筛选器搜索框中输入“集成”。 从结果列表中选择“集成帐户”：    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. 选择要添加证书的集成帐户：    
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)   
4. 选择“协议”磁贴。 如果看不到“协议”磁贴，请添加它：     
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. 在打开的“协议”边栏选项卡中，选择“添加”按钮。   
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)     
6. 在打开的“协议”边栏选项卡，为协议输入“名称”，然后选择“协议类型”、“主机合作伙伴”、“主机标识”、“来宾合作伙伴”、“来宾标识”。    
![](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

| 属性 | 说明 |
| --- | --- |
| Name |协议的名称 |
| 协议类型 | 应为 X12 |
| 管理方 |协议需要有管理方和托管方。 管理方代表配置协议的组织 |
| 管理方标识 |管理方的标识符 |
| 托管方 |协议需要有管理方和托管方。 托管方代表与管理方进行交易的组织 |
| 托管方标识 |托管方的标识符 |
| 接收设置 |这些属性适用于协议接收到的所有消息 |
| 发送设置 |这些属性适用于协议发送的所有消息 |  

> [!NOTE]
> X12 协议的解析取决于是否匹配发送方限定符和标识符，以及合作伙伴和传入消息中定义的接收方限定符和标识符。  如果合作伙伴对这些值有更改，也会编辑该协议。
> 
> 

## <a name="receive-settings"></a>接收设置

1. 选择“接收设置”以配置通过此协议收到的消息的处理方式。  
2. “接收设置”控件划分为以下部分，包括“标识符”、“确认”、“架构”、“信封”、“控制编号”、“验证”和“内部设置”。 基于你要与之交换消息的合作伙伴与你之间的协议来配置这些属性。 下面是这些控件的视图，请基于你希望此协议如何处理标识和处理传入消息来配置它们  

### <a name="identifiers"></a>标识符

![](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| 属性 | 说明 |
| --- | --- |
| ISA1 (授权限定符) |从下拉列表中选择授权限定符值。 |
| ISA2 |可选。 输入授权信息值。 如果为 ISA1 输入的值不是 00，请输入最少一个字母数字字符，最多 10 个字符。 |
| ISA3 (安全限定符) |从下拉列表中选择安全限定符值。 |
| ISA4 |可选。 输入安全信息值。 如果为 ISA3 输入的值不是 00，请输入最少一个字母数字字符，最多 10 个字符。 |

### <a name="acknowledgments"></a>致谢

![](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| 属性 | 说明 |
| --- | --- |
| 预期的 TA1 |向交换发送方返回技术确认 |
| 预期的 FA |向交换发送方返回功能确认。 然后基于架构版本选择是需要 997 还是 999 确认 |
| 包括 AK2/IK2 循环 |启用在功能确认中为已接受的事务集生成 AK2 循环 |


### <a name="schemas"></a>架构
为每种事务类型 (ST1) 和发送程序应用程序 (GS2) 选择架构。 接收管道会通过将传入消息中 ST1 和 GS2 的值与此处设置的值进行匹配，并将传入消息的架构与此处设置的架构进行匹配，来分解传入消息。

![](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| 属性 | 说明 |
| --- | --- |
| 版本 |选择 X12 版本 |
| 事务类型(ST01) |选择事务类型 |
| 发送程序应用程序(GS02) |选择发送程序应用程序 |
| 架构 |选择要使用的架构文件。 架构将添加到集成帐户中 |

> [!NOTE]
> 在[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)中配置上载的所需[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)
> 
> 

### <a name="envelopes"></a>信封

![](./media/logic-apps-enterprise-integration-x12/x12-34.png) 

| 属性 | 说明 |
| --- | --- |
| ISA11 用法 |使用此字段可在事务集中指定分隔符：</br></br>选择标准标识符可在 EDI 接收管道中使用“.”的十进制符号， 而不是传入文档的十进制符号。</br></br>选择重复分隔符可为简单数据元素或重复数据结构的重复出现指定分隔符。 例如，(^) 通常用作重复分隔符。 对于 HIPAA 架构，只能使用 (^)。 |

### <a name="control-numbers"></a>控制编号

![](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| 属性 | 说明 |
| --- | --- |
| 不允许交换控制编号重复项 |阻止重复交换。 检查交换控制编号 (ISA13) 是否匹配收到的交换控制编号。 如果检测到匹配项，则接收管道不处理交换。 可以通过为“每 x 天检查重复的 ISA13”提供适当的值，来指定执行检查的间隔天数 |
| 不允许组合控制编号重复项 |阻止具有重复组控制编号的交换 |
| 不允许事务集控制编号重复项 |阻止具有重复事务集控制编号的交换 |

### <a name="validations"></a>验证

![](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

| 属性 | 说明 |
| --- | --- |
| 消息类型 |EDI 消息类型，如“850-采购订单”或“999-实现确认”。 |
| EDI 验证 |按照架构的 EDI 属性、长度限制、空数据元素和尾部分隔符的定义，对数据类型执行 EDI 验证。 |
| 扩展验证 |如果数据类型不是 EDI，则验证会基于数据元素要求，以及允许重复、枚举和数据元素长度验证（最小/最大）。 |
| 允许前导零/尾随零 |保留前导或尾随的任何额外空格和零字符。 不删除它们。 |
| 尾部分隔符策略 |在收到的交换中生成尾部分隔符。 选项包括“禁止”、“可选”和“强制”。 |

### <a name="internal-settings"></a>内部设置

![](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| 属性 | 说明 |
| --- | --- |
| 将隐式小数格式 Nn 转换为十进制数值 |将使用格式 Nn 指定的 EDI 数字转换为以十进制数值 |
| 如果允许尾随分隔符，请创建空的 XML 标记 |选中此复选框可使交换发送方包含用于尾随分隔符的空 XML 标记。 |
| 将交换拆分为事务集 - 出错时暂停事务集|通过将相应信封应用于事务集，将交换中的每个事务集分析为单独 XML 文档。 仅暂停验证失败的这些事务 |
| 将交换拆分为事务集 - 出错时暂停交换|通过应用相应信封，将交换中的每个事务集分析为单独 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则暂停整个交换 | 
| 保留交换 - 出错时暂停事务集 |保留交换不变，为整个批处理交换创建 XML 文档。 仅暂停未能通过验证的事务集，同时继续处理所有其他事务集 |
| 保留交换 - 出错时暂停交换 |保留交换不变，为整个批处理交换创建 XML 文档。  如果交换中的一个或多个事务集未能通过验证，则暂停整个交换 |

设置接收设置属性之后，选择“确定”按钮  
   
协议已准备好处理符合所选架构的传入消息。

## <a name="send-settings"></a>发送设置

1. 选择“发送设置”以配置通过此协议发送的消息的处理方式。  

2. “发送设置”控件划分为以下部分，包括“标识符”、“确认”、“架构”、“信封”、“控制编号”、“字符集和分隔符”和“验证”。  基于你要与之交换消息的合作伙伴与你之间的协议来配置这些属性。 下面是这些控件的视图，请基于你希望此协议如何处理标识和处理传出消息来配置它。


### <a name="identifiers"></a>标识符

![](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| 属性 | 说明 |
| --- | --- |
| 授权限定符(ISA1) |从下拉列表中选择授权限定符值。 |
| ISA2 |输入授权信息值。 如果此值不是 00，请输入最少一个字母数字字符，最多 10 个字符。 |
| 安全限定符(ISA3) |从下拉列表中选择安全限定符值。 |
| ISA4 |输入安全信息值。 如果对于“值(ISA4)”文本框，此值不是 00，请输入最少一个字母数字值，最多 10 个。 |

### <a name="acknowledgment"></a>确认

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| 属性 | 说明 |
| --- | --- |
| 预期的 TA1 |选中此复选框可向交换发送方返回技术 (TA1) 确认。 此设置指定发送消息的管理方从协议中的托管方请求确认。 主机合作伙伴基于协议的接收设置需要这些确认。 |
| 预期的 FA |选中此复选框可向交换发送方返回功能 (FA) 确认，然后基于所使用的架构版本来选择是需要 997 还是 999 确认。 主机合作伙伴基于协议的接收设置需要这些确认。 |
| FA 版本 |选择 FA 版本 |

### <a name="schemas"></a>架构

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| 属性 | 说明 |
| --- | --- |
| 版本 |选择 X12 版本 |
| 事务类型(ST01) |选择事务类型 |
| 架构 |选择要使用的架构。 架构位于集成帐户中。 如果先选择架构，它会自动配置版本和事务类型  |

> [!NOTE]
> 在[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)中配置上载的所需[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)
> 
> 

### <a name="envelopes"></a>信封

![](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| 属性 | 说明 |
| --- | --- |
| ISA11 用法 |使用此字段指定事务中的分隔符 |
| 标准标识符 |选择标准标识符可在 EDI 接收管道中使用“.”的十进制符号， |
| 重复分隔符 |选择重复分隔符可为简单数据元素或重复数据结构的重复出现指定分隔符。 例如，(^) 通常用作重复分隔符。 对于 HIPAA 架构，只能使用 (^) |

### <a name="control-numbers"></a>控制编号

![](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| 属性 | 说明 |
| --- | --- |
| 控制版本号(ISA12) |选择 X12 标准的版本 |
| 用法指示符(ISA15) |选择交换的上下文。  值是信息、生产数据或测试数据 |
| 架构 |为发送到发送管道的 X12 编码交换生成 GS 和 ST 段 |
| GS1 |可选，从下拉列表中为功能代码选择值 |
| GS2 |可选，应用程序发送方 |
| GS3 |可选，应用程序接收方 |
| GS4 |可选，选择 CCYYMMDD 或 YYMMDD |
| GS5 |可选，选择 HHMM、HHMMSS 或 HHMMSSdd |
| GS7 |可选，从下拉列表中为负责代理选择值 |
| GS8 |可选，文档的版本 |
| 交换控制编号(ISA13) |必需，输入交换控制编号的值范围。 输入最小值为 1 且最大值为 999999999 的数值 |
| 组控制编号(GS06) |必需，输入组控制编号的数值范围。 输入最小值为 1 且最大值为 999999999 的数值 |
| 事务集控制编号(ST02) |必需，输入事务集控制编号的数值范围。 输入一组最小值为 1 且最大值为 999999999 的数值 |
| 前缀 |可选，为确认中使用的事务集控制编号范围指定。 为中间两个字段输入数值，并为前缀和后缀字段输入字母数字值（如果需要）。 中间字段是必需的，包含控制编号的最小和最大值 |
| 后缀 |可选，为确认中使用的事务集控制编号范围指定。 为中间两个字段输入数值，并为前缀和后缀字段输入字母数字值（如果需要）。 中间字段是必需的，包含控制编号的最小和最大值 |

### <a name="character-sets-and-separators"></a>字符集和分隔符

除了字符集，可以输入要用于每种消息类型的不同分隔符集。 如果没有为给定消息架构指定字符集，则使用默认字符集。

![](./media/logic-apps-enterprise-integration-x12/x12-9.png) 


| 属性 | 说明 |
| --- | --- |
| 要使用的字符集 |选择 X12 字符集以验证属性。  选项包括“基本”、“扩展”和“UTF8” |
| 架构 |从下拉列表中选择架构。 对于所选架构，选择要使用的分隔符集 |
| 输入类型 |从下拉列表中选择输入类型 |
| 组件元素分隔符 |输入单个字符以分隔复合数据元素 |
| 数据元素分隔符 |输入单个字符以分隔复合数据元素中的简单数据元素 |
| 替换字符 |输入替换字符。 生成出站 X12 消息时，有效负载数据中分隔符的所有实例都会替换为指定字符 |
| 段终止符 |输入单个字符以指示 EDI 段的结尾 |
| 后缀 |选择与段标识符一起使用的字符。 如果指定了后缀，则段终止符数据元素可以为空。 如果段终止符保留为空，则必须指定后缀。 |

### <a name="validation"></a>验证

![](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

| 属性 | 说明 |
| --- | --- |
| 消息类型 |从列表中选择消息类型 |
| EDI 验证 |选择此选项可对交换接收方启用验证。 此验证对事务集数据元素执行 EDI 验证，并验证数据类型、长度限制以及空数据元素和尾部分隔符 |
| 扩展验证 |选择此选项可启用从交换发送方接收的交换的扩展验证。 这包括验证字段长度、可选性和重复次数的验证以及 XSD 数据类型验证。 可以在未启用 EDI 验证的情况下启用扩展验证，反之亦然。 |
| 允许前导零/尾随零 |选择此选项可指定，如果 EDI 交换中的数据元素由于尾随空格而不符合其长度要求，但是在删除它们之后会符合其长度要求，则从某方收到的 EDI 交换会通过验证。 |
| 尾部分隔符 |选择此选项可指定，如果 EDI 交换中的数据元素由于前导（或尾随）零或尾随空格而不符合其长度要求，但是在删除它们之后会符合其长度要求，则从某方收到的 EDI 交换会通过验证。</br></br>如果不希望在从交换发送方接收的交换中允许尾部分隔符，则选择“不允许”。 如果交换包含尾部分隔符，则会将它声明为无效。</br></br>选择“可选”可接受包含或不包含尾部分隔符的交换。</br></br>如果收到的交换必须包含尾部分隔符，则选择“强制”。 |

设置发送设置属性之后，选择“确定”按钮。  协议已准备好处理符合所选架构的传出消息。

选择“确定”以创建协议。

在“集成帐户”边栏选项卡上选择“协议”磁贴，会看到列出新添加的协议。  
![](./media/logic-apps-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>了解详细信息
* [了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")  




<!--HONumber=Feb17_HO1-->


