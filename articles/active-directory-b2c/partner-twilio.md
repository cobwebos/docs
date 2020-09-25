---
title: Twilio 验证应用程序 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何在 Azure AD B2C 中将示例在线付款应用与 Twilio Verify API 集成。 通过动态链接和强大的客户身份验证，符合 PSD2 (付款服务指令 2) 事务要求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259062"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>将 Twilio 验证应用与 Azure Active Directory B2C 集成

在本演练中，了解如何使用 Twilio Verify API 将 Azure Active Directory B2C (Azure AD B2C) 中的联机支付应用集成。 通过使用 Twilio 验证应用，Azure AD B2C 客户可以通过动态链接和强大的客户身份验证满足 PSD2 (付款服务指令 2) 事务要求。

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 链接到 Azure 订阅的[Azure AD B2C 租户](tutorial-create-tenant.md)。
* Twilio 上的 [试用帐户](https://www.twilio.com/try-twilio) 。

## <a name="scenario-description"></a>方案描述

以下组件组成了 Twilio 解决方案：

- .NET [PSD2 演示 web 应用程序](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)，它提供登录或注册的功能，并执行一个虚拟的高风险事务。
- Azure AD B2C 组合 [登录和注册策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)。
- 与 Twilio 集成的 Azure AD B2C 策略使用验证 API `id_token_hint` 。
- .NET Web 应用，该应用程序承载 `.well-known` OpenIdConnect 终结点以允许验证 `id_token_hint` 。


    ![twilio flow](media/partner-twilio/twilio-flow.png)

| 步骤 | 说明 |
|------|------|
| 1     | 用户启动登录或注册到 PSD2 演示应用。 用户通过 Azure AD B2C 组合登录和注册策略进行身份验证。 令牌将返回到应用程序。 注册时，用户的电话号码使用短信/电话号码进行验证，并记录在其 Azure AD B2C 帐户上。     |
| 2     | 用户启动高风险事务，例如传输 $50.00。 为 PolicyId 评估用户的当前访问令牌，以确定用户是否已通过分步自定义策略进行身份验证。     |
| 3     | 应用程序记录事务值和收款人，$50.00 和 John Doe，并生成已签名的令牌。 此令牌称为 `id_token_hint` ，并包含声明 `amount:$500, payee:john doe` 。 与 `id_token_hint` 请求一起发送到 Azure AD B2C 自定义策略，该策略与 Twilio 集成。     |
| 4     | Azure AD B2C 通过检查应用程序 `/.well-known` OpenId connect 终结点来验证 id_token_hint 的签名。 验证后，它将提取此令牌中的声明，尤其是 `amount` 和 `payee` 。 用户将看到一页通过 SMS 消息验证其移动电话号码。     |
| 5     | 用户请求通过 SMS 消息验证其电话号码，并 Azure AD B2C 向 Twilio 的验证 API 终结点发出 REST API 请求。 它还会发送事务 `amount` ，并将其 `payee` 作为 PSD2 过程的一部分，以生成 (OTP) 的一次性密码。 Twilio 将向用户的已注册电话号码发送一条短信。     |
| 6     |  用户输入在其 SMS 消息中收到的 OTP，并将其提交到 Azure AD B2C。 Azure AD B2C 使用此 OTP 向 Twilio 的验证 API 发出 API 请求，验证 OTP 是否正确。 最后，会向应用程序颁发令牌，其中包含新的 PolicyId，表示用户已进行身份验证。    |
|      |      |

## <a name="onboard-with-twilio"></a>内置 Twilio

1. 在 Twilio 获取 [试用帐户](https://www.twilio.com/try-twilio) 。

2. 按照[此文](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)中所述，在 Twilio 购买电话号码

3. 在 Twilio 控制台导航到 " [验证 API](https://www.twilio.com/console/verify/services) "，并按照 [说明](https://www.twilio.com/docs/verify/verifying-transactions-psd2) 创建服务并启用 PSD2 选项。  

## <a name="configure-the-psd2-demo-app"></a>配置 PSD2 演示应用

1. 打开 WebAPI-DotNet 解决方案，并将以下值替换为你在 web.config 中自己的特定于租户的值：

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. [Web 应用](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)还承载 ID 令牌提示生成器和元数据终结点。
   - 按照此 [示例说明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)中所述创建签名证书。
   - 根据 web.config 中的证书更新以下行：
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. 将演示应用程序上传到所选的托管提供商。  [本示例说明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)中提供了有关 Azure App Service 的指南，其中包括有关上传证书的说明。

4. 通过添加与托管应用程序的 URL 等效的回复 URL 来更新 Azure AD B2C 应用程序注册。

5. 打开 [策略文件](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) ，将的所有实例替换  `contoso` 为你的租户名称。

6. 查找 Twilio REST API 技术配置文件的 **自定义-SMS-注册**。 将  `ServiceURL`   替换为你的 Twilio AccountSID，并将 "从" 数字更新为你购买的电话号码。

7. 查找 Twilio REST API 技术配置文件， **TwilioRestAPI-验证-步骤 1**   和 **TwilioRestAPI-步骤 2**，并将  `ServiceURL`   与 Twilio AccountSID 更新。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

将策略文件添加到 Azure AD B2C：

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。

2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。

4. 导航到 " **Azure AD B2C**  >  **标识体验框架**" "  >  **策略密钥**"。

5. 添加名为 **B2cRestTwilioClientId**的新项。 选择 " **手动**"，并提供 Twilio AccountSID 的值。

6. 添加名为 **B2cRestTwilioClientSecret**的新项。 选择 " **手动**"，并提供 TWILIO 身份验证令牌的值。

7. 将所有策略文件上传到租户。

8. 为注册短信文本自定义 GenerateOTPMessageEnrol 声明转换中的字符串。

## <a name="test-the-solution"></a>测试解决方案

* 浏览到应用程序，并测试登录、注册和发送货币操作。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- 有关[Twilio 集成代码示例](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)，请参阅 GitHub  

- [AAD B2C 中的自定义策略](custom-policy-overview.md)

- [AAD B2C 中的自定义策略入门](custom-policy-get-started.md?tabs=applications)
