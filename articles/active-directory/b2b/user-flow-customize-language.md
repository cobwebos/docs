---
title: Azure AD 用户流中的语言自定义
description: 了解如何在用户流中自定义语言体验。
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed56acc9276177951919a3bc63c8a1dc3876e1c4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594696"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Azure Active Directory（预览版）中的语言自定义
|     |
| --- |
| 自助注册是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

用户流可以使用 Azure Active Directory (Azure AD) 中的语言自定义来适应不同的语言，从而满足用户需求。 Microsoft 提供 [36 种语言](#supported-languages)的翻译。 即使体验是针对一种语言提供的，也可以在“属性集合”页上自定义属性名称。

## <a name="how-language-customization-works"></a>语言自定义的工作原理

默认情况下，会为注册用户启用语言自定义，以确保一致的注册体验。 在注册期间，可以使用语言来修改在属性收集过程中向用户显示的字符串。

> [!NOTE]
> 如果使用自定义用户属性，需要提供自己的翻译。 有关详细信息，请参阅[自定义字符串](#customize-your-strings)。

## <a name="customize-your-strings"></a>自定义字符串

使用语言自定义可以自定义用户流中的任何字符串。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 选择“用户流(预览版)”。
3. 选择想要启用翻译的用户流。
4. 选择“语言”。
5. 在用户流的“语言”页面，选择想要自定义的语言。
6. 展开“属性集合”页。
7. 选择“下载默认值”（如果以前已编辑这种语言，则选择“下载重写”）。 

执行这些步骤可以创建用于开始编辑字符串的 JSON 文件。

### <a name="change-any-string-on-the-page"></a>更改页面上的任何字符串

1. 在 JSON 编辑器中打开根据前面的说明下载的 JSON 文件。
1. 找到想要更改的元素。 可以查找所需字符串的 `StringId`，或者查找想要更改的 `Value` 属性。
1. 使用想要显示的内容更新 `Value` 属性。
1. 对于每个要更改的字符串，将 `Override` 更改为 `true`。
1. 保存文件并上传更改。 （可在下载 JSON 文件的同一位置找到上传控件。）

> [!IMPORTANT]
> 如果需要重写字符串，请确保将 `Override` 值设置为 `true`。 如果未更改该值，将忽略该条目。

### <a name="change-extension-attributes"></a>更改扩展属性

若要更改自定义用户属性的字符串或者想要在 JSON 中添加一个字符串，该字符串需采用以下格式：

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

将 `<ExtensionAttribute>` 替换为自定义用户属性的名称。

将 `<ExtensionAttributeValue>` 替换为要显示的新字符串。

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>使用 LocalizedCollections 提供值列表

若要为响应提供一组值列表，需要创建 `LocalizedCollections` 属性。 `LocalizedCollections` 是 `Name` 和 `Value` 对的数组。 项目的顺序将是它们显示的顺序。 若要添加 `LocalizedCollections`，请使用以下格式：

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` 是此 `LocalizedCollections` 属性响应的用户属性。
* `Name` 是向用户显示的值。
* `Value` 是选择此选项时要在声明中返回的内容。

### <a name="upload-your-changes"></a>上传更改

1. 完成对 JSON 文件的更改后，返回到租户。
1. 选择“用户流”，单击想要启用翻译的用户流。
1. 选择“语言”。
1. 选择要翻译成的语言。
1. 选择“属性集合”页。
1. 选择文件夹图标，选择要上传的 JSON 文件。

更改将自动保存到用户流。

## <a name="additional-information"></a>其他信息

### <a name="page-ui-customization-labels-as-overrides"></a>页面 UI 自定义标签保留为重写

启用语言自定义时，会在英语 (en) 的 JSON 文件中保留以前使用页面 UI 自定义的标签编辑。 可以通过在语言自定义中上传语言资源来继续更改标签和其他字符串。

### <a name="up-to-date-translations"></a>最新的翻译

Microsoft 致力于提供最新的翻译以供使用。 Microsoft 会持续改进翻译，使其符合需要。 Microsoft 将识别全局术语中的 bug 和更改，并在用户流中进行无缝更新。

### <a name="support-for-right-to-left-languages"></a>对从右向左书写的语言的支持

Microsoft 目前不支持从右向左书写的语言。 你可以通过使用自定义区域设置并使用 CSS 更改字符串的显示方式来实现此目的。 如果需要此功能，请在 [Azure 反馈](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)中为此功能投票。

### <a name="social-identity-provider-translations"></a>社交标识提供者翻译

Microsoft 为社交登录名提供 `ui_locales` OIDC 参数。 但某些社交标识提供者（包括 Facebook 和 Google）并不遵循此参数。

### <a name="browser-behavior"></a>浏览器行为

Chrome 和 Firefox 都会请求其设置的语言。 如果支持该语言，将先显示该语言，再显示默认语言。 Microsoft Edge 目前不会请求语言，而是直接使用默认语言。

## <a name="supported-languages"></a>支持的语言

Azure AD 支持以下语言。 用户流语言由 Azure AD 提供。 多重身份验证 (MFA) 通知语言通过 [Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) 提供。

| 语言              | 语言代码 | 用户流         | MFA 通知  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| 阿拉伯语                | ar            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 保加利亚语             | bg            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| Bangla                | bn            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 加泰罗尼亚语               | ca            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 捷克语                 | cs            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 丹麦语                | da            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 德语                | de            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 希腊语                 | el            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 英语               | en            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 西班牙语               | es            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 爱沙尼亚语              | et            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 巴斯克语                | eu            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 芬兰语               | fi            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 法语                | fr            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 加利西亚语              | gl            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 古吉拉特语              | gu            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 希伯来语                | he            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| Hindi                 | hi            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 克罗地亚语              | hr            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 匈牙利语             | hu            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 印度尼西亚语            | id            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 意大利语               | it            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 日语              | ja            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 哈萨克语                | kk            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 卡纳达语               | kn            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 韩语                | ko            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 立陶宛语            | lt            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 拉脱维亚语               | lv            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 马拉雅拉姆语             | ml            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 马拉地语               | mr            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 马来语                 | ms            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 挪威博克马尔语      | nb            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 荷兰语                 | nl            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 挪威语             | 否            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 旁遮普语               | pa            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 波兰语                | pl            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 葡萄牙语 - 巴西   | pt-br         | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 葡萄牙语 - 葡萄牙 | pt-pt         | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 罗马尼亚语              | ro            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 俄语               | ru            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 斯洛伐克语                | sk            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 斯洛文尼亚语             | sl            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 塞尔维亚语 - 西里尔    | sr-cryl-cs    | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 塞尔维亚语 - 拉丁       | sr-latn-cs    | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 瑞典语               | sv            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 泰米尔语                 | ta            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 泰卢固语                | te            | ![是](./media/user-flow-customize-language/yes.png) | ![否](./media/user-flow-customize-language/no.png) |
| 泰语                  | th            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 土耳其语               | tr            | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 乌克兰语             | uk            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 越南语            | vi            | ![否](./media/user-flow-customize-language/no.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 简体中文  | zh-hans       | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |
| 繁体中文 | zh-hant       | ![是](./media/user-flow-customize-language/yes.png) | ![是](./media/user-flow-customize-language/yes.png) |