---
title: "Azure Active Directory B2C：使用语言自定义 | Microsoft Docs"
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
ms.date: 04/25/2017
ms.author: sama
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 03c65775d7fe7eee0d14ca4551ae8a332dbc39b2
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017


---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C：使用语言自定义

>[!NOTE] 
>此功能目前以公共预览版提供。  建议在测试租户中使用此功能。  我们并未计划在正式版中基于预览版做出重大更改，但我们保留做出此类更改，以改进功能的权利。  在试用功能后，请根据你的体验提供反馈，并告知如何改进该功能。  可以在 Azure 门户中使用右上角的笑脸图标工具提供反馈。   如果在预览阶段，需要此功能才能实现某项业务要求，请将你的情景告诉我们，以便我们提供适当的指导和帮助。  可以通过 [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com) 联系我们。

使用语言自定义可以根据的客户需求将用户旅程更改为不同的语言。  我们提供 36 种语言的翻译（请参阅[其他信息](#additional-information)）。  即使体验是针对一种语言提供的，你也可以根据需要自定义页面上的任何文本。  

## <a name="how-does-language-customization-work"></a>语言自定义的工作原理
语言自定义可让你选择用户旅程所用的语言。  启用该功能后，可以从应用程序提供查询字符串参数 ui_locales。  当你调用 Azure AD B2C 时，我们会根据指定的区域设置翻译页面。  使用配置类型可以完全控制用户旅程中的语言，并忽略客户浏览器的语言设置。 或者，你可能不需要对客户看到的语言拥有这种控制度。  如果未提供 ui_locales 参数，客户的体验将由其浏览器设置确定。  你仍可以控制要将用户旅程翻译成哪种语言，只需将该语言添加为支持的语言即可。  如果客户浏览器设置为显示你不想要支持的语言，则会改为显示你选作受支持区域性中默认语言的语言。

1. **ui-locales 指定的语言** - 启用语言自定义后，用户旅程将翻译成此处指定的语言
2. **浏览器请求的语言** - 如果未指定 ui-locales，用户旅程将翻译成浏览器请求的语言（**如果该语言包含在受支持的语言中**）
3. **策略默认语言**：如果浏览器未指定语言，或者指定了不支持的语言，用户旅程将翻译成策略默认语言

>[!NOTE]
>如果使用自定义用户属性，需要提供自己的翻译。 有关详细信息，请参阅[自定义字符串](#customize-your-strings)。
>

## <a name="support-uilocales-requested-languages"></a>支持 ui_locales 请求的语言 
现在，在策略中启用“语言自定义”后，可以通过添加 ui_locales 参数来控制用户旅程的语言。
1. [遵循以下步骤在 Azure 门户中导航到 B2C 功能边栏选项卡。](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-the-b2c-features-blade)
2. 导航到想要启用翻译的策略。
3. 单击“语言自定义”。
4. 仔细阅读警告。  启用“语言自定义”后，无法将它关闭。
5. 打开此功能，然后单击“确定”。
6. 在“编辑策略”边栏选项卡的左上角保存策略。

## <a name="select-which-languages-your-user-journey-supports"></a>选择用户旅程支持的语言 
创建未提供 ui_locales 参数时，要将用户旅程翻译成的允许语言列表。
1. 确保已根据前面的说明为策略启用“语言自定义”。
2. 在“编辑策略”边栏选项卡中，选择“语言自定义”。
3. 随后你将转到“支持的语言”边栏选项卡。  在此处可以选择“添加语言”。
4. 选择想要支持的所有语言。  

>[!NOTE]
>如果未提供 ui_locales 参数，则仅当客户的浏览器语言已列在此列表中时，才将页面翻译成此语言
>

5. 单击底部的“确定”
6. 关闭“语言自定义”边栏选项卡并**保存**策略。

## <a name="customize-your-strings"></a>自定义字符串
“语言自定义”允许自定义用户旅程中的任何字符串。
1. 确保已根据前面的说明为策略启用“语言自定义”。
2. 在“编辑策略”边栏选项卡中，选择“语言自定义”。
3. 在左侧导航菜单中选择“下载内容”。
4. 选择要编辑的页面。
5. 在下拉列表中，选择要编辑的语言。
6. 单击“下载”。 

执行这些步骤可以创建用于开始编辑字符串的 JSON 文件。

### <a name="changing-any-string-on-the-page"></a>更改页面上的任何字符串
1. 在 JSON 编辑器中打开根据前面的说明下载的 JSON 文件。
2. 找到想要更改的元素。  可以查找所需字符串的 `StringId`，或者查找想要更改的 `Value`。
3. 使用想要显示的内容更新 `Value` 属性。
4. 保存文件并上传更改。

### <a name="changing-extension-attributes"></a>更改扩展属性
如果你要更改自定义用户属性的字符串或者想要在 JSON 中添加一个字符串，该字符串需采用以下格式：
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>如果需要重写字符串，请确保将 `Override` 值设置为 `true`。  如果未更改该值，将忽略该条目。 
>

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
      "Override": false,
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
>[!IMPORTANT]
>如果需要重写字符串，请确保将 `Override` 值设置为 `true`。  如果未更改该值，将忽略该条目。 
>

* `ElementId` 是此 `LocalizedCollections` 响应的用户属性
* `Name` 向用户显示的值
* `Value` 是选择此选项时要在声明中返回的内容

### <a name="upload-your-changes"></a>上传更改
1. 完成对 JSON 文件的更改后，请导航回到 B2C 租户。
2. 在“编辑策略”边栏选项卡中，选择“语言自定义”。
3. 在左侧导航菜单中选择“上传内容”。
4. 选择想要上传其更改的页面。
5. 若要上传以前已为其提供了 JSON 的语言，需要删除以前的条目。  在该语言的右侧单击 `...`，然后选择“删除”即可删除该条目。
6. 在左上角单击“添加”。
7. 选择 JSON 文件的语言。
8. 单击右侧的文件夹按钮，然后浏览到该 JSON 文件。
9. 在边栏选项卡的底部单击“上传”按钮。
10. 返回“编辑策略”边栏选项卡，然后单击“保存”。

## <a name="additional-information"></a>其他信息
### <a name="recommendations-for-language-customization"></a>有关“语言自定义”的建议
我们建议只在明确想要替换的字符串的语言资源中输入条目。  我们针对基于所有 JSON 翻译编译的文件实施大小限制。  如果文件太大，会影响用户旅程的性能。
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>启用“语言自定义”后，将删除页面 UI 自定义标签
启用“语言自定义”时，以前使用页面 UI 自定义对标签所做的编辑将被删除，但自定义用户属性除外。  执行此项更改的目的是避免可以编辑字符串的位置发生冲突。  可以通过在“语言自定义”中上传语言资源来继续更改标签和其他字符串。
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft 承诺提供最新的翻译供你使用
我们将持续改进翻译，使其符合你的需要。  我们将识别 bug 和全局术语的更改，并在用户旅程中进行无缝更新。
### <a name="support-for-right-to-left-languages"></a>对从右向左书写的语言的支持
不支持从右向左书写的语言，如果需要此功能，请在 [Azure 反馈](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)上为此功能投票。
### <a name="social-identity-provider-translations"></a>社交标识提供者翻译
我们为社交登录提供 ui_locales OIDC 参数，但某些社交标识提供者（包括 Facebook 和 Google）并不遵循这种登录方式。 
### <a name="browser-behavior"></a>浏览器行为
Chrome 和 Firefox 都会请求其设置的语言，如果支持该语言，将先显示该语言，再显示默认语言。  Edge 目前不会请求语言，而是直接使用默认语言。
### <a name="known-issues"></a>已知问题
* 目前无法为配置文件编辑策略中的 MFA 页面上传语言资源。
* 不会根据响应类型的需要为值生成 `LocalizedCollections`
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>如果所需的语言不受支持怎么办？
我们正在规划提供此功能的扩展，使你能够为“自定义语言”上传 JSON 资源。  此功能允许指定任何语言的名称和语言代码，并提供该语言的*所有*翻译。  如果需要此功能，请将你的方案发送到 [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com)。  

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

