---
title: "Azure Active Directory B2C：使用自定义策略动态自定义 Azure AD B2C 用户界面 (UI)"
description: "如何支持多品牌体验（包含运行时动态更改的 HTML/CSS 内容）"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略配置包含动态内容的 UI
使用 Azure AD B2C 自定义策略可在查询字符串中传递参数。 该参数会传递到 HTML 终结点，并可动态更改页面内容。 例如，可以基于从 Web/移动应用程序传递的参数，更改 B2C 注册或登录背景图像。 

## <a name="prerequisites"></a>先决条件
本文重点介绍如何使用自定义策略来自定义包含**动态内容**的 Azure AD B2C 用户界面。 若要开始体验自定义策略 UI 自定义，请阅读[自定义策略中的 UI 自定义](active-directory-b2c-ui-customization-custom.md)一文。 

>[!NOTE]
>
>  [在自定义策略中配置 UI 自定义](active-directory-b2c-ui-customization-custom.md)一文讲解了有关使用自定义策略实现 Azure AD B2C UI 自定义的基础知识：
> * 页面用户界面 (UI) 自定义功能。
> * 一种有助于使用示例内容测试页面 UI 自定义功能的工具。
> * 每种类型页面中的核心 UI 元素。
> * 执行此功能时的最佳做法。

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>将 HTML5/CSS 模板的链接添加到用户旅程

在自定义策略中，内容定义定义用于给定 UI 步骤的 HTML5 页面 URI。 例如，登录或注册页面。 基本策略通过指向 HTML5 文件的 URI（引用其 CSS）来定义默认外观。 在扩展策略中，可以通过重写该 HTML5 文件的 LoadUri 来修改外观。 因此，内容定义包含通过适当编写 HTML5/CSS 文件所定义的外部内容的 URL。 

`ContentDefinitions` 节包含一系列 `ContentDefinition` XML 元素。 `ContentDefinition` 元素的 ID 属性指定与内容定义相关的页面类型。 因此，也指定了要在其中使用自定义 HTML5/CSS 模板的上下文。 下表描述了 IEF 引擎识别的内容定义 ID 集及其相关的页面类型。

| 内容定义 ID | 默认 HTML5 模板| 说明 | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **错误页面**。 遇到异常或错误时显示此页面。 |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **标识提供者选择页面**。 此页面包含有可供用户在登录期间选择的标识提供者列表。 这些选项通常是企业标识提供者、社交标识提供者（例如 Facebook 和 Google+）或本地帐户。 |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **用于注册的标识提供者选项**。 此页面包含有可供用户在注册期间选择的标识提供者列表。 这些选项是企业标识提供者、社交标识提供者（例如 Facebook 和 Google+）或本地帐户。 |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **忘记密码页面**。 此页面包含一个窗体，用户必须填写该窗体才能发起密码重置。  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本地帐户登录页面**。 此页面包含一个登录窗体，用于通过基于电子邮件地址或用户名的本地帐户进行登录。 该窗体可以包含文本输入框和密码输入框。 |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本地帐户注册页面**。 此页面包含一个注册窗体，用于通过基于电子邮件地址或用户名的本地帐户进行注册。 该窗体可以包含各种输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。 |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多重身份验证页面** 在此页面上，用户可以在注册或登录期间（使用文字或语音）验证其电话号码。 |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **社交帐户注册页面**。 此页面包含一个注册窗体，用户在使用社交标识提供者的现有帐户注册时必须填写此窗体。 除了密码输入字段之外，此页面类似于前面的社交帐户注册页面。 |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **个人资料更新页面**。 此页面包含用户可以用来更新其个人资料的窗体。 除了密码输入字段之外，此页面类似于社交帐户注册页面。 |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **统一注册或登录页面**。 此页面处理用户的注册和登录。 用户可以使用企业标识提供者、社交标识提供者（例如 Facebook 或 Google+）或本地帐户。  |

## <a name="serving-dynamic-content"></a>提供动态内容
在上一文章[在自定义策略中配置 UI 自定义](active-directory-b2c-ui-customization-custom.md)中，已将 HTML5 文件上传到 Azure Blob 存储。 这些 HTML5 文件是静态的，为每个请求呈现相同的 HTML 内容。 本文使用 ASP.Net Web 应用，该应用可接受查询字符串参数并相应地做出响应。 本演练中的操作：
* 创建用于托管 HTML5 模板的 ASP.NET Core Web 应用程序 
* 添加自定义 HTML5 模板 _unified.cshtml_ 
* 将 Web 应用发布到 Azure 服务应用 
* 为 Web 应用设置 CORS
* 重写 `LoadUri` 元素，以指向 HTML5 文件

## <a name="step-1-create-an-aspnet-web-app"></a>步骤 1：创建 ASP.NET Web 应用

