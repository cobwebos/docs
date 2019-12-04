---
title: 使用 Azure Active Directory B2C 中的自定义策略自定义应用程序的用户界面 | Microsoft Docs
description: 了解如何使用 Azure Active Directory B2C 中的自定义策略自定义用户界面。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2f0e13b4e68ee4b94a254cb8497a44cc0b8b470f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209447"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 中的自定义策略自定义应用程序的用户界面

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文后，你将拥有一个带有品牌和外观的注册和登录自定义策略。 使用 Azure Active Directory B2C (Azure AD B2C)，几乎可以完全控制呈现给用户的 HTML 和 CSS 内容。 使用自定义策略时，需要以 XML 配置 UI 自定义，而不是使用 Azure 门户中的控件进行配置。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。

## <a name="page-ui-customization"></a>页面 UI 自定义

使用页面 UI 自定义功能可对任何自定义策略的外观进行自定义。 还可以在应用程序与 Azure AD B2C 之间保持品牌和视觉一致性。

工作原理如下：Azure AD B2C 在客户的浏览器中运行代码，并使用称为[跨域资源共享 (CORS)](https://www.w3.org/TR/cors/) 的现代方法。 首先，请在包含自定义 HTML 内容的自定义策略中指定 URL。 Azure AD B2C 会将 UI 元素与从 URL 加载的 HTML 内容合并，然后向客户显示页面。

## <a name="create-your-html5-content"></a>创建 HTML5 内容

在标题中创建包含产品的品牌名称的 HTML 内容。

1. 复制以下 HTML 代码段。 它是格式正确的 HTML5， *\<body\>\< 标记内包含名为 \>* div id="api" *\</div\>* 的空元素。 此元素指示要在何处插入 Azure AD B2C 内容。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. 将复制的代码段粘贴到文本编辑器中，然后将文件保存为 *customize-ui.html*。

> [!NOTE]
> 如果使用 login.microsoftonline.com，将删除 HTML 窗体元素。 如果要使用自定义 HTML 内容中的 HTML 窗体元素，请使用 b2clogin.com。 有关其他权益，请参阅[使用 b2clogin.com](b2clogin.md) 。

## <a name="create-an-azure-blob-storage-account"></a>创建 Azure Blob 存储帐户

>[!NOTE]
> 在本文中，我们使用 Azure Blob 存储来托管内容。 可以选择将内容托管在 Web 服务器上，但必须[在 Web 服务器上启用 CORS](https://enable-cors.org/server.html)。

若要在 Blob 存储中托管此 HTML 内容，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“中心”菜单上，选择“新建” **“存储”** “存储帐户”。 >  > 
1. 为存储帐户选择一个**订阅**。
1. 创建一个**资源组**或选择现有的资源组。
1. 为存储帐户输入唯一的**名称**。
1. 为存储帐户选择**地理位置**。
1. “部署模型”可保留为“Resource Manager”。
1. “性能”可保留为“标准”。
1. 将“帐户类型”更改为“Blob 存储”。
1. “复制”可保留为“RA-GRS”。
1. “访问层”可保留为“热”。
1. 单击 "**查看 + 创建**" 以创建存储帐户。
    部署完成后，将自动打开 "**存储帐户**" 页。

## <a name="create-a-container"></a>创建容器

若要在 Blob 存储中创建公共容器，请执行以下步骤：

1. 在左侧菜单中的 " **blob 服务**" 下，选择 " **blob**"。
1. 单击 " **+ 容器**"。
1. 对于 "**名称**"，请输入*root*。 这可以是你选择的名称（例如*wingtiptoys*），但为了简单起见，我们在此示例中使用了*root* 。
1. 对于 "**公共访问级别**"，请选择 " **Blob**"，然后选择 **"确定"** 。
1. 单击 "**根**" 打开新容器。
1. 单击“上传”。
1. 单击“选择文件”旁边的文件夹图标。
1. 导航到并选择前面在 "页面 UI 自定义" 部分中创建的**customize-ui.html。**
1. 如果要上传到子文件夹，请展开 "**高级**"，然后在 "**上传到文件夹**" 中输入文件夹名称。
1. 选择“上传”。
1. 选择上传的**customize-ui.html** blob。
1. 在 " **URL** " 文本框的右侧，选择 "**复制到剪贴板**" 图标以将 URL 复制到剪贴板。
1. 在 web 浏览器中，导航到复制的 URL，以验证上传的 blob 是否可访问。 如果它不可访问，例如如果遇到 `ResourceNotFound` 错误，请确保容器访问类型设置为**blob**。

## <a name="configure-cors"></a>配置 CORS

通过执行以下步骤，将 Blob 存储配置为跨域资源共享：

1. 在菜单中，选择“CORS”。
1. 对于“允许的源”，请输入 `https://your-tenant-name.b2clogin.com`。 将 `your-tenant-name` 替换为 Azure AD B2C 租户的名称。 例如，`https://fabrikam.b2clogin.com`。 输入租户名称时，需要使用全小写字母。
1. 对于“允许的方法”，请同时选择 **和**`GET``OPTIONS`。
1. 对于“允许的标头”，请输入一个星号 (*)。
1. 对于“公开的标头”，请输入一个星号 (*)。
1. 对于“最大期限”，请输入 200。
1. 单击“ **保存**”。

## <a name="test-cors"></a>测试 CORS

通过执行以下步骤验证是否已准备就绪：

1. 转到 [www.test-cors.org](https://www.test-cors.org/) 网站，然后将 URL 粘贴到“远程 URL”框中。
1. 单击“发送请求”。
    如果收到错误，请确保 [CORS 设置](#configure-cors)正确。 可能还需要清除浏览器缓存，或通过按 Ctrl+Shift+P 打开专用浏览会话。

## <a name="modify-the-extensions-file"></a>修改扩展文件

要配置 UI 自定义，请将 ContentDefinition 及其子元素从基本文件复制到扩展文件。

1. 打开策略的基文件。 例如， <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em> 。 这是自定义策略初学者包中包含的策略文件之一，你应该已在先决条件中获取[自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)。
1. 搜索并复制 ContentDefinitions 元素的全部内容。
1. 打开扩展文件， 例如，TrustFrameworkExtensions.xml。 搜索 BuildingBlocks 元素。 如果该元素不存在，请添加该元素。
1. 粘贴作为 BuildingBlocks 元素的子元素复制的 ContentDefinitions 元素的全部内容。
1. 在复制的 XML 中搜索包含  **的 ContentDefinition**`Id="api.signuporsignin"` 元素。
1. 将 LoadUri 的值更改为上传到存储的 HTML 文件的 URL。 例如，`https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`。

    自定义策略应如下所示：

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

## <a name="upload-your-updated-custom-policy"></a>上传已更新的自定义策略

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
1. 选择“标识体验框架”。
1. 单击“所有策略”。
1. 单击“上传策略”。
1. 上传以前已更改的扩展文件。

## <a name="test-the-custom-policy-by-using-run-now"></a>使用“立即运行”测试自定义策略

1. 在 " **Azure AD B2C** " 页上，中转到 "**所有策略**"。
1. 选择上传的自定义策略，并单击“立即运行”按钮。
1. 现在，应该可以使用电子邮件地址进行注册了。

## <a name="reference"></a>参考

### <a name="sample-templates"></a>示例模板
可以在以下位置找到用于 UI 自定义的示例模板：

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

sample_templates/wingtip 文件夹包含以下 HTML 文件：

| HTML5 模板 | 说明 |
|----------------|-------------|
| *phonefactor.html* | 将此文件用作多重身份验证页面的模板。 |
| *resetpassword.html* | 将此文件用作“忘记密码”页面的模板。 |
| *selfasserted.html* | 将此文件用作社交帐户注册页面、本地帐户注册页面或本地帐户登录页面的模板。 |
| *unified.html* | 将此文件用作统一注册或登录页面的模板。 |
| *updateprofile.html* | 将此文件用作个人资料更新页面的模板。 |

下面是有关如何使用此示例的步骤：

1. 将存储库克隆到本地计算机。 选择 sample_templates 下的模板文件夹。 您可以使用 `wingtip` 或 `contoso`。
1. 按照前面几节中所述，将 `css`、`fonts`和 `images` 文件夹下的所有文件上传到 Blob 存储。
1. 接下来，在 `wingtip` 或 `contoso` （在第一步中选择）的根目录中打开每个 \*.html 文件，并将 "http://localhost" 的所有实例替换为在步骤2中上载的 css、图像和字体文件的 Url。
1. 保存 \*.html 文件并将其上传到 Blob 存储。
1. 现在，如上文所述[修改扩展文件](#modify-the-extensions-file)来修改扩展文件。
1. 如果发现缺少字体、图像或 CSS，请检查扩展策略和 \*.html 文件中的引用。

### <a name="content-definition-ids"></a>内容定义 ID

在“修改注册或登录自定义策略”部分中，你已配置了 `api.idpselections` 的内容定义。 下表中列出了 Azure AD B2C 标识体验框架可以识别的整组内容定义 ID 及其说明：

| 内容定义 ID | 说明 |
|-----------------------|-------------|
| *api.error* | **错误页面**。 遇到异常或错误时显示此页面。 |
| *api.idpselections* | **标识提供者选择页面**。 此页面包含有可供用户在登录期间选择的标识提供者列表。 这些选项是企业标识提供者、社交标识提供者（例如 Facebook 和 Google+）或本地帐户。 |
| *api.idpselections.signup* | **用于注册的标识提供者选项**。 此页面包含有可供用户在注册期间选择的标识提供者列表。 这些选项是企业标识提供者、社交标识提供者（例如 Facebook 和 Google+）或本地帐户。 |
| *api.localaccountpasswordreset* | **忘记密码页面**。 此页面包含一个窗体，用户必须填写该窗体才能发起密码重置。  |
| *api.localaccountsignin* | **本地帐户登录页面**。 此页面包含一个登录窗体，用于通过基于电子邮件地址或用户名的本地帐户进行登录。 该窗体可以包含文本输入框和密码输入框。 |
| *api.localaccountsignup* | **本地帐户注册页面**。 此页面包含一个注册窗体，用于通过基于电子邮件地址或用户名的本地帐户进行注册。 该窗体可以包含各种输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。 |
| *api.phonefactor* | **多重身份验证页面** 在此页面上，用户可以在注册或登录期间（使用文字或语音）验证其电话号码。 |
| *api.selfasserted* | **社交帐户注册页面**。 此页面包含一个注册窗体，用户在使用 Facebook 或 Google+ 等社交标识提供者的现有帐户注册时必须填写此窗体。 除了密码输入字段之外，此页面类似于前面的社交帐户注册页面。 |
| *api.selfasserted.profileupdate* | **个人资料更新页面**。 此页面包含用户可以用来更新其个人资料的窗体。 除了密码输入字段之外，此页面类似于社交帐户注册页面。 |
| *api.signuporsignin* | **统一注册或登录页面**。 此页面处理用户的注册和登录，用户可以使用企业标识提供者、社交标识提供者（例如 Facebook 或 Google+）或本地帐户。  |

## <a name="next-steps"></a>后续步骤

有关可自定义的 UI 元素的详细信息，请参阅[用户流的 ui 自定义的参考指南](active-directory-b2c-reference-ui-customization.md)。
