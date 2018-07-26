---
title: 向 Azure Active Directory B2C 的自定义策略添加你自己的属性 | Microsoft Docs
description: 有关使用扩展属性、自定义属性以及将其包含在用户界面中的演练。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 41d0d3826acdd374a86588fbd8e7a23d03810fda
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113774"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C：在自定义配置文件编辑策略中使用自定义属性

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在本文中，可在 Azure Active Directory (Azure AD) B2C 目录中创建自定义属性。 将在配置文件编辑用户旅程中将此新属性用作自定义声明。

## <a name="prerequisites"></a>先决条件

遵循文章 [Azure Active Directory B2C：自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤进行操作。

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>使用自定义属性来收集有关使用自定义策略的 Azure AD B2C 中的客户信息
Azure AD B2C 目录附带了一组内置属性。 示例包括：Given Name、Surname、City、Postal Code 和 userPrincipalName。 通常需要创建自己的属性，如下所示：
* 面向客户的应用程序需要保留如 LoyaltyNumber 等属性 **。**
* 标识提供者具有必须保存的唯一用户标识符，例如 uniqueUserGUID。
* 自定义用户旅程需要保留用户的状态，例如 migrationStatus。

Azure AD B2C 扩展存储在每个用户帐户中的属性集。 还可以使用 [Azure AD 图形 API](active-directory-b2c-devquickstarts-graph-dotnet.md) 读取和写入这些属性。

扩展属性扩展目录中用户对象的架构。 术语扩展属性、自定义属性和自定义声明在本文的上下文中引用相同的内容。 名称会因上下文（应用程序、对象、策略）而异。

尽管扩展属性可以包含用户的数据，但它们只能在应用程序对象中注册。 属性将附加到应用程序。 应用程序对象必须具有写入访问权限，使其能够注册扩展属性。 可将 100 个扩展属性（针对所有类型和所有应用程序）写入任何单个对象。 将扩展属性添加到目标目录类型，然后在 Azure AD B2C 目录租户中立即可供访问。
如果删除了该应用程序，也会删除这些扩展属性以及其中为所有用户包含的所有数据。 如果应用程序删除了某个扩展属性，该属性将从目标 Directory 对象中移除，同时会删除值。

扩展属性只在租户中已注册的应用程序的上下文中存在。 该应用程序的对象 ID 必须包含在使用它的 TechnicalProfile 中。

>[!NOTE]
>Azure AD B2C 目录通常包含名为 `b2c-extensions-app` 的 Web 应用。 此应用程序主要由通过 Azure 门户创建的自定义声明的 B2C 内置策略使用。 仅建议高级用户使用此应用程序来注册 B2C 自定义策略的扩展。  
本文的“后续步骤”部分中提供了相关说明。


## <a name="create-a-new-application-to-store-the-extension-properties"></a>创建用于存储扩展属性的新应用程序

1. 打开浏览会话并导航到 [Azure 门户](https://portal.azure.com)。 使用要配置的 B2C 目录的管理凭据登录。
2. 在左侧导航菜单中，选择“Azure Active Directory”。 可能需要选择“更多服务”才能找到该选项。
3. 选择“应用注册”。 选择“新建应用程序注册”。
4. 提供了以下条目：
    * Web 应用的名称：WebApp-GraphAPI-DirectoryExtensions。
    * 应用程序类型：Web 应用/API。
    * 登录 URL：https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions。
5. 选择**创建**。
6. 选择新建的 Web 应用程序。
7. 选择“设置” > “所需权限”。
8. 选择 API“Windows Azure Active Directory”。
9. 在应用程序权限“读取和写入目录数据”中输入一个选中标记。 再选择“保存”。
10. 选择“授予权限”，然后单击“是”确认。
11. 将以下标识符复制到剪贴板并保存：
    * **应用程序 ID**。 示例：`103ee0e6-f92d-4183-b576-8c3739027780`。
    * **对象 ID**。 示例：`80d8296a-da0a-49ee-b6ab-fd232aa45201`。



## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>修改自定义策略以添加 ApplicationObjectId

当按照 [Azure Active Directory B2C：自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤进行操作时，你已下载并修改了名为 TrustFrameworkBase.xml、TrustFrameworkExtensions.xml、SignUpOrSignin.xml、ProfileEdit.xml 和 PasswordReset.xml 的[示例文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)。 在此步骤中，请对这些文件进行更多修改。

* 打开 **TrustFrameworkBase.xml** 文件，并添加 `Metadata` 节，如以下示例所示。 插入先前为 `ApplicationObjectId` 值记录的对象 ID 以及为 `ClientId` 值记录的应用程序 ID： 

    ```xml
    <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              
          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->
              
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> 当 TechnicalProfile 首次写入新建的扩展属性中时，可能会遇到一次性错误。 首次使用时将创建扩展属性。  

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>在用户旅程中使用新的扩展属性或自定义属性

1. 打开 **ProfileEdit.xml** 文件。
2. 添加自定义声明 `loyaltyId`。 通过在 `<RelyingParty>` 元素中包含自定义声明，它将包含在应用程序的令牌中。
    
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. 打开 **TrustFrameworkExtensions.xml** 文件，将 `<ClaimsSchema>` 元素及其子元素添加到 `BuildingBlocks` 元素：

    ```xml
    <BuildingBlocks>
      <ClaimsSchema> 
        <ClaimType Id="extension_loyaltyId"> 
          <DisplayName>Loyalty Identification Tag</DisplayName> 
          <DataType>string</DataType> 
          <UserHelpText>Your loyalty number from your membership card</UserHelpText> 
          <UserInputType>TextBox</UserInputType> 
        </ClaimType> 
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. 将相同的 `ClaimType` 定义添加到 **TrustFrameworkBase.xml**。 不必在基本文件和扩展文件中添加 `ClaimType` 定义。 但是，后续步骤会在基本文件中将 `extension_loyaltyId` 添加到 TechnicalProfiles。 因此，策略验证程序在没有此元素的情况下会拒绝上传基本文件。 这可能有助于在 TrustFrameworkBase.xml 文件中跟踪名为“ProfileEdit”的用户旅程的执行操作。 在编辑器中搜索同名的用户旅程。 观察业务流程步骤 5 是否调用 TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate。 请搜索并检查此 TechnicalProfile，熟悉相关的流。

5. 打开 TrustFrameworkBase.xml 文件，并在 TechnicalProfile SelfAsserted-ProfileUpdate 中添加 `loyaltyId` 作为输入和输出声明：

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        
        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. 在 TrustFrameworkBase.xml 文件中，将 `loyaltyId` 声明添加到 TechnicalProfile AAD-UserWriteProfileUsingObjectId。 此添加将声明的值保留在目录中当前用户的扩展属性中：

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. 在 TrustFrameworkBase.xml 文件中，将 `loyaltyId` 声明添加到 TechnicalProfile AAD-UserReadUsingObjectId，以便每次用户登录时都会读取扩展属性的值。 到目前为止，TechnicalProfiles 仅在本地帐户流中发生过更改。 如果社交帐户或联合帐户流中需要新属性，则需要更改一组不同的 TechnicalProfiles。 请参阅“后续步骤”部分。

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 打开“Azure AD B2C”边栏选项卡并导航到“标识体验框架” > “自定义策略”。
1. 选择已上传的自定义策略。 选择“立即运行”。
1. 使用电子邮件地址注册。

发回到应用程序的 ID 令牌会将新扩展属性包含为前面带有 extension_loyaltyId 的自定义声明。 请参阅以下示例：

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>后续步骤

1. 通过更改以下 TechnicalProfiles，将新声明添加到流以登录社交帐户。 社交帐户和联合帐户使用这两个 TechnicalProfiles 登录。 它们使用 alternativeSecurityId 作为用户对象的定位符来读写用户数据。

  ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  ```

2. 在内置策略和自定义策略之间使用相同的扩展属性。 通过门户体验添加扩展属性或自定义属性时，使用每个 B2C 租户中都存在的 b2c-extensions-app 注册这些属性。 按照以下步骤在自定义策略中使用扩展属性：

  a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在 portal.azure.com 的 B2C 租户中，导航到 Azure Active Directory 并选择“应用注册”。  
  b. 找到 b2c-extensions-app，然后选择它。  
  c. 在“概要”下，输入“应用程序 ID”和“对象 ID”。  
  d. 将这些信息包含在 AAD-Common TechnicalProfile 元数据中：  

  ```xml
      <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
                <DisplayName>Azure Active Directory</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <!-- Provide objectId and appId before using extension properties. -->
                <Metadata>
                  <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                  <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
                </Metadata>
  ```

3. 与门户体验保持一致。 在自定义策略中使用这些属性之前，需先使用门户 UI 创建这些属性。 在门户中创建属性 ActivationStatus 时，必须对其进行引用，如下所示：

  ```
  extension_ActivationStatus in the custom policy.
  extension_<app-guid>_ActivationStatus via Graph API.
  ```


## <a name="reference"></a>引用

有关扩展属性的详细信息，请参阅文章[目录架构扩展| 图形 API 概念](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)。

> [!NOTE]
> * TechnicalProfile 是一种元素类型或函数，用于定义终结点的名称、元数据和协议。 TechnicalProfile 详细说明了“标识体验框架”执行的声明交换。 在业务流程步骤中或者通过另一 TechnicalProfile 调用此函数时，调用方以参数形式提供 InputClaims 和 OutputClaims。  
> * 图形 API 中的扩展属性使用约定 `extension_ApplicationObjectID_attributename` 来命名。  
> * 自定义策略将扩展属性称为 extension_attributename。 此引用在 XML 中省略了 ApplicationObjectId。