1.  在 Visual Studio 中，通过依次选择“文件”>“新建”>“项目”创建项目。
2.  在“新建项目”对话框中，选择“Visual C#”>“Web”>“ASP.NET Core Web 应用程序(.NET Core)”。
3.  为该应用程序命名（例如 Contoso.AADB2C.UI），选择“确定”。

    ![创建新的 Visual Studio 项目](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  选择“Web 应用程序”模板
5.  确保身份验证设置为“无身份验证”

    ![选择“Web API”模板](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  单击“确定”创建项目

## <a name="step-2-create-mvc-view"></a>步骤 2：创建 MVC 视图
### <a name="step-21-download-b2c-built-in-html5-template"></a>步骤 2.1：下载 B2C 内置 HTML5 模板
自定义 HTML5 已基于 B2C 的内置 HTML5 模板进行模板化。 可以下载 [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml)，或者从[初学者包](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip)下载。 将为统一的注册或登录页面使用此 HTML5 文件。

### <a name="step-22-add-mvc-view"></a>步骤 2.2：添加 MVC 视图
1. 右键单击“Views/Home”文件夹，选择“添加”>“新项”。
 ![添加 MVC 新项](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. 在“添加新项 - Contoso.AADB2C.UI”对话框中，选择“Web”>“ASP”>“NET”
3. 点击“MVC 视图页面”
4. 在“名称”框中，将名称更改为 _unified.cshtml_。
5. 单击“添加”
 “添加 MVC 视图”![](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)
6.  如果此文件尚未打开，请双击文件将其打开。 清除 _unified.cshtml_ 的内容。
7. 出于演示，我们删除了对布局页面的引用。 在 _unified.cshtml_ 中添加以下代码片段

    ```C#
    @{
        Layout = null;
    }
    ```

8. 打开从 AAD B2C 内置 HTML5 模板下载的 _unified.cshtml_ 文件。
9. 找到 `@` 符号，将其替换为 `@@`
10. 复制该文件的内容，并将其粘贴到 Layout 定义的下面。 代码应如下所示：![添加 HTML5 之后的 unified.cshtml 文件](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)
### <a name="step-23-change-the-background-image"></a>步骤 2.3：更改背景图像
10. 找到具有 ID `background_background_image` 的 `<img>` 元素，将 `src` 替换为 _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_ 或所需的其他任何背景图像。
![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>步骤 2.4：将视图添加到 MVC 控制器
打开 **Controllers\HomeController.cs** 并添加以下方法。 
```C
public IActionResult unified()
{
    return View();
}
```
此代码指定该方法应使用视图模板文件在浏览器中呈现响应。 由于我们未显式指定视图模板文件的名称，MVC 已默认使用 /Views/Home 文件夹中的 _unified.cshtml_  视图文件。

添加 _unified_ 方法后，代码应如下所示：![更改控制器以呈现视图](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

调试 Web 应用，确保可访问 _unified_ 页面。 例如： `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>步骤 2.5：发布到 Azure
1.  在“解决方案资源管理器”中右键单击“Contoso.AADB2C.UI”项目，选择“发布”。

    ![发布到 Microsoft Azure 应用服务](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  确保已选择“Microsoft Azure 应用服务”，然后选择“发布”。

    ![创建新的 Microsoft Azure 应用服务](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    借助“创建应用服务”对话框，创建在 Azure 中运行 ASP.NET Web 应用所需的所有 Azure 资源。

> [!NOTE]
>
>有关发布方法的详细信息，请参阅：[在 Azure 中创建 ASP.NET Web 应用](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)。

3.  在“Web 应用名称”中，键入唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。 Web 应用的 URL 为 `http://<app_name>.azurewebsites.NET`，其中 `<app_name>` 是 Web 应用名称。 可以接受自动生成的名称，它是唯一的。

    选择“创建”开始创建 Azure 资源。

    ![提供应用服务属性](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  向导完成后，它会将 ASP.NET Web 应用发布到 Azure，然后在默认浏览器中启动该应用，并复制 _unified_ 页面的 URL。 例如 _https://<app_name>.azurewebsites.net/home/unified_

## <a name="step-3-configure-cors-in-azure-app-service"></a>步骤 3：在 Azure 应用服务中配置 CORS
1. 在浏览器中，导航到 [Azure 门户](https://portal.azure.com/)。

2. 单击“应用服务”，并单击 API 应用的名称。

    ![在门户中选择 API 应用](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. 在“设置”部分中，向下滚动找到“API”部分，单击“CORS”。

    ![选择 CORS 设置](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. 在文本框中，输入要允许的一个或多个 JavaScript 调用来源 URL。
或者输入星号 (*) 指定接受所有来源域。

5. 单击“保存” 。

    ![点击“保存”(Save)](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

单击“保存”后，API 应用会接受来自指定 URL 的 JavaScript 调用。 

## <a name="step-4-html5-template-validation"></a>步骤 4：HTML5 模板验证
已模板化的 HTML5 现在可供使用。  但是，无法在任何 `ContentDefinition` 中使用它。 在将 `ContentDefinition` 添加到自定义策略之前，必须：
* 确保内容符合 HTML5 规范且可访问
* 确保为 CORS 启用了内容服务器。

>[!NOTE]
>
>若要验证托管内容的站点是否已启用 CORS 并测试 CORS 请求，可以使用站点 http://test-cors.org/。 

* 提供的内容通过 **HTTPS** 安全传输。
* 为所有链接、CSS 内容和图像使用**绝对 URL**，例如 https://yourdomain/content。

## <a name="step-5-configure-your-content-definition"></a>步骤 5：配置内容定义
配置 `ContentDefinition`
1.  打开策略的基文件（例如 TrustFrameworkBase.xml）。
2.  找到 `<ContentDefinitions>` 元素并复制整个 `<ContentDefinitions>` 节点的内容。
3.  打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<BuildingBlocks>` 元素。 如果该元素不存在，请添加一个。
4.  将复制的整个 `<ContentDefinitions>` 节点的内容粘贴为 `<BuildingBlocks>` 元素的子级。 
5.  在复制的 XML 中找到包含 `Id="api.signuporsignin"` 的 `<ContentDefinition>` 节点。
6.  将 `LoadUri` 的值从 _~/tenant/default/unified_ 更改为 _https://app_name.azurewebsites.net/home/unified_。自定义策略应如下所示：![内容定义](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>步骤 6：将策略上传到租户
1.  在 [Azure 门户](https://portal.azure.com)中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，打开“Azure AD B2C”
2.  选择“标识体验框架”。
3.  打开“所有策略”。
4.  选择“上传策略”。
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>步骤 7：使用“立即运行”测试自定义策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。

    >[!NOTE]
    >
    >    “立即运行”需要在租户中至少预先注册一个应用程序。 
    >    在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。
    >

2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。
3.  应会看到带有前面所创建的背景的自定义 HTML5![注册或登录策略](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-adding-dynamic-content"></a>步骤 8：添加动态内容
在本部分，我们基于名为 _campaignId_ 的查询字符串参数更改背景。 信赖方应用程序（Web/移动应用）将参数发送到 AAD B2C。 策略读取参数，并将其值发送到 HTML5 模板 


### <a name="step-81-adding-content-definition-parameter"></a>步骤 8.1：添加内容定义参数

若要添加 `ContentDefinitionParameters` 元素，请执行以下操作：
1.  打开策略的 SignUpOrSignin 文件（例如 SignUpOrSignin.xml）。
2.  找到 `<UserJourneyBehaviors>` 节点 
4.  在 `<UserJourneyBehaviors>` 中添加以下 XML 片段 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>步骤 8.2：更改代码以接受查询字符串参数并相应地替换背景图像
在本部分，我们修改 HomeController _unified_ 方法以接受 campaignId 参数。 然后，该方法检查该参数的值，并相应地设置 `ViewData["background"]` 变量。

1. 打开 **Controllers\HomeController.cs**，使用以下代码片段更改 `unified` 方法：

    ```C#
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

2. 找到具有 ID `background_background_image` 的 `<img>` 元素，并将 `src` 替换为 `@ViewData["background"]`。

    ![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>步骤 8.3：上传更改并发布策略
1. 将 Visual Studio 项目发布到 Azure 服务应用
2. 将 SignUpOrSignin.xml 策略上传到 AAD B2C
3.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。 应会看到与前面相同的背景图像。
4. 复制浏览器地址栏中的 URL
5. 将 _campaignId_ 查询字符串参数添加到 URI。 例如，如下图所示添加 `&campaignId=hawaii`![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. 按 **Enter**，页面会显示 Hawaii 背景![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. 现在，将值更改为 *Tokyo* 并按 **Enter**。 页面会显示 Tokyo 背景![更改页面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>步骤 9：更改剩余的用户旅程
如果导航到登录页面并单击“立即注册”链接的话，看到的是默认背景而不是定义的背景。 之所以出现此行为，是因为前面仅更改了注册或登录页面，但还需要更改剩余的 Self-Assert 内容定义。 为此，请完成以下步骤：
* 在步骤 2 中：
    * 下载 **selfasserted** 文件。
    * 复制文件内容。
    * 创建新视图 **selfasserted**。
    * 将 **selfasserted** 添加到 **Home** 控制器。
* 在步骤 4 中 
    * 在扩展策略中，找到包含 `Id="api.selfasserted"`、`Id="api.localaccountsignup"` 和 `Id="api.localaccountpasswordreset"` 的 `<ContentDefinition>` 节点
    *  将 `LoadUri` 属性设置为 selfasserted URI。
* 在“步骤 8.2：更改代码以接受查询字符串参数”中 
* 上传 TrustFrameworkExtensions.xml 策略，并确保它能够通过验证。
* 运行策略测试，然后单击“立即注册”查看结果

## <a name="optional-download-the-complete-policy-files-and-code"></a>[可选] 下载完整的策略文件和代码
* 建议在完成自定义策略入门演练后，使用自己的自定义策略文件而不是这些示例文件来生成方案。  [参考示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* 可在此处下载完整代码：[Visual Studio 解决方案参考示例](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)




