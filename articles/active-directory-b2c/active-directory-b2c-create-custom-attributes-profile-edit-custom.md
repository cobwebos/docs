---
title: 向 Azure Active Directory B2C 的自定义策略添加你自己的属性 | Microsoft Docs
description: 演示了如何使用扩展属性、自定义属性以及将其包含在用户界面中。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ecde4d8cd8ee454290b16b640ba05d310cf348fe
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448926"
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C：在自定义配置文件编辑策略中创建和使用自定义属性

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在本文中，会在 Azure AD B2C 目录中创建一个自定义属性，然后将此新属性用作配置文件编辑用户旅程中的自定义声明。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>使用自定义属性来收集有关使用自定义策略的 Azure Active Directory B2C 中的使用者的信息
Azure Active Directory (Azure AD) B2C 目录附带了一组内置属性：名字、姓氏、城市、邮政编码、userPrincipalName，等等。通常需要创建自己的属性。  例如：
* 面向客户的应用程序需要保留“LoyaltyNumber”等属性。
* 标识提供者具有必须保存的唯一用户标识符，例如“uniqueUserGUID”。
* 自定义用户旅程需要保留用户的状态，例如“migrationStatus”。

在 Azure AD B2C 中，可以扩展存储在每个用户帐户中的属性集。 还可以使用 [Azure AD 图形 API](active-directory-b2c-devquickstarts-graph-dotnet.md) 读取和写入这些属性。

扩展属性扩展目录中用户对象的架构。  术语扩展属性、自定义特性和自定义声明在本文的上下文中引用相同的内容，并且名称会因上下文（应用程序、对象、策略）而异。

尽管扩展属性可以包含用户的数据，但它们只能在应用程序对象中注册。 属性将附加到应用程序。 必须向应用程序对象授予写入访问权限，使其能够注册扩展属性。 可将 100 个扩展属性（针对所有类型和所有应用程序）写入任何一个对象。 扩展属性将添加到目标目录类型，在 Azure AD B2C 目录租户中立即可供访问。
如果删除了该应用程序，则也会删除这些扩展属性及其为所有用户包含的所有数据。 如果应用程序删除了某个扩展属性，该属性将从目标目录对象中移除，同时会删除值。

扩展属性只在租户中已注册的应用程序的上下文中存在。 必须在使用该应用程序的 TechnicalProfile 中包含该应用程序的对象 ID。

>[!NOTE]
>Azure AD B2C 目录通常包含名为 `b2c-extensions-app` 的 Web 应用。  此应用程序主要由通过 Azure 门户创建的自定义声明的 b2c 内置策略使用。  仅建议高级用户使用此应用程序来注册 b2c 自定义策略的扩展。  本文的“后续步骤”部分中提供了相关说明。


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>创建用于存储扩展属性的新应用程序

