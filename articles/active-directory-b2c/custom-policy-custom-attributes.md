---
title: 将您自己的属性添加到自定义策略
titleSuffix: Azure AD B2C
description: 有关使用扩展属性、自定义属性以及将其包含在用户界面中的演练。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc5204518cb6e801ba661aecd5498a501122225f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473653"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure 活动目录 B2C：在自定义配置文件策略中启用自定义属性

在["添加声明"中，并使用自定义策略自定义用户输入](custom-policy-configure-user-input.md)一文中，您将了解如何使用内置[用户配置文件属性](user-profile-attributes.md)。 在本文中，您将在 Azure 活动目录 B2C （Azure AD B2C） 目录中启用自定义属性。 稍后，您可以在[用户流](user-flow-overview.md)或[自定义策略](custom-policy-get-started.md)中同时使用新属性作为自定义声明。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>先决条件

遵循文章 [Azure Active Directory B2C：自定义策略入门](custom-policy-get-started.md)中的步骤进行操作。

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>使用自定义属性收集有关客户的信息 

Azure AD B2C 目录附带一[组内置属性](user-profile-attributes.md)。 但是，您通常需要创建自己的属性来管理特定方案，例如：在以下情况下：

* 面向客户的应用程序需要保留一个**LoyalId**属性。
* 标识提供程序具有唯一的用户标识符 **，唯一用户 GUID，** 必须保留该标识符。
* 自定义用户旅程需要保留用户的状态 **，迁移状态**，以便对其他逻辑进行操作。

Azure AD B2C 允许您扩展存储在每个用户帐户的属性集。 您还可以使用[Microsoft 图形 API](manage-user-accounts-graph-api.md)读取和写入这些属性。

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 扩展应用

扩展属性只能在应用程序对象上注册，即使它们可能包含用户的数据。 扩展属性附加到称为 b2c 扩展应用程序的应用程序。 不要修改此应用程序，因为它被 Azure AD B2C 用于存储用户数据。 您可以在 Azure AD B2C、应用注册下找到此应用程序。

术语扩展属性、自定义属性和自定义声明在本文的上下文中引用相同的内容******。 名称会因上下文（应用程序、对象、策略）而异。

## <a name="get-the-application-properties"></a>获取应用程序属性

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”**** 筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”****。 或者，选择“所有服务”**** 并搜索并选择“Azure AD B2C”****。
1. 选择**应用注册（预览），** 然后选择 **"所有应用程序**"。
1. 选择`b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`应用程序。
1. 将以下标识符复制到剪贴板并保存：
    * **应用程序 ID**. 示例：`11111111-1111-1111-1111-111111111111`。
    * **对象 ID**. 示例：`22222222-2222-2222-2222-222222222222`。

## <a name="modify-your-custom-policy"></a>修改自定义策略

要在策略中启用自定义属性，请在 AAD 通用技术配置文件元数据中提供**应用程序 ID**和应用程序**对象 ID。** *AAD-通用*技术配置文件位于基本[Azure 活动目录](active-directory-technical-profile.md)技术配置文件中，并支持 Azure AD 用户管理。 其他 Azure AD 技术配置文件包括 AAD 通用以利用其配置。 覆盖扩展文件中的 AAD 通用技术配置文件。

1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 找到 ClaimsProviders 元素。 向声明提供程序元素添加新的声明提供程序。
1. 替换为`ApplicationObjectId`以前记录的对象 ID。 然后替换为`ClientId`以前记录在以下代码段的应用程序 ID。

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      <TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>上传自定义策略

1. 登录到 Azure[门户](https://portal.azure.com)。
2. 通过在顶部菜单中选择**目录 + 订阅**筛选器并选择包含 Azure AD B2C 租户的目录，请确保使用的目录包含 Azure AD 租户。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
4. 选择**身份体验框架**。
5. 选择 **"上载自定义策略**"，然后上载您更改的 TrustFramework 扩展.xml 策略文件。

> [!NOTE]
> 第一次 Azure AD 技术配置文件将声明保存到目录时，它会检查自定义属性是否存在。 如果没有，它将创建自定义属性。  

## <a name="create-a-custom-attribute-through-azure-portal"></a>通过 Azure 门户创建自定义属性

相同的扩展属性在内置策略和自定义策略之间共享。 通过门户体验添加自定义属性时，将使用每个 B2C 租户中存在的**b2c 扩展应用**来注册这些属性。

您可以在自定义策略中使用门户 UI 来创建这些属性。 按照有关如何在 Azure[活动目录 B2C 中定义自定义属性](user-flow-custom-attributes.md)的指导。 在门户中创建属性 **"忠诚 Id"** 时，必须引用如下：

|“属性”     |适用范围 |
|---------|---------|
|`extension_loyaltyId`  | 自定义策略|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

下面的示例演示了在 Azure AD B2C 自定义策略声明定义中使用自定义属性。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

下面的示例演示了在技术配置文件、输入、输出和持久声明中使用 Azure AD B2C 自定义策略中的自定义属性。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>在策略中使用自定义属性

按照有关如何[使用自定义策略添加声明和自定义用户输入的指导](custom-policy-configure-user-input.md)。 此示例使用内置声明"城市"。 要使用自定义属性，请将"城市"替换为您自己的自定义属性。


## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [Azure AD B2C 用户配置文件属性](user-profile-attributes.md)
- [扩展属性定义](user-profile-attributes.md#extension-attributes)
- [使用微软图形管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)
