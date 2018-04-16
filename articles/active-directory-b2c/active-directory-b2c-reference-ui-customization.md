---
title: 用户界面 (UI) 自定义 - Azure AD B2C | Microsoft Docs
description: 有关 Azure Active Directory B2C 中用户界面 (UI) 自定义功能的主题
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: ea33fa95aa7b73668aeee313a9d71a7f78e89a77
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C：自定义 Azure AD B2C 用户界面 (UI)

用户体验在面向客户的应用程序中至关重要。  通过提升品牌观感的用户体验，可扩大客户群。 通过 Azure Active Directory B2C (Azure AD B2C)，可以使用完美的像素控制自定义注册、登录、配置文件编辑和密码重置页面。

> [!NOTE]
> 本文所述的页面 UI 自定义功能不适用于仅登录策略、其随附的密码重置页面和验证电子邮件。  这些功能使用[公司品牌功能](../active-directory/customize-branding.md)。
>
> 同样，如果用户在登录之前启动编辑配置文件策略，则用户将被重定向到可使用[公司品牌功能](../active-directory/customize-branding.md)进行自定义的页。

本文涵盖以下主题：

* 页面 UI 自定义功能。
* 用于将 HTML 内容上传到 Azure Blob 存储，以便用于页面 UI 自定义功能的工具。
* Azure AD B2C 使用的 UI 元素，可使用级联样式表 (CSS) 自定义该元素。
* 执行此功能时的最佳做法。

## <a name="the-page-ui-customization-feature"></a>页面 UI 自定义功能

可自定义客户注册、登录（有关与品牌相关的例外，请参阅上面的说明）、密码重置和配置文件编辑页面的外观和风格（通过配置[策略](active-directory-b2c-reference-policies.md)）。 在应用程序和 Azure AD B2C 提供的页面之间导航时，客户会获得无缝体验。

不同于使用 UI 选项的其他服务，Azure AD B2C 使用简单的新式方法自定义 UI。

工作原理如下：Azure AD B2C 在客户的浏览器中运行代码，并使用称为[跨域资源共享 (CORS)](http://www.w3.org/TR/cors/) 的现代方法。  在运行时，从在策略中指定的 URL 加载内容。 可以为不同的页面指定不同的 URL。 从 URL 加载的内容与从 Azure AD B2C 插入的 HTML 片段合并后，会向客户显示页面。 只需要：

1. 使用位于 `<body>` 中某处的空 `<div id="api"></div>` 元素创建格式正确的 HTML5 内容。 此元素标记插入 Azure AD B2C 内容的位置。
1. 在 HTTPS 终结点上托管内容（允许 CORS）。 请注意，配置 CORS 时必须启用 GET 和 OPTIONS 请求方法。
1. 使用 CSS 为 Azure AD B2C 插入的 UI 元素设置样式。

### <a name="a-basic-example-of-customized-html"></a>自定义的 HTML 的一个基本示例

以下示例是可以用来测试此功能的最基本 HTML 内容。 使用[帮助程序工具](active-directory-b2c-reference-ui-customization-helper-tool.md)在 Azure Blob 存储中上传和配置此内容。 然后，可以验证每个页面上的无样式基本按钮和表单域是否显示且运行正常。

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>测试 UI 自定义功能

想要使用我们的示例 HTML 和 CSS 内容来尝试 UI 自定义功能？  我们提供了[帮助程序工具](active-directory-b2c-reference-ui-customization-helper-tool.md)，它可用于在 Azure Blob 存储中上传和配置示例内容。

> [!NOTE]
> 可以在任何位置托管 UI 内容：Web 服务器、CDN、AWS S3 和文件共享系统等。只需内容托管在启用了 CORS 的公开可用的 HTTPS 终结点上即可。 使用 Azure Blob 存储只是为了用于说明。
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>Azure AD B2C 嵌入的 UI 片段

以下部分列出了 Azure AD B2C 合并到内容中 `<div id="api"></div>` 元素的 HTML5 片段。 **请勿在 HTML 5 内容中插入这些片段。** Azure AD B2C 服务会在运行时插入它们。 设计自己的级联样式表 (CSS) 时，请使用这些片段作为参考。

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>插入到“标识提供者选择页”的片段

此页面包含有可供用户在注册或登录期间进行选择的标识提供者列表。 这些按钮包括社交标识提供者（如 Facebook 和 Google+）或本地帐户（基于电子邮件地址或用户名）。

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>插入到“本地帐户注册页”的片段

此页面包含一个窗体，用于基于电子邮件地址或用户名的本地帐户注册。 该窗体可以包含不同的输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>插入到“社交帐户注册页”的片段

使用社交标识提供者（如 Facebook 或 Google+）的现有帐户进行注册时，可能会显示此页面。  在必须使用注册窗体收集最终用户的其他信息时使用此页面。 除密码输入字段外，此页面类似于本地帐户注册页面（如上一部分所示）。

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>插入到“统一注册或登录页”的片段

此页面处理客户的注册和登录，这些客户可以使用社交标识提供者（Facebook 或 Google+）的帐户或本地帐户。

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>插入到“多重身份验证页”的片段

在此页面上，用户可以在注册或登录期间（使用文字或语音）验证其电话号码。

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>插入到“错误页”的片段

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>本地化 HTML 内容

可通过启用[语言自定义](active-directory-b2c-reference-language-customization.md)来本地化 HTML 内容。  启用此功能可让 Azure AD B2C 将 OpenID 连接参数 `ui-locales` 转发到终结点。  内容服务器可使用此参数提供特定语言的自定义 HTML 页面。

## <a name="things-to-remember-when-building-your-own-content"></a>构建自己的内容时要注意的事项

如果打算使用页面 UI 自定义功能，请查看以下最佳做法：

* 请勿复制 Azure AD B2C 的默认内容并尝试修改。 最好从头开始构建 HTML5 内容，将默认内容用作参考。
* 出于安全考虑，我们不允许在自己的内容中包含任何 JavaScript。 所需的大部分功能应该都是现成的。 如果不是，请使用[User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)（用户之声）请求新功能。
* 支持的浏览器版本：
  * Internet Explorer 11、10、Microsoft Edge
  * 对 Internet Explorer 9 和 8 的支持有限
  * Google Chrome 42.0 和更高版本
  * Mozilla Firefox 38.0 和更高版本
