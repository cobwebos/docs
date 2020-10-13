---
title: Azure CDN 规则引擎参考 | Microsoft 文档
description: Azure CDN 规则引擎匹配条件和功能的参考文档。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362574"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Verizon Premium Azure CDN 规则引擎引用

本文列出了 Azure 内容分发网络 (CDN) [规则引擎](cdn-verizon-premium-rules-engine.md)的可用匹配条件和功能的详细说明。

设计规则引擎的目的，是让其充当一个最终授权机构，控制 CDN 处理特定类型请求的方式。

**常见用途**：

- 重写或定义自定义缓存策略。
- 保护或拒绝对敏感内容的请求。
- 将请求重定向。
- 存储自定义日志数据。
## <a name="key-concepts"></a>关键概念
下面将介绍设置规则引擎的关键概念。
### <a name="draft"></a>草稿
策略草稿由一个或多个用于标识请求的规则和将应用于请求的操作集组成。 草稿是一项正在进行的工作，它允许在不影响站点流量的情况下进行频繁的配置更新。 草稿准备好定稿后，应将其转换为只读策略。

### <a name="rule"></a>规则
规则标识一种或多种类型的请求以及将应用于这些请求的操作集。

该环境包括： 

- 定义用于标识请求的逻辑的一组条件表达式。
- 定义用于标识请求的标准的一组匹配条件。
- 定义 CDN 如何处理上述请求的一组功能。
下图中标识了这些元素。

![带标签的屏幕截图显示规则的条件表达式、匹配项和功能。](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>策略
包含一组只读规则的策略提供了以下方法：

- 创建、存储和管理规则的多个变体。
- 回退到以前部署的版本。
- 预先准备特定于事件的规则（例如，由于客户源代码维护而重定向流量的规则。）

> [!NOTE]
> 尽管每个环境只允许单个策略，但可以根据需要部署策略。

### <a name="deploy-request"></a>部署请求
部署请求提供了一个简单而简化的过程，通过该过程，策略可以快速应用于过渡或生产环境。 提供部署请求的历史记录，以便于跟踪应用于这些环境的更改。

> [!NOTE]
> 只有未通过自动验证和错误检测系统的请求才需要手动审查和批准。

### <a name="rule-precedence"></a>规则优先级
策略中包含的规则通常按列出的顺序（即从上到下）进行处理。 如果请求与冲突规则匹配，则将优先处理最后一个要处理的规则。

### <a name="policy-deployment-workflow"></a>策略部署工作流
下面说明了可以将策略应用于生产或过渡环境的工作流。

![策略部署工作流](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|步骤 |说明 |
|---------|---------|
|[创建草稿](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    草稿由一组规则组成，这些规则定义 CDN 应如何处理对内容的请求。     |
|锁定草稿   |     草稿定稿后，应将其锁定并转换为只读策略。    |
|[提交部署请求](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> 部署请求允许将策略应用于测试或生产流量。</br> <br>向暂存或生产环境提交部署请求。</br>     |
|部署请求评审   |    <br>部署请求将进行自动验证和错误检测。</br><br>尽管大部分部署请求都是自动批准的，但对于更复杂的策略，需要手动审查。</br>   |
|策略部署（[过渡](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging)）   |  <br> 在批准部署请求到过渡环境时，策略将应用于过渡环境。 此环境允许针对模拟站点流量测试策略。</br><br>策略已准备好应用于实时站点流量后，就应该提交生产环境的新部署请求。</br>      |
|策略部署（[生产](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti)）   |  向生产环境批准部署请求后，策略将应用于生产环境。 此环境允许策略充当确定 CDN 应如何处理实时流量的最终权限。     |
## <a name="syntax"></a>语法

特殊字符的处理方式取决于匹配条件或功能处理文本值的方式。 匹配条件或功能可能会以下述某种方式解释文本：

- [**文本值**](#literal-values)
- [**通配符值**](#wildcard-values)
- [**正则表达式**](#regular-expressions)

### <a name="literal-values"></a>文本值

可解释为文本值的文本会将所有特殊字符（% 字符除外）视为必须匹配的值的一部分。 换言之，仅当找到精确值（即 `\'*'\`）时才满足设置为 `\'*'\` 的文本匹配条件。

百分比符号用于指示 URL 编码（例如 `%20`）。

### <a name="wildcard-values"></a>通配符值

可以解释为通配符值的文本会为特殊字符赋予其他意义。 下表说明了如何解释以下字符集：

字符 | 说明
----------|------------
\ | 反斜杠用于对此表中指定的任何字符进行转义操作。 必须直接在应该进行转义的特殊字符之前指定一个反斜杠。<br/>例如，以下语法会对星号进行转义：`\*`
% | 百分比符号用于指示 URL 编码（例如 `%20`）。
\* | 星号是通配符，代表一个或多个字符。
Space | 空格字符表示某个匹配条件可以由指定的值或模式满足。
'值' | 一个单引号没有特殊含义， 但是，可以使用一组单引号来指示应将其中的值视为文本值。 可以通过以下方式使用单引号：<br><br/>- 使用单引号时，只要指定的值与比较值的任何部分匹配，即表示满足匹配条件。  例如，`'ma'` 与以下任何字符串都匹配： <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 使用单引号时，可以将特殊字符指定为文本字符。 例如，可以通过将空格字符括在一组单引号中（即 `' '` 或 `'sample value'`）来指定文本空格字符。<br/>- 使用单引号时，可以指定空值。 通过指定一组单引号（即 ''）来指定空值。<br /><br/>**重要提示：**<br/>- 如果指定的值不包含通配符，则会自动将其视为文本值，这意味着不需要指定一组单引号。<br/>- 如果反斜杠不对此表中的其他字符进行转义操作，则会忽略在一组单引号中指定的反斜杠。<br/>- 要将特殊字符指定为文本字符，另一种方式是使用反斜杠（即 `\`）对其进行转义操作。

### <a name="regular-expressions"></a>正则表达式

正则表达式定义要在文本值中对其进行搜索的模式。 正则表达式表示法定义各种符号的具体含义。 下表说明了支持正则表达式的匹配条件和功能如何处理特殊字符。

特殊字符 | 说明
------------------|------------
\ | 反斜杠会对其后的字符进行转义操作，这会导致系统将该字符视为文本值，而不会采用其正则表达式含义。 例如，以下语法会对星号进行转义：`\*`
% | 百分比符号的含义取决于其使用情况。<br/><br/> `%{HTTPVariable}`：此语法标识 HTTP 变量。<br/>`%{HTTPVariable%Pattern}`：此语法使用百分比符号来标识 HTTP 变量并将其用作分隔符。<br />`\%`：对百分比符号进行转义操作以后，即可将其用作文本值，或者用于指示 URL 编码（例如 `\%20`）。
\* | 使用星号时，其前面的字符可以匹配零次或更多次。
Space | 通常将空格字符视为文本字符。
'值' | 可将单引号视为文本字符。 一组单引号没有特殊含义。

匹配支持正则表达式的条件和功能接受由 Perl 兼容的正则表达式 (PCRE) 定义的模式。



## <a name="next-steps"></a>后续步骤

- [规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用规则引擎重写 HTTP 行为](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)
