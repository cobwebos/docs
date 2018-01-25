---
title: "Azure Active Directory B2C：使用自定义策略动态自定义 Azure AD B2C 用户界面 (UI)"
description: "支持多品牌体验（包含运行时动态更改的 HTML5/CSS 内容）。"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: 3a2310ae6266709df6677c55f11b15239c0425a2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略配置包含动态内容的 UI
使用 Azure Active Directory B2C (Azure AD B2C) 自定义策略可在查询字符串中发送参数。 通过将该参数传递到 HTML 终结点，可以动态更改页面内容。 例如，可以基于从 Web 或移动应用程序传递的参数，更改 Azure AD B2C 注册或登录页面上的背景图像。 

## <a name="prerequisites"></a>先决条件
本文重点介绍如何使用自定义策略来自定义包含动态内容的 Azure AD B2C 用户界面。 若要开始，请参阅[自定义策略中的 UI 自定义](active-directory-b2c-ui-customization-custom.md)。 

>[!NOTE]
>Azure AD B2C 文章[在自定义策略中配置 UI 自定义](active-directory-b2c-ui-customization-custom.md)讲解了以下基础知识：
> * 页面用户界面 (UI) 自定义功能。
> * 用于通过示例内容测试页面 UI 自定义功能的基本工具。
> * 每个页面类型的核心 UI 元素。
> * 有关应用此功能的最佳做法。

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>将 HTML5/CSS 模板的链接添加到用户旅程

在自定义策略中，内容定义定义指定 UI 步骤所用的 HTML5 页面 URI（例如，登录或注册页面）。 基本策略通过指向 CSS 中的 HTML5 文件的 URI 来定义默认外观。 在扩展策略中，可以通过重写该 HTML5 文件的 LoadUri 来修改外观。 内容定义包含通过适当编写 HTML5/CSS 文件所定义的外部内容的 URL。 

`ContentDefinitions` 节包含一系列 `ContentDefinition` XML 元素。 `ContentDefinition` 元素的 ID 属性指定与内容定义相关的页面类型。 也就是说，该元素定义了要在其中应用自定义 HTML5/CSS 模板的上下文。 下表描述了 IEF 引擎识别的内容定义 ID 集及其相关的页面类型。

| 内容定义 ID | 默认 HTML5 模板| 说明 | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **错误页面**。 遇到异常或错误时显示此页面。 |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **标识提供者选择页面**。 此页面列出可供用户在登录期间选择的标识提供者。 选项通常是企业标识提供者、社交标识提供者（例如 Facebook 和 Google+）或本地帐户。 |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **用于注册的标识提供者选项**。 此页面列出可供用户在登录注册选择的标识提供者。 选项为企业标识提供者、社交标识提供者（例如 Facebook 和 Google+）或本地帐户。 |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **忘记密码页面**。 此页面包含一个窗体，用户必须填写该窗体才能发起密码重置。  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本地帐户登录页面**。 此页面包含一个窗体，用于通过基于电子邮件地址或用户名的本地帐户进行登录。 该窗体可以包含文本输入框和密码输入框。 |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本地帐户注册页面**。 此页面包含一个窗体，用于通过基于电子邮件地址或用户名的本地帐户进行注册。 该窗体可以包含各种输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。 |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多重身份验证页面** 在此页面上，用户可以在注册或登录期间（使用文字或语音）验证其电话号码。 |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **社交帐户注册页面**。 此页面包含一个窗体，用户在使用社交标识提供者的现有帐户注册时必须填写此窗体。 除了密码输入字段之外，此页面类似于前面的社交帐户注册页面。 |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **个人资料更新页面**。 此页面包含用户在更新其个人资料时可以访问的窗体。 除了密码输入字段之外，此页面类似于社交帐户注册页面。 |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **统一注册或登录页面**。 此页面处理用户注册和登录过程。 用户可以使用企业标识提供者、社交标识提供者（例如 Facebook 或 Google+）或本地帐户。  |

