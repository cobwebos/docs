---
title: 协作转换框架（CTF）报告-翻译人员
titleSuffix: Azure Cognitive Services
description: 如何使用协作性翻译框架 (CTF) 报告。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 1bf6fefbe7d2ea3fccc393f4445fceec44ed4117
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584665"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>如何使用协作性翻译框架 (CTF) 报告

> [!NOTE]
> 不推荐使用此方法。 它在转换器的3.0 版中不可用。
> 
> 从2018年2月1日起，已弃用了以前可用于版本转换器的协作转换框架（CTF）。 AddTranslation 和 AddTranslationArray 函数允许用户通过协作性翻译框架启用更正。 在 2018 年 1 月 31 日之后，这两个函数不接受新的句子提交，并且用户会收到错误消息。 这些函数已停用，将不会被替换。

协作性翻译框架 (CTF) 报告 API 返回 CTF 存储中的统计信息和实际内容。 此 API 与 GetTranslations() 方法不同，因为它：
* 仅从你的帐户（appId 或 Azure 市场帐户）返回已翻译的内容及其总计数。
* 无需匹配源句即可返回已翻译的内容及其总计数。
* 不返回自动翻译（机器翻译）。

## <a name="endpoint"></a>终结点
CTF 报告 API 的终结点是 https://api.microsofttranslator.com/v2/beta/ctfreporting.svc 。

## <a name="methods"></a>方法
| 名称 | 描述|
|:---|:---|
| GetUserTranslationCounts 方法 | 获取用户创建的翻译计数。 |
| GetUserTranslations 方法 | 检索用户创建的翻译。 |

这些方法可用于：
* 检索帐户 ID 下的完整用户翻译和更正集以供下载。
* 获取经常参与翻译的人员的列表。 确保在 AddTranslation() 中提供正确的用户名。
* 生成一个用户界面 (UI)，允许你的可信用户根据 URI 前缀查看所有可用候选项，或在必要时限制为仅查看你的站点的部分内容。

> [!NOTE]
> 这两种方法都相对较慢且成本高昂。 建议尽量少用。

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts 方法

此方法可获取用户创建的翻译计数。 它提供按 uriPrefix、from、to、user、minRating 和 maxRating 请求参数分组的翻译计数列表。

**语法**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parameters**

| 参数 | 说明 |
|:---|:---|
| appId | **必需**：如果使用授权标头，请将 appid 字段留空，否则请指定包含 "Bearer" + " " + access token 的字符串。|
| uriPrefix | **可选**：一个包含翻译 URI 前缀的字符串。|
| from | **可选**：一个表示翻译文本语言代码的字符串。 |
| 更改为 | **可选**：一个字符串，表示要将文本翻译成的语言代码。|
| minRating| **可选**：一个整数值，表示已翻译文本的最低质量等级。 有效值介于 -10 和 10 之间。 默认值为 1。|
| maxRating| **可选**：一个整数值，表示已翻译文本的最高质量等级。 有效值介于 -10 和 10 之间。 默认值为 1。|
| user | **可选**：一个字符串，用于根据提交发起方筛选结果。 |
| category| **可选**：一个包含翻译类别或领域的字符串。 此参数仅支持默认选项 general。|
| minDateUtc| **可选**：要检索翻译的起始日期。 日期必须采用 UTC 格式。 |
| maxDateUtc| **可选**：要检索翻译的结束日期。 日期必须采用 UTC 格式。 |
| skip| **可选**：要在页面上跳过的结果数。 例如，如果要跳过前 20 行结果，从第 21 个结果记录开始查看，则为此参数指定 20。 此参数的默认值为 0。|
| take | **可选**：要检索的结果数。 每个请求的最大数目为 100 个。 默认值为 100。|

> [!NOTE]
> skip 和 take 请求参数可为大量结果记录启用分页。

**返回值**

结果集包含 **UserTranslationCount** 的数组。 每个 UserTranslationCount 都具有以下元素：

