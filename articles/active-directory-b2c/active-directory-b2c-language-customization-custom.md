---
title: Azure Active Directory B2C 自定义策略中的语言自定义 | Microsoft Docs
description: 了解如何在自定义策略中针对多种语言本地化内容。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 11/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c8deabd4d0a4126365b014875624525d5b1f3063
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711750"
---
# <a name="language-customization-in-custom-policies"></a>自定义策略中的语言自定义

> [!NOTE]
> 此功能目前以公共预览版提供。
> 

在自定义策略中，语言自定义与内置策略的工作方式相同。  请参阅内置[文档](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization)，该文档介绍了如何根据参数和浏览器设置选择语言的行为。

## <a name="enable-supported-languages"></a>启用支持的语言
如果未指定 ui-locales，并且用户的浏览器要求使用其中一种语言，系统会向用户显示支持的语言。  

支持的语言在 `<BuildingBlocks>` 中按以下格式定义：

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

默认语言和支持的语言的行为方式与内置策略中的行为方式相同。

## <a name="enable-custom-language-strings"></a>启用自定义语言字符串

创建自定义语言字符串需要两个步骤：
1. 编辑页面的 `<ContentDefinition>` 以指定所需语言的资源 ID
2. 使用 `<BuildingBlocks>` 中的对应 ID 创建 `<LocalizedResources>`

请记住，可以在扩展文件或依赖策略文件中放置 `<ContentDefinition>` 和 `<BuildingBlock>`，具体取决于更改是否要包含在所有继承策略中。

### <a name="edit-the-contentdefinition-for-the-page"></a>编辑页面的 ContentDefinition

对于每个要本地化的页面，可以在 `<ContentDefinition>` 中指定每种语言代码要查找的语言资源。

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

在此示例中，法语 (fr) 和英语 (en) 自定义字符串被添加到统一注册或登录页面。  每个 `LocalizedResourcesReference` 的 `LocalizedResourcesReferenceId` 与其区域设置相同，但是可使用任何字符串作为 ID。  对于每种语言和页面组合，必须创建相应的 `<LocalizedResources>`，如下所示。


### <a name="create-the-localizedresources"></a>创建 LocalizedResources

替代包含在 `<BuildingBlocks>` 中，每个页面和为每个页面在 `<ContentDefinition>` 中指定每种语言都有一个 `<LocalizedResources>`。  每个替代被指定为一个 `<LocalizedString>`，如下面的示例所示：

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

页面中有四种类型的字符串元素：

**ClaimsProvider** - 标识提供程序的标签（Facebook、Google、Azure AD 等）**ClaimType** - 特性及其相应的帮助文本的标签，或字段验证错误 **UxElement** - 默认情况下，页面中的其他字符串元素，如按钮、链接或文本 **ErrorMessage** - 窗体验证错误消息

请确保 `StringId` 与正在使用这些替代的页面匹配，否则上传时会被策略验证阻止。  