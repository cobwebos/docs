---
title: "用于 B2B 企业集成的 X12 消息 - Azure 逻辑应用 | Microsoft 文档"
description: "使用 Azure 逻辑应用交换 EDI 格式的 X12 消息以实现 B2B 企业集成"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d30cbc1cfd095ab9e8321a723335ebab593ee5fe
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>使用逻辑应用交换 X12 消息以实现企业集成

在交换 Azure 逻辑应用的 X12 消息之前，必须先创建 X12 协议并将它存储在集成帐户中。 下面是创建 X12 协议的步骤。

> [!NOTE]
> 本页介绍 Azure 逻辑应用的 X12 功能。 有关详细信息，请参阅 [EDIFACT](logic-apps-enterprise-integration-edifact.md)。

## <a name="before-you-start"></a>开始之前

下面是需要准备好的项：

* 已定义的、与你的 Azure 订阅关联的[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)
* 在集成帐户中至少定义了两个[合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，并且在“企业标识”下面配置了这些合作伙伴的 X12 标识符    
* 要上载到[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)的所需[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)

[创建集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)、[添加合作伙伴](logic-apps-enterprise-integration-partners.md)并定义想要使用的[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)之后，可以遵循以下步骤来创建 X12 协议。

## <a name="create-an-x12-agreement"></a>创建 X12 协议

1.    登录 [Azure 门户](http://portal.azure.com "Azure portal")。 在左侧菜单中，选择“更多服务”。 

    > [!TIP]
    > 如果未看到“更多服务”，可能需要先展开菜单。 在折叠的菜单顶部，选择“显示菜单”。

    ![在左侧菜单中，选择“更多服务”](./media/logic-apps-enterprise-integration-x12/account-1.png)

2.    在搜索框中，键入“集成”作为筛选器。 在结果列表中，选择“集成帐户”。  

    ![筛选“集成”，选择“集成帐户”](./media/logic-apps-enterprise-integration-x12/account-2.png)

3. 在打开的“集成帐户”边栏选项卡中，选择要在其中添加协议的集成帐户。
如果未看到任何集成帐户，请[先创建一个集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md "有关集成帐户的详细信息")。

    ![选择要在其中创建协议的集成帐户](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. 选择“概述”，然后选择“协议”磁贴。 如果未添加“协议”磁贴，请先添加该磁贴。 

    ![选择“协议”磁贴](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

5. 在打开的“协议”边栏选项卡中，选择“添加”。

    ![选择“添加”](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)     

6. 在“添加”下面，输入协议的**名称**。 对于协议类型，请选择“X12”。 为协议选择“宿主合作伙伴”，“宿主标识”、“来宾合作伙伴”和“来宾标识”。 有关属性的详细信息，请参阅本步骤中的表格。

    ![提供协议详细信息](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | 属性 | 说明 |
    | --- | --- |
    | Name |协议的名称 |
    | 协议类型 | 应为 X12 |
    | 管理方 |协议需要有管理方和托管方。 宿主合作伙伴代表配置协议的组织。 |
    | 管理方标识 |管理方的标识符 |
    | 托管方 |协议需要有管理方和托管方。 托管方代表与管理方进行交易的组织。 |
    | 托管方标识 |托管方的标识符 |
    | 接收设置 |这些属性适用于协议接收的所有消息。 |
    | 发送设置 |这些属性适用于协议发送的所有消息。 |  

  > [!NOTE]
  > X12 协议的解析取决于是否匹配发送方限定符和标识符，以及合作伙伴和传入消息中定义的接收方限定符和标识符。 如果这些值根据不同的合作伙伴发生更改，请同时更新协议。

## <a name="configure-how-your-agreement-handles-received-messages"></a>配置协议如何处理收到的消息

设置协议属性后，可以配置此协议如何识别和处理从合作伙伴接收的传入消息。

1.    在“添加”下面，选择“接收设置”。
根据要与其交换消息的合作伙伴达成的协议来配置这些属性。 有关属性说明，请参阅本部分中的表格。

    “接收设置”划分为以下部分：“标识符”、“确认”、“架构”、“信封”、“控制编号”、“验证”和“内部设置”。

2. 完成后，请务必选择“确定”保存设置。

协议现已准备就绪，可以处理符合所选设置的传入消息。

### <a name="identifiers"></a>标识符

![设置标识符属性](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| 属性 | 说明 |
| --- | --- |
| ISA1 (授权限定符) |从下拉列表中选择授权限定符值。 |
| ISA2 |可选。 输入授权信息值。 如果为 ISA1 输入的值不是 00，请输入最少一个字母数字字符，最多 10 个字符。 |
| ISA3 (安全限定符) |从下拉列表中选择安全限定符值。 |
| ISA4 |可选。 输入安全信息值。 如果为 ISA3 输入的值不是 00，请输入最少一个字母数字字符，最多 10 个字符。 |

### <a name="acknowledgment"></a>确认

![设置确认属性](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| 属性 | 说明 |
| --- | --- |
| 预期的 TA1 |向交换发送方返回技术确认 |
| 预期的 FA |向交换发送方返回功能确认。 然后基于架构版本选择是需要 997 还是 999 确认 |
| 包括 AK2/IK2 循环 |启用在功能确认中为已接受的事务集生成 AK2 循环 |

### <a name="schemas"></a>架构

为每种事务类型 (ST1) 和发送程序应用程序 (GS2) 选择架构。 接收管道会通过将传入消息中 ST1 和 GS2 的值与此处设置的值进行匹配，并将传入消息的架构与此处设置的架构进行匹配，来分解传入消息。

![选择架构](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| 属性 | 说明 |
| --- | --- |
| 版本 |选择 X12 版本 |
| 事务类型(ST01) |选择事务类型 |
| 发送程序应用程序(GS02) |选择发送程序应用程序 |
| 架构 |选择要使用的架构文件。 架构将添加到集成帐户中。 |

> [!NOTE]
> 配置将上载到[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)的所需[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。

### <a name="envelopes"></a>信封

![在事务集中指定分隔符：选择“标准标识符”或“重复分隔符”](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| 属性 | 说明 |
| --- | --- |
| ISA11 用法 |指定要在事务集中使用的分隔符： <p>选择“标准标识符”可在 EDI 接收管道中使用句点 (.) 十进制表示法，而不是使用传入文档的十进制表示法。 <p>选择“重复分隔符”可为简单数据元素或重复数据结构的重复出现指定分隔符。 例如，脱字符 (^) 通常用作重复分隔符。 对于 HIPAA 架构，只能使用脱字符。 |

### <a name="control-numbers"></a>控制编号

![选择如何处理控制编号重复项](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| 属性 | 说明 |
| --- | --- |
| 不允许交换控制编号重复项 |阻止重复交换。 检查交换控制编号 (ISA13) 是否匹配收到的交换控制编号。 如果检测到匹配项，则接收管道不处理交换。 可以通过为“每 x 天检查重复的 ISA13”提供适当的值，来指定执行检查的间隔天数。 |
| 不允许组合控制编号重复项 |阻止具有重复组控制编号的交换。 |
| 不允许事务集控制编号重复项 |阻止具有重复事务集控制编号的交换。 |

### <a name="validations"></a>验证

![针对收到的消息设置验证属性](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

完成每个验证行后，会自动添加另一行。 如果未指定任何规则，验证将使用“默认”行。

| 属性 | 说明 |
| --- | --- |
| 消息类型 |选择 EDI 消息类型。 |
| EDI 验证 |根据架构的 EDI 属性、长度限制、空数据元素和尾部分隔符的定义，对数据类型执行 EDI 验证。 |
| 扩展验证 |如果数据类型不是 EDI，则验证会基于数据元素要求，以及允许重复、枚举和数据元素长度验证（最小/最大）。 |
| 允许前导零/尾随零 |保留所有前导或尾随零和空格字符。 不要删除这些字符。 |
| 剪裁前导零/尾随零 |删除前导或尾随零和空格字符。 |
| 尾部分隔符策略 |生成尾部分隔符。 <p>选择“不允许”会禁止在接收的交换中包含尾部分隔符。 如果交换包含尾部分隔符，会将它声明为无效。 <p>选择“可选”可接受包含或不包含尾部分隔符的交换。 <p>如果交换必须包含尾部分隔符，请选择“强制”。 |

### <a name="internal-settings"></a>内部设置

![选择内部设置](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| 属性 | 说明 |
| --- | --- |
| 将隐式小数格式“Nn”转换为十进制数值 |将使用格式“Nn”指定的 EDI 数字转换为以十进制数值 |
| 如果允许尾随分隔符，请创建空的 XML 标记 |选中此复选框可使交换发送方包含用于尾随分隔符的空 XML 标记。 |
| 将交换拆分为事务集 - 出错时暂停事务集|通过将相应信封应用于事务集，将交换中的每个事务集分析为单独 XML 文档。 仅暂停未通过验证的事务。 |
| 将交换拆分为事务集 - 出错时暂停交换|通过应用相应信封，将交换中的每个事务集分析为单独 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则暂停整个交换。 | 
| 保留交换 - 出错时暂停事务集 |保留交换不变，为整个批处理交换创建 XML 文档。 仅暂停未能通过验证的事务集，同时继续处理所有其他事务集。 |
| 保留交换 - 出错时暂停交换 |保留交换不变，为整个批处理交换创建 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则暂停整个交换。 |

## <a name="configure-how-your-agreement-sends-messages"></a>配置协议如何发送消息

可以配置此协议如何识别和处理发送给合作伙伴的传出消息。

1.    在“添加”下面，选择“发送设置”。
根据要与其交换消息的合作伙伴达成的协议来配置这些属性。 有关属性说明，请参阅本部分中的表格。

    “发送设置”划分为以下部分：“标识符”、“确认”、“架构”、“字符集和分隔符”、“控制编号”和“验证”。

2. 完成后，请务必选择“确定”保存设置。

协议现已准备就绪，可以处理符合所选设置的传出消息。

### <a name="identifiers"></a>标识符

![设置标识符属性](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| 属性 | 说明 |
| --- | --- |
| 授权限定符(ISA1) |从下拉列表中选择授权限定符值。 |
| ISA2 |输入授权信息值。 如果此值不是 00，请输入最少一个字母数字字符，最多 10 个字符。 |
| 安全限定符(ISA3) |从下拉列表中选择安全限定符值。 |
| ISA4 |输入安全信息值。 如果对于“值(ISA4)”文本框，此值不是 00，请输入最少一个字母数字值，最多 10 个。 |

### <a name="acknowledgment"></a>确认

![设置确认属性](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| 属性 | 说明 |
| --- | --- |
| 预期的 TA1 |向交换发送方返回技术确认 (TA1)。 此设置指定发送消息的管理方从协议中的托管方请求确认。 主机合作伙伴基于协议的接收设置需要这些确认。 |
| 预期的 FA |向交换发送方返回功能确认 (FA)。 基于所使用的架构版本来选择是需要 997 还是 999 确认。 主机合作伙伴基于协议的接收设置需要这些确认。 |
| FA 版本 |选择 FA 版本 |

### <a name="schemas"></a>架构

![选择要使用的架构](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| 属性 | 说明 |
| --- | --- |
| 版本 |选择 X12 版本 |
| 事务类型(ST01) |选择事务类型 |
| 架构 |选择要使用的架构。 架构位于集成帐户中。 如果先选择架构，它会自动配置版本和事务类型  |

> [!NOTE]
> 配置将上载到[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)的所需[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。

### <a name="envelopes"></a>信封

![在事务集中指定分隔符：选择“标准标识符”或“重复分隔符”](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| 属性 | 说明 |
| --- | --- |
| ISA11 用法 |指定要在事务集中使用的分隔符： <p>选择“标准标识符”可在 EDI 接收管道中使用句点 (.) 十进制表示法，而不是使用传入文档的十进制表示法。 <p>选择“重复分隔符”可为简单数据元素或重复数据结构的重复出现指定分隔符。 例如，脱字符 (^) 通常用作重复分隔符。 对于 HIPAA 架构，只能使用脱字符。 |

### <a name="control-numbers"></a>控制编号

![指定控制编号属性](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

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

除了字符集，可以输入每种消息类型的不同分隔符集。 如果没有为给定消息架构指定字符集，则使用默认字符集。

![指定消息类型的分隔符](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| 属性 | 说明 |
| --- | --- |
| 要使用的字符集 |若要验证属性，请选择 X12 字符集。 选项包括“基本”、“扩展”和“UTF8”。 |
| 架构 |从下拉列表中选择架构。 完成每行后，会自动添加新行。 对于所选的架构，请根据以下分隔符说明选择要使用的分隔符集。 |
| 输入类型 |从下拉列表中选择输入类型。 |
| 组件分隔符 |若要分隔复合数据元素，请输入单个字符。 |
| 数据元素分隔符 |若要分隔复合数据元素中的简单数据元素，请输入单个字符。 |
| 替换字符 |输入一个替换字符，用于在生成出站 X12 消息时替换有效负载数据中的所有分隔符。 |
| 段终止符 |若要指示 EDI 段的结尾，请输入单个字符。 |
| 后缀 |选择与段标识符一起使用的字符。 如果指定了后缀，则段终止符数据元素可以为空。 如果段终止符保留为空，则必须指定后缀。 |

### <a name="validation"></a>验证

![针对发送的消息设置验证属性](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

完成每个验证行后，会自动添加另一行。 如果未指定任何规则，验证将使用“默认”行。

| 属性 | 说明 |
| --- | --- |
| 消息类型 |选择 EDI 消息类型。 |
| EDI 验证 |根据架构的 EDI 属性、长度限制、空数据元素和尾部分隔符的定义，对数据类型执行 EDI 验证。 |
| 扩展验证 |如果数据类型不是 EDI，则验证会基于数据元素要求，以及允许重复、枚举和数据元素长度验证（最小/最大）。 |
| 允许前导零/尾随零 |保留所有前导或尾随零和空格字符。 不要删除这些字符。 |
| 剪裁前导零/尾随零 |删除前导或尾随零字符。 |
| 尾部分隔符策略 |生成尾部分隔符。 <p>选择“不允许”会禁止在发送的交换中包含尾部分隔符。 如果交换包含尾部分隔符，会将它声明为无效。 <p>选择“可选”可发送包含或不包含尾部分隔符的交换。 <p>如果发送的交换必须包含尾部分隔符，请选择“强制”。 |

## <a name="find-your-created-agreement"></a>查找创建的协议

1.    设置完所有协议属性后，请在“添加”边栏选项卡中选择“确定”来完成创建协议，并返回到集成帐户边栏选项卡。

    新添加的协议随即会出现在“协议”列表中。

2.    还可以在集成帐户概述中查看协议。 在集成帐户边栏选项卡中选择“概述”，然后选择“协议”磁贴。

    ![选择“协议”磁贴可查看所有协议](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>查看 Swagger
请参阅 [Swagger 详细信息](/connectors/x12/)。 

## <a name="learn-more"></a>了解详细信息
* [了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")  