## <a name="serving-dynamic-content"></a>提供动态内容
在[在自定义策略中配置 UI 自定义](active-directory-b2c-ui-customization-custom.md)一文中，已将 HTML5 文件上传到 Azure Blob 存储。 这些 HTML5 文件是静态的，为每个请求呈现相同的 HTML 内容。 

本文使用 ASP.NET Web 应用，该应用可接受查询字符串参数并相应地做出响应。 

本演练中的操作：
* 创建用于托管 HTML5 模板的 ASP.NET Core Web 应用程序。 
* 添加自定义 HTML5 模板 _unified.cshtml_。 
* 将 Web 应用发布到 Azure 应用服务。 
* 为 Web 应用设置跨源资源共享 (CORS)。
* 重写 `LoadUri` 元素，以指向 HTML5 文件。

## <a name="step-1-create-an-aspnet-web-app"></a>步骤 1：创建 ASP.NET Web 应用

1. 在 Visual Studio 中，选择“文件” > “新建” > “项目”来创建项目。

2. 在“新建项目”窗口中，选择“Visual C#” > “Web” > “ASP.NET Core Web 应用程序(.NET Core)”。

3. 为该应用程序命名（例如 *Contoso.AADB2C.UI*），选择“确定”。

    ![创建新的 Visual Studio 项目](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. 选择“Web 应用程序”模板。

5. 将身份验证设置为“无身份验证”。

    ![选择“Web 应用程序”模板](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. 选择“确定”创建该项目。

## <a name="step-2-create-mvc-view"></a>步骤 2：创建 MVC 视图
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>步骤 2.1：下载 B2C 内置 HTML5 模板
自定义 HTML5 模板基于 Azure AD B2C 的内置 HTML5 模板。 可以下载 [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml)，或者从[初学者包](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip)下载模板。 将使用此 HTML5 文件来创建统一的注册或登录页面。

### <a name="step-22-add-the-mvc-view"></a>步骤 2.2：添加 MVC 视图
1. 右键单击“Views/Home”文件夹，选择“添加” > “新项”。

    ![添加 MVC 新项](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. 在“添加新项 - Contoso.AADB2C.UI”窗口中，选择“Web”>“ASP.NET”。

3. 选择“MVC 视图页面”。

4. 在“名称”框中，将名称更改为 **unified.cshtml**。

5. 选择“添加”。

    ![添加 MVC 视图](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. 如果 *unified.cshtml* 文件尚未打开，请双击该文件将其打开，然后清除文件内容。

7. 在本演练中，我们删除了对布局页面的引用。 在 _unified.cshtml_ 中添加以下代码片段：

    ```csharp
    @{
        Layout = null;
    }
    ```

8. 打开从 Azure AD B2C 内置 HTML5 模板下载的 _unified.cshtml_ 文件。

9. 将单 @ 符号替换为双 @ 符号 (@@)。

10. 复制该文件的内容，并将其粘贴到 Layout 定义的下面。 代码应如下所示：

    ![添加 HTML5 之后的 unified.cshtml 文件](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>步骤 2.3：更改背景图像

找到包含 `ID` 值 *background_background_image* 的 `<img>` 元素，将 `src` 替换为 **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** 或想要使用的其他任何背景图像。

![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>步骤 2.4：将视图添加到 MVC 控制器

1. 打开 **Controllers\HomeController.cs** 并添加以下方法： 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    此代码指定该方法应使用“视图”模板文件在浏览器中呈现响应。 由于我们未显式指定“视图”模板文件的名称，MVC 已默认使用 */Views/Home* 文件夹中的 _unified.cshtml_ 视图文件。
    
    添加 _unified_ 方法后，代码应如下所示：
    
    ![更改控制器以呈现视图](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. 调试 Web 应用，确保可访问 _unified_ 页面（例如 `http://localhost:<Port number>/Home/unified`）。

### <a name="step-25-publish-to-azure"></a>步骤 2.5：发布到 Azure
1. 在“解决方案资源管理器”中右键单击“Contoso.AADB2C.UI”项目，选择“发布”。

    ![发布到 Microsoft Azure 应用服务](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. 选择“Microsoft Azure 应用服务”磁贴，再选择选择“发布”。

    ![创建新的 Microsoft Azure 应用服务](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    此时会打开“创建应用服务”窗口。 在此窗口中，可以开始创建在 Azure 中运行 ASP.NET Web 应用所需的全部 Azure 资源。

    > [!NOTE]
    > 有关发布方法的详细信息，请参阅[在 Azure 中创建 ASP.NET Web 应用](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)。

3. 在“Web 应用名称”框中，键入唯一的应用名称（有效字符为 a-z、A-Z、0-9 和连字符 (-)）。 Web 应用的 URL 为 `http://<app_name>.azurewebsites.NET`，其中 `<app_name>` 是 Web 应用名称。 可以接受自动生成的名称，它是唯一的。

4. 选择“创建”开始创建 Azure 资源。

    ![提供应用服务属性](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    完成创建过程后，向导会将 ASP.NET Web 应用发布到 Azure，然后在默认浏览器中启动该应用。

5. 复制 _unified_ 页面的 URL（例如 _https://<app_name>.azurewebsites.net/home/unified_）。

## <a name="step-3-configure-cors-in-azure-app-service"></a>步骤 3：在 Azure 应用服务中配置 CORS
1. 在 [Azure 门户](https://portal.azure.com/)中选择“应用服务”，再选择 API 应用的名称。

    ![在 Azure 门户中选择 API 应用](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. 在“设置”部分中的“API”部分下，选择“CORS”。

    ![选择 CORS 设置](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. 在“CORS”窗口中的“允许的来源”框内，执行以下操作之一：

    * 输入要允许的一个或多个 JavaScript 调用来源 URL。
    * 输入星号 ( * ) 表示接受所有来源域。

4. 选择“保存”。

    ![“CORS”窗口](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    选择“保存”后，API 应用会接受来自指定 URL 的 JavaScript 调用。 

## <a name="step-4-html5-template-validation"></a>步骤 4：HTML5 模板验证
HTML5 模板现在可供使用。 但是，无法在 `ContentDefinition` 代码中使用它。 在将 `ContentDefinition` 添加到自定义策略之前，请确保：
* 内容符合 HTML5 规范且可访问。
* 为 CORS 启用了内容服务器。

    >[!NOTE]
    >若要验证托管内容的站点是否已启用 CORS 并可测试 CORS 请求，请转到 [test-cors.org](http://test-cors.org/) 网站。 

* 提供的内容通过 **HTTPS** 安全传输。
* 为所有链接、CSS 内容和图像使用绝对 URL，例如 *https://yourdomain/content*。

## <a name="step-5-configure-your-content-definition"></a>步骤 5：配置内容定义
若要配置 `ContentDefinition`，请执行以下操作：
1. 打开策略的基文件（例如 *TrustFrameworkBase.xml*）。

2. 搜索 `<ContentDefinitions>` 元素，并复制 `<ContentDefinitions>` 节点的整个内容。

3. 打开扩展文件（例如 *TrustFrameworkExtensions.xml*），并搜索 `<BuildingBlocks>` 元素。 如果该元素不存在，请添加该元素。

4. 将复制的整个 `<ContentDefinitions>` 节点的内容粘贴为 `<BuildingBlocks>` 元素的子级。 

5. 在复制的 XML 中搜索包含 `Id="api.signuporsignin"` 的 `<ContentDefinition>` 节点。

6. 将 `LoadUri` 的值从 _~/tenant/default/unified_ 更改为 _https://<app_name>.azurewebsites.net/home/unified_。  
    自定义策略应如下所示：
    
    ![内容定义](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>步骤 6：将策略上传到租户
1. 在 [Azure 门户](https://portal.azure.com)中，切换到[你的 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后选择“Azure AD B2C”。

2. 选择“标识体验框架”。

3. 选择“所有策略”。

4. 选择“上传策略”。

5. 选中“覆盖策略(如果存在)”复选框。

6. 上传 *TrustFrameworkExtensions.xml* 文件，并确保它能够通过验证。

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>步骤 7：使用“立即运行”测试自定义策略
1. 选择“Azure AD B2C 设置”，然后选择“标识体验框架”。

    >[!NOTE]
    >“立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2. 打开已上传的信赖方 (RP) 自定义策略 **B2C_1A_signup_signin**，然后选择“立即运行”。  
    应会看到带有前面所创建的背景的自定义 HTML5。

    ![注册或登录策略](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>步骤 8：添加动态内容
基于名为 _campaignId_ 的查询字符串参数更改背景。 RP 应用程序（Web 和移动应用）将参数发送到 Azure AD B2C。 策略读取参数，并将其值发送到 HTML5 模板。 

### <a name="step-81-add-a-content-definition-parameter"></a>步骤 8.1：添加内容定义参数

执行以下操作来添加 `ContentDefinitionParameters` 元素：
1. 打开策略的 *SignUpOrSignin* 文件（例如 *SignUpOrSignin.xml*）。

2. 搜索 `<DefaultUserJourney>` 节点。 

3. 在 `<DefaultUserJourney>` 节点中添加以下 XML 片段：  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>步骤 8.2：更改代码以接受查询字符串参数并替换背景图像
修改 HomeController `unified` 方法以接受 campaignId 参数。 然后，该方法检查该参数的值，并相应地设置 `ViewData["background"]` 变量。

1. 打开 *Controllers\HomeController.cs* 文件，添加以下代码片段以更改 `unified` 方法：

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. 找到具有 ID `background_background_image` 的 `<img>` 元素，并将 `src` 值替换为 `@ViewData["background"]`。

    ![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>步骤 8.3：上传更改并发布策略
1. 将 Visual Studio 项目发布到 Azure 应用服务。

2. 将 *SignUpOrSignin.xml* 策略上传到 Azure AD B2C。

3. 打开上传的 RP 自定义策略 **B2C_1A_signup_signin**，选择“立即运行”。  
    应会看到前面所显示的相同背景图像。

4. 复制浏览器地址栏中的 URL。

5. 将 _campaignId_ 查询字符串参数添加到 URI。 例如，如下图所示添加 `&campaignId=hawaii`：

    ![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. 按 **Enter** 显示 Hawaii 背景图像。

    ![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. 将值更改为 *Tokyo* 并按 **Enter**。  
    浏览器会显示 Tokyo 背景。

    ![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>步骤 9：更改剩余的用户旅程
如果在登录页面上选择“立即注册”链接，浏览器会显示默认背景，而不是定义的背景。 之所以出现此行为，是因为前面仅更改了注册或登录页面。 若要更改剩余的 Self-Assert 内容定义，请执行以下操作：
1. 返回“步骤 2”，执行以下操作：

    a. 下载 *selfasserted* 文件。

    b. 复制文件内容。

    c. 创建新视图 *selfasserted*。

    d.单击“下一步”。 将 *selfasserted* 添加到 **Home** 控制器。

2. 返回“步骤 4”，执行以下操作： 

    a. 在扩展策略中，找到包含 `Id="api.selfasserted"`、`Id="api.localaccountsignup"` 和 `Id="api.localaccountpasswordreset"` 的 `<ContentDefinition>` 节点。

    b. 将 `LoadUri` 属性设置为 *selfasserted* URI。

3. 返回“步骤 8.2”，更改代码以接受查询字符串参数，但这次请使用 *selfasserted* 函数。 

4. 上传 *TrustFrameworkExtensions.xml* 策略，并确保它能够通过验证。

5. 运行策略测试，然后选择“立即注册”查看结果。

## <a name="optional-download-the-complete-policy-files-and-code"></a>（可选）下载完整的策略文件和代码
* 完成[自定义策略入门](active-directory-b2c-get-started-custom.md)演练后，我们建议你使用自己的自定义策略文件来构建方案。 我们已提供[示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)用于参考。
* 可以从 [Visual Studio 解决方案参考示例](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)下载完整代码。




