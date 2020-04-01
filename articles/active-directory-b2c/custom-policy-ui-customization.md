---
title: 使用自定义策略自定义应用的用户界面
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure Active Directory B2C 中的自定义策略自定义用户界面。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e50d6d0623e87dfa68a7cc9744c3f595ff0179c6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396384"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 中的自定义策略自定义应用程序的用户界面

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

通过完成本文中的步骤，您可以创建一个注册和登录自定义策略与您的品牌和外观。 使用 Azure Active Directory B2C (Azure AD B2C)，几乎可以完全控制呈现给用户的 HTML 和 CSS 内容。 使用自定义策略时，需要以 XML 配置 UI 自定义，而不是使用 Azure 门户中的控件进行配置。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](custom-policy-get-started.md)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. 修改扩展名文件

要配置 UI 自定义，请将**ContentDefinition**及其子元素从基本文件复制到扩展文件。

1. 打开策略的基文件。 例如， <em> `SocialAndLocalAccounts/` </em>. 此基本文件是自定义策略初学者包中包含的策略文件之一，您应该在先决条件中获取，[从自定义策略开始](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)。
1. 搜索并复制 ContentDefinitions**** 元素的全部内容。
1. 打开扩展文件， 例如，TrustFrameworkExtensions.xml**。 搜索 BuildingBlocks**** 元素。 如果该元素不存在，请添加该元素。
1. 粘贴作为 BuildingBlocks**** 元素的子元素复制的 ContentDefinitions**** 元素的全部内容。
1. 在复制的 XML 中搜索包含 `Id="api.signuporsignin"` 的 ContentDefinition**** 元素。
1. 将 LoadUri**** 的值更改为上传到存储的 HTML 文件的 URL。 例如，`https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html` 。

    自定义策略应类似于以下代码段：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. 保存扩展文件。

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. 上传并测试更新的自定义策略

#### <a name="51-upload-the-custom-policy"></a>5.1 上传自定义策略

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录****。
1. 搜索并选择**Azure AD B2C**。
1. 在“策略”下，选择“Identity Experience Framework”。********
1. 选择 **"上载自定义策略**"。
1. 上传以前已更改的扩展文件。

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 使用 **"立即运行"** 测试自定义策略

1. 选择您上载的策略，然后选择 **"立即运行**"。
1. 现在，应该可以使用电子邮件地址进行注册了。

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>配置动态自定义页面内容 URI

通过使用 Azure AD B2C 自定义策略，可以在 URL 路径或查询字符串中发送参数。 通过将该参数传递到 HTML 终结点，可以动态更改页面内容。 例如，可以基于从 Web 或移动应用程序传递的参数，更改 Azure AD B2C 注册或登录页面上的背景图像。 参数可以是任何[声明解析器](claim-resolver-overview.md)，如应用程序 ID、语言 ID 或自定义查询字符串参数，如`campaignId`。

### <a name="sending-query-string-parameters"></a>发送查询字符串参数

要发送查询字符串参数，在[依赖方策略](relyingparty.md)中，添加如下`ContentDefinitionParameters`所示的元素。

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

在内容定义中，将 的值`LoadUri`更改为`https://<app_name>.azurewebsites.net/home/unified`。 自定义策略`ContentDefinition`应类似于以下代码段：

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

当 Azure AD B2C 加载页面时，它会调用 Web 服务器终结点：

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>动态页面内容 URI

内容可以根据所使用的参数从不同位置提取。 在启用 CORS 的终结点中，设置一个文件夹结构以承载内容。 例如，您可以在以下结构中组织内容。 *根文件夹/文件夹每种语言/您的html文件*。 例如，自定义的页面 URI 可能类似于以下内容：

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C 为法语发送两`fr`个字母的 ISO 代码：

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>后续步骤

有关可自定义的 UI 元素的详细信息，请参阅[用户流的 UI 自定义参考指南](customize-ui-overview.md)。
