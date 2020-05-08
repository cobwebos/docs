---
title: 为 B2B 发送和接收 X12 消息
description: 使用 Azure 逻辑应用和 Enterprise Integration Pack 来交换 B2B 企业集成方案的 X12 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 8ec20e03544ba54b83130ae41244dcdb186252d0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612990"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中交换 X12 消息以实现 B2B 企业集成

若要在 Azure 逻辑应用中使用 X12 消息，可以使用 X12 连接器，它提供用于管理 X12 通信的触发器和操作。 有关 EDIFACT 消息的详细信息，请参阅[EXCHANGE EDIFACT 消息](logic-apps-enterprise-integration-edifact.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从中使用 X12 连接器的逻辑应用，以及用于启动逻辑应用工作流的触发器。 X12 连接器仅提供操作，而不提供触发器。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 与 Azure 订阅关联并链接到计划使用 X12 连接器的逻辑应用的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 逻辑应用和集成帐户必须存在于同一位置或 Azure 区域中。

* 已使用 X12 标识限定符在集成帐户中至少定义了两个[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 已添加到集成帐户的 XML 验证所使用的[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 如果正在使用健康保险便携性和责任法案（HIPAA）架构，请参阅[HIPAA 架构](#hipaa-schemas)。

* 在您可以使用 X12 连接器之前，必须在您的贸易合作伙伴之间创建 X12[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)，并将该协议存储在集成帐户中。 如果使用健康保险便携性和责任法案（HIPAA）架构，则需要向协议中添加`schemaReferences`部分。 有关详细信息，请参阅[HIPAA 架构](#hipaa-schemas)。

<a name="receive-settings"></a>

## <a name="receive-settings"></a>接收设置

设置协议属性后，可以配置此协议如何识别和处理您通过本协议从合作伙伴接收的入站消息。

1. 在“添加”下面，选择“接收设置”。********

1. 根据要与其交换消息的合作伙伴达成的协议来配置这些属性。 **接收设置**分为以下几个部分：

   * [标识符](#inbound-identifiers)
   * [Acknowledgement](#inbound-acknowledgement)
   * [架构](#inbound-schemas)
   * [信封](#inbound-envelopes)
   * [控制编号](#inbound-control-numbers)
   * [验证](#inbound-validations)
   * [内部设置](#inbound-internal-settings)

   有关属性说明，请参阅本部分中的表格。

1. 完成后，请选择 **"确定"** 以保存设置。

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>接收设置-标识符

![入站消息的标识符属性](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| 属性 | 说明 |
|----------|-------------|
| **ISA1 (授权限定符)** | 要使用的授权限定符值。 默认值为**00-不存在任何授权信息**。 <p>**注意**：如果选择其他值，请为**ISA2**属性指定一个值。 |
| **ISA2** | **ISA1**属性不是00时要使用的授权信息值 **-不存在任何授权信息**。 此属性值必须至少包含一个字母数字字符，最多包含10个字符。 |
| **ISA3 (安全限定符)** | 要使用的安全限定符值。 默认值为**00-不存在任何安全信息**。 <p>**注意**：如果选择其他值，请为**ISA4**属性指定一个值。 |
| **ISA4** | 当**ISA3**属性不是00时使用的安全信息值 **-不存在任何安全信息**。 此属性值必须至少包含一个字母数字字符，最多包含10个字符。 |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>接收设置-确认

![对入站消息的确认](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| 属性 | 说明 |
|----------|-------------|
| **预期的 TA1** | 向交换发送方返回技术确认 (TA1)。 |
| **预期的 FA** | 向交换发送方返回功能确认 (FA)。 <p>对于 " **FA 版本**" 属性，根据架构版本，选择 "997" 或 "999" 确认。 <p>若要在功能确认中为接受的事务集启用 AK2 循环生成，请选择 "**包括 AK2/IK2 循环**"。 |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>接收设置-架构

![入站消息的架构](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

对于此部分，请从[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)中为每个事务类型（ST01）和发送程序应用程序（GS02）选择[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 EDI 接收管道通过将此部分中设置的值和架构与传入消息中的 ST01 和 GS02 的值和传入消息的架构进行匹配，来反汇编传入消息。 完成每行后，会自动显示一个新的空行。

| 属性 | 说明 |
|----------|-------------|
| **版本** | 架构的 X12 版本 |
| **事务类型(ST01)** | 事务类型 |
| **发送程序应用程序(GS02)** | 发件人应用程序 |
| **模式** | 要使用的架构文件 |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>接收设置-信封

![用于入站消息的事务集中的分隔符](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| 属性 | 说明 |
|----------|-------------|
| **ISA11 用法** | 要在事务集中使用的分隔符： <p>- **标准标识符**：对十进制表示法使用句点（.），而不是 EDI 接收管道中传入文档的十进制符号。 <p>- **重复分隔符**：为简单数据元素或重复数据结构的重复出现指定分隔符。 例如，脱字符 (^) 通常用作重复分隔符。 对于 HIPAA 架构，只能使用脱字符。 |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>接收设置-控制编号

![处理入站消息的控制编号重复项](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| 属性 | 说明 |
|----------|-------------|
| **禁止交换控制编号重复项** | 阻止重复交换。 检查所接收的交换控制编号的交换控制编号（ISA13）。 如果检测到匹配项，则 EDI 接收管道不处理交换。 <p><p>若要指定执行检查的天数，请为 "**检查重复的 ISA13 的时间间隔（天）** " 属性输入一个值。 |
| **不允许组合控制编号重复项** | 阻止具有重复组控制编号的交换。 |
| **不允许事务集控制编号重复项** | 阻止具有重复事务集控制编号的交换。 |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>接收设置-验证

![入站消息验证](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

**默认**行显示用于 EDI 消息类型的验证规则。 如果要定义不同的规则，请选择要将规则设置为 " **true**" 的每个框。 完成每行后，会自动显示一个新的空行。

| 属性 | 说明 |
|----------|-------------|
| **消息类型** | EDI 消息类型 |
| **EDI 验证** | 根据架构的 EDI 属性、长度限制、空数据元素和尾部分隔符的定义，对数据类型执行 EDI 验证。 |
| **扩展验证** | 如果数据类型不是 EDI，则验证将基于数据元素要求，并允许重复、枚举和数据元素长度验证（最小值或最大值）。 |
| **允许前导零/尾随零** | 保留任何其他前导或尾随零和空格字符。 不要删除这些字符。 |
| **剪裁前导零/尾随零** | 删除任何前导或尾随零和空格字符。 |
| **尾部分隔符策略** | 生成尾部分隔符。 <p>- **不允许**：在入站交换中禁止尾随分隔符和分隔符。 如果交换包含尾部分隔符，会将它声明为无效。 <p>- **可选**：接受带有或不带尾部分隔符的交换。 <p>- **必需**：入站交换必须包含尾随分隔符和分隔符。 |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>接收设置-内部设置

![入站消息的内部设置](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| 属性 | 说明 |
|----------|-------------|
| **将隐式小数格式 Nn 转换为十进制数值** | 将使用格式 "Nn" 指定的 EDI 数字转换为十进制数值。 |
| **如果允许尾随分隔符，请创建空的 XML 标记** | 让交换发送方包含空的 XML 标记用于尾部分隔符。 |
| **将交换拆分为事务集 - 出错时暂停事务集** | 通过向事务集应用相应的信封，将交换中的每个事务集解析为单独的 XML 文档。 仅挂起验证失败的事务。 |
| **将交换拆分为事务集 - 出错时暂停交换** | 通过应用相应的信封，将交换中的每个事务集解析为单独的 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则暂停整个交换。 |
| **保留交换 - 出错时暂停事务集** | 保持交换不变，并为整个批处理交换创建 XML 文档。 仅暂停未通过验证的事务集，但会继续处理所有其他事务集。 |
| **保留交换 - 出错时暂停交换** |保留交换不变，为整个批处理交换创建 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则暂停整个交换。 |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>发送设置

设置协议属性后，可以配置此协议如何识别和处理发送给合作伙伴的出站消息。

1. 在“添加”下面，选择“发送设置”。********

1. 根据要与其交换消息的合作伙伴达成的协议来配置这些属性。 有关属性说明，请参阅本部分中的表格。

   **发送设置**分为以下几个部分：

   * [标识符](#outbound-identifiers)
   * [Acknowledgement](#outbound-acknowledgement)
   * [架构](#outbound-schemas)
   * [信封](#outbound-envelopes)
   * [控制版本号](#outbound-control-version-number)
   * [控制编号](#outbound-control-numbers)
   * [字符集和分隔符](#outbound-character-sets-separators)
   * [验证](#outbound-validation)

1. 完成后，请选择 **"确定"** 以保存设置。

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>发送设置-标识符

![出站消息的标识符属性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| 属性 | 说明 |
|----------|-------------|
| **ISA1 (授权限定符)** | 要使用的授权限定符值。 默认值为**00-不存在任何授权信息**。 <p>**注意**：如果选择其他值，请为**ISA2**属性指定一个值。 |
| **ISA2** | **ISA1**属性不是00时要使用的授权信息值 **-不存在任何授权信息**。 此属性值必须至少包含一个字母数字字符，最多包含10个字符。 |
| **ISA3 (安全限定符)** | 要使用的安全限定符值。 默认值为**00-不存在任何安全信息**。 <p>**注意**：如果选择其他值，请为**ISA4**属性指定一个值。 |
| **ISA4** | 当**ISA3**属性不是00时使用的安全信息值 **-不存在任何安全信息**。 此属性值必须至少包含一个字母数字字符，最多包含10个字符。 |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>发送设置-确认

![出站消息的确认属性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| 属性 | 说明 |
|----------|-------------|
| **预期的 TA1** | 向交换发送方返回技术确认 (TA1)。 <p>此设置指定发送消息的主机伙伴向协议中的来宾合作伙伴请求确认。 主机伙伴应基于协议的接收设置进行这些确认。 |
| **预期的 FA** | 向交换发送方返回功能确认 (FA)。 对于 " **FA 版本**" 属性，根据架构版本，选择997或999确认。 <p>此设置指定发送消息的主机伙伴从协议中的来宾合作伙伴请求确认。 主机伙伴应基于协议的接收设置进行这些确认。 |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>发送设置-架构

![出站消息的架构](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

对于此部分，请从[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)中为每个事务类型（ST01）选择一个[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 完成每行后，会自动显示一个新的空行。

| 属性 | 说明 |
|----------|-------------|
| **版本** | 架构的 X12 版本 |
| **事务类型(ST01)** | 架构的事务类型 |
| **模式** | 要使用的架构文件。 如果首先选择架构，则将自动设置版本和事务类型。 |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>发送设置-信封

![要用于出站消息的事务集中的分隔符](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| 属性 | 说明 |
|----------|-------------|
| **ISA11 用法** | 要在事务集中使用的分隔符： <p>- **标准标识符**：对十进制表示法使用句点（.），而不是 EDI 发送管道中的出站文档的十进制符号。 <p>- **重复分隔符**：为简单数据元素或重复数据结构的重复出现指定分隔符。 例如，脱字符 (^) 通常用作重复分隔符。 对于 HIPAA 架构，只能使用脱字符。 |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>发送设置-控制版本号

![控制出站消息的版本号](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

对于此部分，请从[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)中为每个交换选择一个[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 完成每行后，会自动显示一个新的空行。

| 属性 | 说明 |
|----------|-------------|
| **控制版本号(ISA12)** | X12 标准的版本 |
| **用法指示符(ISA15)** | 交换的上下文，可以是**测试**数据、**信息**数据或**生产**数据 |
| **模式** | 用于为发送到 EDI 发送管道的 X12 编码交换生成 GS 和 ST 段的架构。 |
| **GS1** | 可选，选择功能代码。 |
| **GS2** | 可选，指定应用程序发送方。 |
| **GS3** | 可选，指定应用程序接收器。 |
| **GS4** | 可选，选择**CCYYMMDD**或**YYMMDD**。 |
| **GS5** | 可选，选择**HHMM**、 **HHMMSS**或**HHMMSSdd**。 |
| **GS7** | 可选，为负责代理选择值。 |
| **GS8** | 可选，指定架构文档版本。 |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>发送设置-控制编号

![出站消息控制编号](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| 属性 | 说明 |
|----------|-------------|
| **交换控制编号(ISA13)** | 交换控制编号的值范围，其最小值为1，最大值为999999999 |
| **组控制编号(GS06)** | 组控制编号的值范围，最小值为1，最大值为999999999 |
| **事务集控制编号(ST02)** | 事务集控制编号的值范围，最小值为1，最大值为999999999 <p>- **Prefix**：可选，字母数字值 <br>- **后缀**：可选，字母数字值 |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>发送设置-字符集和分隔符

![出站消息中的消息类型分隔符](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

**默认**行显示用作消息架构分隔符的字符集。 如果不想使用**默认**字符集，则可以为每种消息类型输入一组不同的分隔符。 完成每行后，会自动显示一个新的空行。

> [!TIP]
> 若要提供特殊字符值，请编辑作为 JSON 的协议并提供特殊字符的 ASCII 值。

| 属性 | 说明 |
|----------|-------------|
| **要使用的字符集** | X12 字符集，它是**基本**、**扩展**或**UTF8**。 |
| **模式** | 要使用的架构。 选择架构后，根据下面的分隔符说明选择要使用的字符集。 |
| **输入类型** | 字符集的输入类型 |
| **组件分隔符** | 分隔复合数据元素的单个字符 |
| **数据元素分隔符** | 分隔复合数据内的简单数据元素的单个字符 |
| **替换字符分隔符** | 在生成出站 X12 消息时替换有效负载数据中的所有分隔符的替换字符 |
| **段终止符** | 指示 EDI 段的末尾的单个字符 |
| **后缀** | 要与段标识符一起使用的字符。 如果指定一个后缀，则段终止符数据元素可以为空。 如果段终止符保留为空，你必须指定一个后缀。 |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>发送设置-验证

![出站消息的验证属性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

**默认**行显示用于 EDI 消息类型的验证规则。 如果要定义不同的规则，请选择要将规则设置为 " **true**" 的每个框。 完成每行后，会自动显示一个新的空行。

| 属性 | 说明 |
|----------|-------------|
| **消息类型** | EDI 消息类型 |
| **EDI 验证** | 根据架构的 EDI 属性、长度限制、空数据元素和尾部分隔符的定义，对数据类型执行 EDI 验证。 |
| **扩展验证** | 如果数据类型不是 EDI，则验证将基于数据元素要求，并允许重复、枚举和数据元素长度验证（最小值或最大值）。 |
| **允许前导零/尾随零** | 保留任何其他前导或尾随零和空格字符。 不要删除这些字符。 |
| **剪裁前导零/尾随零** | 删除任何前导或尾随零和空格字符。 |
| **尾部分隔符策略** | 生成尾部分隔符。 <p>- **不允许**：在出站交换中禁止尾随分隔符和分隔符。 如果交换包含尾部分隔符，会将它声明为无效。 <p>- **可选**：发送带或不带尾部分隔符的交换。 <p>- **必需**：出站交换必须包含尾随分隔符和分隔符。 |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA 架构和消息类型

使用 HIPAA 架构和277或837消息类型时，需要执行一些额外的步骤。 这些消息类型的[文档版本号（GS8）](#outbound-control-version-number)包含9个以上的字符，例如 "005010X222A1"。 此外，某些文档版本号映射为变体消息类型。 如果在架构和协议中未引用正确的消息类型，则会收到以下错误消息：

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

此表列出了受影响的消息类型、所有变体以及映射到这些消息类型的文档版本号：

| 消息类型或变体 |  说明 | 文档版本号（GS8） |
|-------------------------|--------------|-------------------------------|
| 277 | 卫生保健信息状态通知 | 005010X212 |
| 837_I | 卫生保健索赔牙科 | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | 医疗保健索赔机构 | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | 医疗保健索赔专员 | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

使用这些文档版本号时，还需要禁用 EDI 验证，因为它们会导致字符长度无效的错误。

若要指定这些文档的版本号和消息类型，请执行以下步骤：

1. 在 HIPAA 架构中，将当前消息类型替换为要使用的文档版本号的变量消息类型。

   例如，假设您要使用`005010X222A1` `837`消息类型的文档版本号。 在架构中，将`"X12_00501_837"` `"X12_00501_837_P"`每个值替换为值。

   若要更新架构，请执行以下步骤：

   1. 在 Azure 门户中，请前往你的集成帐户。 查找并下载你的架构。 替换消息类型并重命名架构文件，并将修改后的架构上传到集成帐户。 有关详细信息，请参阅[编辑架构](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)。

   1. 在 "你是协议的消息设置" 中，选择修改后的架构。

1. 在协议的`schemaReferences`对象中，添加另一个条目来指定与你的文档版本号匹配的变体消息类型。

   例如，假设您要使用 "文档版本号" `005010X222A1`作为 "消息`837`类型"。 你的协议中`schemaReferences`有一个具有以下属性和值的部分：

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   在本`schemaReferences`部分中，添加具有以下值的另一个条目：

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   完成后， `schemaReferences`部分如下所示：

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. 在协议的消息设置中，如果使用的是**默认**值，请清除每个消息类型的**edi 验证**复选框或为所有消息类型禁用 edi 验证。

   ![为所有消息类型或每种消息类型禁用验证](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>连接器参考

有关此连接器的其他技术详细信息（如连接器的 Swagger 文件所述的操作和限制），请参阅[连接器的参考页](https://docs.microsoft.com/connectors/x12/)。

> [!NOTE]
> 对于[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的 ise 标记版本使用 Ise 的[B2B 消息限制](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)。

## <a name="next-steps"></a>后续步骤

* 了解[逻辑应用的其他连接器](../connectors/apis-list.md)