---
title: 将自己的属性添加到自定义策略
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
ms.openlocfilehash: b5990f79891a9cbc0d18c3499691a3d7ef309a73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678256"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C：启用自定义配置文件策略中的自定义属性

在[添加声明和使用自定义策略自定义用户输入](custom-policy-configure-user-input.md)一文中，你将了解如何使用内置[用户配置文件属性](user-profile-attributes.md)。 本文介绍如何在 Azure Active Directory B2C （Azure AD B2C）目录中启用自定义属性。 稍后，你可以将新属性同时用作[用户流](user-flow-overview.md)或[自定义策略](custom-policy-get-started.md)中的自定义声明。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>必备条件

遵循文章 [Azure Active Directory B2C：自定义策略入门](custom-policy-get-started.md)中的步骤进行操作。

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>使用自定义属性来收集有关客户的信息 

Azure AD B2C 目录附带了一[组内置属性](user-profile-attributes.md)。 但是，您通常需要创建自己的属性以管理您的特定方案，例如：

* 面向客户的应用程序需要保留**LoyaltyId**属性。
* 标识提供程序具有必须保持的唯一用户标识符**uniqueUserGUID**。
* 自定义用户旅程需要保留用户的状态**migrationStatus**，以供其他逻辑操作。

Azure AD B2C 允许扩展存储在每个用户帐户上的属性集。 还可以通过使用[MICROSOFT GRAPH API](manage-user-accounts-graph-api.md)来读取和写入这些属性。

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 扩展应用

扩展属性只能在应用程序对象上注册，即使它们可能包含用户的数据也是如此。 扩展属性附加到称为 "b2c-扩展-应用" 的应用程序。 请不要修改此应用程序，因为它由用于存储用户数据的 Azure AD B2C 使用。 可以在 Azure AD B2C 应用注册下找到此应用程序。

术语扩展属性、自定义属性和自定义声明在本文的上下文中引用相同的内容******。 名称会因上下文（应用程序、对象、策略）而异。

## <a name="get-the-application-properties"></a>获取应用程序属性

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”  筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 选择 "**应用注册（预览版）**"，然后选择 "**所有应用程序**"。
1. 选择`b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`应用程序。
1. 将以下标识符复制到剪贴板并保存：
    * **应用程序 ID**。 示例：`11111111-1111-1111-1111-111111111111`。
    * **对象 ID**。 示例：`22222222-2222-2222-2222-222222222222`。

## <a name="modify-your-custom-policy"></a>修改自定义策略

若要在策略中启用自定义属性，请在 AAD 通用技术配置文件元数据中提供**应用程序 id**和应用程序**对象 id** 。 *AAD 通用*技术配置文件位于基本[Azure Active Directory](active-directory-technical-profile.md)技术配置文件中，并提供对 Azure AD 用户管理的支持。 其他 Azure AD 技术配置文件包含用于利用其配置的 AAD 公用配置文件。 覆盖扩展文件中的 AAD 通用技术配置文件。

1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 找到 ClaimsProviders 元素。 将新的 ClaimsProvider 添加到 ClaimsProviders 元素。
1. 替换`ApplicationObjectId`为之前记录的对象 ID。 然后， `ClientId`将替换为你之前在以下代码片段中记录的应用程序 ID。

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
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>上传自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含 Azure AD B2C 租户的目录，确保使用的是包含 Azure AD 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
4. 选择 "**标识体验框架**"。
5. 选择 "**上载自定义策略**"，然后上传所更改的 trustframeworkextensions.xml 策略文件。

> [!NOTE]
> 第一次 Azure AD 技术配置文件将声明保留在目录中时，它会检查自定义属性是否存在。 如果不是，则创建自定义属性。  

## <a name="create-a-custom-attribute-through-azure-portal"></a>通过 Azure 门户创建自定义属性

在内置策略和自定义策略之间共享相同的扩展属性。 通过门户体验添加自定义属性时，这些属性是使用每个 B2C 租户中存在的**b2c 扩展应用**注册的。

你可以通过在自定义策略中使用门户 UI 前后使用门户 UI 来创建这些属性。 按照如何[在 Azure Active Directory B2C 中定义自定义属性](user-flow-custom-attributes.md)的指导进行操作。 在门户中创建属性**loyaltyId**时，必须按如下所示进行引用：

|名称     |适用范围 |
|---------|---------|
|`extension_loyaltyId`  | 自定义策略|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

下面的示例演示如何在 Azure AD B2C 自定义策略声明定义中使用自定义特性。

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

下面的示例演示如何使用自定义属性，该属性在技术配置文件、输入、输出和持久化声明中 Azure AD B2C 自定义策略中。

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

按照有关如何[使用自定义策略添加声明和自定义用户输入](custom-policy-configure-user-input.md)的指导进行操作。 此示例使用内置声明 "city"。 若要使用自定义属性，请将 "city" 替换为你自己的自定义属性。


## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [Azure AD B2C 用户配置文件属性](user-profile-attributes.md)
- [扩展属性定义](user-profile-attributes.md#extension-attributes)
- [通过 Microsoft Graph 管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)