| 字段 | 说明 |
|:---|:---|
| 计数| 检索的结果数|
| From | 源语言|
| Rating| 提交者在 AddTranslation() 方法调用中应用的等级|
| 功能| 目标语言|
| URI| AddTranslation() 方法调用中应用的 URI|
| 用户| 用户名|

**异常**

| 异常 | 消息 | Conditions |
|:---|:---|:---|
| ArgumentOutOfRangeException | 参数“**maxDateUtc**”必须大于或等于“**minDateUtc**”。| 参数 **maxDateUtc** 的值小于参数 **minDateUtc** 的值。|
| TranslateApiException | IP 超过配额。| <ul><li>达到每分钟请求数限制。</li><li>请求大小一直限制为 10000 个字符。</li><li>每小时和每日配额限制转换器将接受的字符数。</li></ul>|
| TranslateApiException | AppId 超过配额。| 应用程序 ID 超出了每小时或每日配额。|

> [!NOTE]
> 将调整配额，以确保该服务的所有用户之间的公平性。

**查看 GitHub 上的代码示例**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)

## <a name="getusertranslations-method"></a>GetUserTranslations 方法

此方法可检索用户创建的翻译。 它提供按 uriPrefix、from、to、user、minRating 和 maxRating 请求参数分组的翻译。

**语法**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parameters**

| 参数 | 说明 |
|:---|:---|
| appId | **必需**：如果使用授权标头，请将 appid 字段留空，否则请指定包含 "Bearer" + " " + access token 的字符串。|
| uriPrefix| **可选**：一个包含翻译 URI 前缀的字符串。|
| from| **可选**：一个表示翻译文本语言代码的字符串。|
| 更改为| **可选**：一个字符串，表示要将文本翻译成的语言代码。|
| minRating| **可选**：一个整数值，表示已翻译文本的最低质量等级。 有效值介于 -10 和 10 之间。 默认值为 1。|
| maxRating| **可选**：一个整数值，表示已翻译文本的最高质量等级。 有效值介于 -10 和 10 之间。 默认值为 1。|
| user| **可有可无.一个字符串，用于根据提交的发起方筛选结果**|
| category| **可选**：一个包含翻译类别或领域的字符串。 此参数仅支持默认选项 general。|
| minDateUtc| **可选**：要检索翻译的起始日期。 日期必须采用 UTC 格式。|
| maxDateUtc| **可选**：要检索翻译的结束日期。 日期必须采用 UTC 格式。|
| skip| **可选**：要在页面上跳过的结果数。 例如，如果要跳过前 20 行结果，从第 21 个结果记录开始查看，则为此参数指定 20。 此参数的默认值为 0。|
| take| **可选**：要检索的结果数。 每个请求的最大数目为 100 个。 默认值为 50。|

> [!NOTE]
> skip 和 take 请求参数可为大量结果记录启用分页。

**返回值**

结果集包含 **UserTranslation** 的数组。 每个 UserTranslation 都具有以下元素：

| 字段 | 描述 |
|:---|:---|
| CreatedDateUtc| 使用 AddTranslation() 创建条目的日期|
| From| 源语言|
| OriginalText| 提交请求时使用的源语言文本|
|Rating |提交者在 AddTranslation() 方法调用中应用的等级|
|功能|    目标语言|
|TranslatedText|    在 AddTranslation() 方法调用中提交的翻译|
|URI|   AddTranslation() 方法调用中应用的 URI|
|用户   |用户名|

**异常**

| 异常 | 消息 | Conditions |
|:---|:---|:---|
| ArgumentOutOfRangeException | 参数“**maxDateUtc**”必须大于或等于“**minDateUtc**”。| 参数 **maxDateUtc** 的值小于参数 **minDateUtc** 的值。|
| TranslateApiException | IP 超过配额。| <ul><li>达到每分钟请求数限制。</li><li>请求大小一直限制为 10000 个字符。</li><li>每小时和每日配额限制转换器将接受的字符数。</li></ul>|
| TranslateApiException | AppId 超过配额。| 应用程序 ID 超出了每小时或每日配额。|

> [!NOTE]
> 将调整配额，以确保该服务的所有用户之间的公平性。

**查看 GitHub 上的代码示例**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)
