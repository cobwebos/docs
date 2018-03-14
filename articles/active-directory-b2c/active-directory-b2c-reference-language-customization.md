---
title: "使用语言自定义项 - Azure AD B2C | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 02/26/2018
ms.author: sama
ms.openlocfilehash: 332c6b4ffd841a2c7aef9db5c8ba9e843790f4d6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C：使用语言自定义

>[!NOTE]
>此功能目前以公共预览版提供。
>

策略使用语言自定义，可以适应不同的语言以满足客户需求。  Microsoft 提供 36 种语言的翻译（请参阅[其他信息](#additional-information)），但你也可以为任何语言提供自己的翻译。  即使体验是针对一种语言提供的，也可以自定义页面上的任何文本。  

## <a name="how-does-language-customization-work"></a>语言自定义的工作原理
通过语言自定义可选择用户旅程所用的语言。  启用该功能后，可以从应用程序提供查询字符串参数 ui_locales。  调用 Azure AD B2C 时，我们会根据指定的区域设置翻译页面。  使用此配置类型可以完全控制用户旅程中的语言，并忽略客户浏览器的语言设置。 或者，可能不需要对客户看到的语言拥有这种控制度。  如果未提供 ui_locales 参数，客户的体验由其浏览器设置确定。  仍可以控制要将用户旅程翻译成哪种语言，只需将该语言添加为支持的语言即可。  如果客户浏览器设置为显示你不希望支持的语言，则将改为显示你在受支持区域性中选为默认的语言。

1. **ui-locales 指定的语言** - 启用语言自定义后，用户旅程将翻译成此处指定的语言
2. **浏览器请求的语言** - 如果未指定 ui-locales，用户旅程将翻译成浏览器请求的语言（**如果该语言包含在受支持的语言中**）
3. **策略默认语言**：如果浏览器未指定语言，或者指定了不支持的语言，用户旅程将翻译成策略默认语言

>[!NOTE]
>如果使用自定义用户属性，需要提供自己的翻译。 有关详细信息，请参阅[自定义字符串](#customize-your-strings)。
>

## <a name="support-uilocales-requested-languages"></a>支持 ui_locales 请求的语言 
在语言自定义正式版发布之前创建的策略将需要先启用此功能。  之后创建的策略默认情况下已启用语言自定义。  现在，在策略中启用“语言自定义”后，可以通过添加 ui_locales 参数来控制用户旅程的语言。
1. [按照以下步骤在 Azure 门户上导航到“B2C 功能”页](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)。
2. 导航到想要启用翻译的策略。
3. 单击“语言自定义”。  
4. 单击顶部的“启用语言自定义”。
5. 阅读对话框中的内容，单击“是”。

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>选择要在用户旅程中启用哪些语言 
为用户旅程启用一组语言，以便在未提供 ui_locales 参数时翻译成这些语言。
1. 确保已根据前面的说明为策略启用“语言自定义”。
2. 在“编辑策略”页中，选择“语言自定义”。
3. 选择想要支持的语言。
4. 在属性窗格中，将“已启用”切换到“是”。  
5. 单击属性窗格顶部的“保存”。

>[!NOTE]
>如果未提供 ui_locales 参数，则仅当客户的浏览器语言已启用时，才将页面翻译成此语言
>

## <a name="customize-your-strings"></a>自定义字符串
“语言自定义”允许自定义用户旅程中的任何字符串。
1. 确保已根据前面的说明为策略启用“语言自定义”。
2. 在“编辑策略”页中，选择“语言自定义”。
3. 选择要自定义的语言。
4. 选择要编辑的页面。
5. 单击**下载默认值**（或者，如果以前已编辑这种语言，则单击“下载重写”）。 

执行这些步骤可以创建用于开始编辑字符串的 JSON 文件。

### <a name="changing-any-string-on-the-page"></a>更改页面上的任何字符串
1. 在 JSON 编辑器中打开根据前面的说明下载的 JSON 文件。
2. 找到想要更改的元素。  可以查找所需字符串的 `StringId`，或者查找想要更改的 `Value`。
3. 使用想要显示的内容更新 `Value` 属性。
4. 对于每个要更改的字符串，请记得将 `Override` 切换到 **True**。
5. 保存文件并上传所做的更改（可在下载 JSON 文件的同一位置找到上传控件）。 

>[!IMPORTANT]
>如果需要重写字符串，请确保将 `Override` 值设置为 `true`。  如果未更改该值，将忽略该条目。 
>

### <a name="changing-extension-attributes"></a>更改扩展属性
如果要更改自定义用户属性的字符串或者想要在 JSON 中添加一个字符串，该字符串需采用以下格式：
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

### <a name="using-localizedcollections"></a>使用 LocalizedCollections
若要为响应提供一组值列表，需要创建 `LocalizedCollections`。  `LocalizedCollections` 是 `Name` 和 `Value` 对的数组。  `Name` 是显示的内容，`Value` 是在声明中返回的内容。  若要添加 `LocalizedCollections`，需采用以下格式：

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

* `ElementId` 是此 `LocalizedCollections` 响应的用户属性
* `Name` 向用户显示的值
* `Value` 是选择此选项时要在声明中返回的内容

### <a name="upload-your-changes"></a>上传更改
1. 完成对 JSON 文件的更改后，请导航回到 B2C 租户。
2. 在“编辑策略”页中，选择“语言自定义”。
3. 选择想要提供翻译的语言。
4. 选择想要提供翻译的页面。
5. 单击文件夹图标，然后选择要上传的 JSON 文件。
6. 此更改将自动保存到你的策略。

## <a name="using-page-ui-customization-with-language-customization"></a>将页面 UI 自定义与语言自定义配合使用

可通过两种方法本地化 HTML 内容。  通过启用“语言自定义”。[](active-directory-b2c-reference-language-customization.md)  启用此功能可让 Azure AD B2C 将 OpenID 连接参数 `ui-locales` 转发到终结点。  内容服务器可使用此参数提供特定语言的自定义 HTML 页面。

或者，我们可以基于所使用的区域设置从不同位置拉取内容。  在已启用 CORS 的终结点中，可以设置文件夹结构以托管特定语言的内容，以便在提供通配符值 `{Culture:RFC5646}` 时调用正确的语言。  例如，如果将以下项设为自定义页 URI：

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
可以使用 `fr` 加载页面，当它拉取 html 和 css 内容时，将从以下项拉取：
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>自定义区域设置

还可以添加 Microsoft 目前未为其提供翻译的语言。  你将需要为策略中的所有字符串提供翻译。

1. 在“编辑策略”页中，选择“语言自定义”。
2. 从页面顶部选择“添加自定义语言”。
3. 在打开的上下文窗格中，通过输入有效的区域设置代码确定你要为其提供翻译的语言。
4. 对于每个页，可以下载一组英语重写，并处理翻译。
5. 完成 JSON 文件后，可以为每个页面上传这些文件。
6. 选择“启用”，你的策略即可为用户显示该语言。
7. 启用语言后，请记得保存它。

## <a name="additional-information"></a>其他信息

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>启用“语言自定义”后，“页面 UI 自定义”标签将保留为你的第一组重写
启用“语言自定义”时，会在英语 (en) 的 JSON 文件中保留以前使用“页面 UI 自定义”的标签编辑。  可以通过在“语言自定义”中上传语言资源来继续更改标签和其他字符串。
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft 致力于提供最新的翻译以供使用
我们将持续改进翻译，使其符合需要。  我们将识别 bug 和全局术语的更改，并在用户旅程中进行无缝更新。
### <a name="support-for-right-to-left-languages"></a>对从右向左书写的语言的支持
我们目前不支持从右向左书写的语言，如果需要此功能，请在 [Azure 反馈](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)上为此功能投票。
### <a name="social-identity-provider-translations"></a>社交标识提供者翻译
我们为社交登录提供 ui_locales OIDC 参数，但某些社交标识提供者（包括 Facebook 和 Google）并不遵循这种登录方式。 
### <a name="browser-behavior"></a>浏览器行为
Chrome 和 Firefox 都会请求其设置的语言，如果支持该语言，将先显示该语言，再显示默认语言。  Edge 目前不会请求语言，而是直接使用默认语言。

### <a name="what-languages-are-supported"></a>支持哪些语言？

| 语言              | 语言代码 |
|-----------------------|---------------|
| Bangla                | bn            |
| 捷克语                 | cs            |
| 丹麦语                | da            |
| 德语                | de            |
| 希腊语                 | el            |
| 英语               | en            |
| 西班牙语               | es            |
| 芬兰语               | fi            |
| 法语                | fr            |
| 古吉拉特语              | gu            |
| 印地语                 | hi            |
| 克罗地亚语              | hr            |
| 匈牙利语             | hu            |
| 意大利语               | it            |
| 日语              | ja            |
| 卡纳达语               | kn            |
| 朝鲜语                | ko            |
| 马拉雅拉姆语             | ml            |
| 马拉地语               | mr            |
| 马来语                 | ms            |
| 挪威博克马尔语      | nb            |
| 荷兰语                 | nl            |
| 旁遮普语               | pa            |
| 波兰语                | pl            |
| 葡萄牙语 - 巴西   | pt-br         |
| 葡萄牙语 - 葡萄牙 | pt-pt         |
| 罗马尼亚语              | ro            |
| 俄语               | ru            |
| 斯洛伐克语                | sk            |
| 瑞典语               | sv            |
| 泰米尔语                 | ta            |
| 泰卢固语                | te            |
| 泰语                  | th            |
| 土耳其语               | tr            |
| 简体中文  | zh-hans       |
| 繁体中文 | zh-hant       |