1. 打开浏览会话，转到 [Azure 门户](https://portal.azure.com)，并使用要配置的 B2C 目录的管理凭据进行登录。
2. 在左侧导航菜单中，单击“Azure Active Directory”。 可能需要选择“更多服务>”才能找到该选项。
3. 选择“应用注册”并单击“新建应用程序注册”
4. 提供以下建议条目：
    * 指定 Web 应用程序的名称：WebApp-GraphAPI-DirectoryExtensions
    * 应用程序类型：Web 应用/API
    * 登录 URL：https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
5. 选择**创建**。
6. 选择新建的 Web 应用程序。
7. 选择“设置” > “所需权限”。
8. 选择 API“Windows Azure Active Directory”。
9. 勾选应用程序权限：“读取和写入目录数据”，然后选择“保存”。
10. 选择“授予权限”，然后单击“是”确认。
11. 复制到剪贴板并保存以下标识符：
    * **应用程序 ID**。 示例：`103ee0e6-f92d-4183-b576-8c3739027780`
    * **对象 ID**。 示例：`80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>修改自定义策略以添加 ApplicationObjectId

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤后，你已下载并修改了名为 *TrustFrameworkBase.xml*、*TrustFrameworkExtensions.xml*、*SignUpOrSignin.xml*、*ProfileEdit.xml* 和 *PasswordReset.xml* 的[文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)。 在以下步骤中，将继续对这些文件进行修改。

1. 打开 *TrustFrameworkBase.xml* 文件，并添加 `Metadata` 节，如以下示例所示。 插入先前为 `ApplicationObjectId` 值记录的对象 ID 以及为 `ClientId` 值记录的应用程序 ID： 

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

>[!NOTE]
>当技术配置文件在新建的扩展属性中首次写入时，可能会遇到一次性错误。 首次使用时将创建扩展属性。  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>在用户旅程中使用新的扩展属性/自定义属性

1. 打开 *ProfileEdit.xml* 文件。
2. 添加自定义声明 `loyaltyId`。  通过在 `<RelyingParty>` 元素中包含自定义声明，它将包含在应用程序的令牌中。
    
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

3. 打开 *TrustFrameworkExtensions.xml* 文件，将 `<ClaimsSchema>` 元素及其子元素添加到 `BuildingBlocks` 元素：

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

4. 将相同的 `ClaimType` 定义添加到 *TrustFrameworkBase.xml*。 通常同时在基本文件和扩展文件中添加 `ClaimType` 定义并不是必要的，但由于后续步骤要将 `extension_loyaltyId` 添加到基本文件中的 TechnicalProfiles，如果未提供该项，策略验证程序将拒绝上传基本文件。 在 TrustFrameworkBase.xml 文件中跟踪名为“ProfileEdit”的用户旅程的执行可能会有所帮助。  在编辑器中搜索同名的用户旅程，并观察业务流程步骤 5 是否调用 TechnicalProfileReferenceID ="SelfAsserted ProfileUpdate"。  请搜索并检查此技术配置文件，以熟悉相关的流。

5. 打开 *TrustFrameworkBase.xml* 文件，并在 TechnicalProfile“SelfAsserted-ProfileUpdate”中添加 `loyaltyId` 作为输入和输出声明：

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

6. 在 *TrustFrameworkBase.xml* 文件中，向 TechnicalProfile“AAD-UserWriteProfileUsingObjectId”中添加 `loyaltyId` 声明，以保留目录中当前用户的扩展属性中的声明值：

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

7. 在 *TrustFrameworkBase.xml* 文件中，向 TechnicalProfile“AAD-UserReadUsingObjectId”添加 `loyaltyId` 声明，以便每次用户登录时都会读取扩展属性的值。 到目前为止，技术配置文件仅在本地帐户流中发生过更改。  如果社交/联合帐户流中需要新属性，则需要更改一组不同的技术配置文件。 请参阅后续步骤。

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

1. 打开“Azure AD B2C 边栏选项卡”并导航到“标识体验框架”>“自定义策略”。
1. 选择上传的自定义策略，并单击“立即运行”按钮。
1. 现在，应该可以使用电子邮件地址注册。

发回到应用程序的 ID 令牌会将新扩展属性包含为前面带有 extension_loyaltyId 的自定义声明。 请参阅示例。

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

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>通过更改下面列出的技术配置文件，将新的声明添加到社交帐户登录流。 社交/联合帐户登录使用这两个技术配置文件来写入和读取将 alternativeSecurityId 用作用户对象定位符的数据。
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

在内置策略和自定义策略之间使用相同的扩展属性。
通过门户体验添加扩展属性（也称为自定义属性）时，使用每个 b2c 租户中都存在的 **b2c-extensions-app 注册这些属性。  若要在自定义策略中使用这些扩展属性，请执行以下操作：
1. 在 portal.azure.com 的 b2c 租户中，导航到 **Azure Active Directory**，选择“应用注册”
2. 找到 **b2c-extensions-app**，然后选择它
3. 在“概要”下记录**应用程序 ID**和**对象 ID**
4. 将这些信息包含在 AAD-Common 技术配置文件元数据中，如下所示：

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

若要保持与门户体验的一致性，在自定义策略中使用这些属性*之前*，需先使用门户 UI 创建这些属性。  在门户中创建属性“ActivationStatus”时，必须引用它，如下所示：

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>引用

* **技术配置文件 (TP)** 是一个可被视为*函数*的元素类型，定义终结点的名称及其元数据和协议，以及标识体验框架应执行的声明交换的详细信息。  在业务流程步骤中或者通过另一技术配置文件调用此*函数*时，调用方以参数形式提供 InputClaims 和 OutputClaims。


* 有关扩展属性的完整处理方法，请参阅[目录架构扩展 | 图形 API 的概念](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)一文

>[!NOTE]
>图形 API 中的扩展属性使用约定 `extension_ApplicationObjectID_attributename` 来命名。 自定义策略将扩展属性称为 extension_attributename，因此会在 XML 中省略 ApplicationObjectId
