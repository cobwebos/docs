---
title: 使用自定义策略来自定义用户旅程的 UI | Microsoft Docs
description: 了解 Azure Active Directory B2C 自定义策略。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c6882dd56ee96e1fcab5926b77f11ce928bf950b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442349"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>使用自定义策略来自定义用户旅程的 UI

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> 本文深入介绍了 UI 自定义的工作方式，以及如何使用标识体验框架通过 Azure AD B2C 自定义策略实现这种自定义。


无缝用户体验是任何企业到消费者解决方案的关键所在。 无缝用户体验是指，不管是在设备上还是在浏览器上，用户在我们服务中的经历与他们在自己的服务中的经历没有任何差别。

## <a name="understand-the-cors-way-for-ui-customization"></a>了解用于 UI 自定义的 CORS 方法

Azure AD B2C 允许在 Azure AD B2C 通过自定义策略提供和显示的各个页面上自定义用户体验 (UX) 的外观。

出于此目的，Azure AD B2C 会在使用者的浏览器中运行代码，并使用新式标准方法[跨域资源共享 (CORS)](http://www.w3.org/TR/cors/) 从自定义策略中指定的、指向 HTML5/CSS 模板的特定 URL 加载自定义内容。 CORS 是一种机制，允许从资源来源域外部的另一个域请求网页上的受限资源，如字体。

相比之下，在旧式传统方法中，模板页面由解决方案拥有，而解决方案提供的文本和图像以及对布局和外观的控制度都有限，因此，更难以实现无缝体验。CORS 方法支持 HTML5 和 CSS，使你能够：

- 承载内容。解决方案将使用客户端脚本注入其控件。
- 完全控制布局和外观的每个像素。

可通过创建 HTML5/CSS 文件，适当地提供任意数目的内容页面。

> [!NOTE]
> 出于安全原因，目前会阻止使用 JavaScript 进行自定义。 若要解除阻止 JavaScript，需要对 Azure AD B2C 租户使用自定义域名。

在每个 HTML5/CSS 模板中，需要提供一个 *anchor* 元素，该元素对应于稍后所示的 HTML 或内容页面中的所需 `<div id=”api”>` 元素。 Azure AD B2C 要求所有内容页面包含此特定 div 元素

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

页面的 Azure AD B2C 相关内容将注入此 div，页面的其余部分由你控制。 Azure AD B2C JavaScript 代码将提取内容，并将 HTML 注入到此特定 div 元素中。 Azure AD B2C 会适当地注入以下控件：帐户选择器控件、登录控件、多重身份验证（目前为基于电话）控件和属性集合控件。 Azure AD B2C 确保所有控件符合 HTML5 规范且可访问、可以完全设置所有控件的样式，并且控件版本不会倒退。

合并的内容最终以动态文档的形式显示给使用者。

若要确保一切按预期运行，必须：

- 确保内容符合 HTML5 规范且可访问
- 确保为 CORS 启用了内容服务器。
- 通过 HTTPS 提供内容。
- 为所有链接和 CSS 内容使用绝对 URL，例如 https://yourdomain/content。

> [!TIP]
> 若要验证托管内容的站点是否已启用 CORS 并测试 CORS 请求，可以使用站点 http://test-cors.org/。 可以借助此站点将 CORS 请求发送到远程服务器（测试是否支持 CORS），或者将 CORS 请求发送到测试服务器（探索 CORS 的某些功能）。

> [!TIP]
> 站点 http://enable-cors.org/ 也构成了用于实现 CORS 的极有用资源。

得益于这种基于 CORS 的方法，最终用户可以在应用程序与 Azure AD B2C 提供的页面之间获得一致的体验。

## <a name="create-a-storage-account"></a>创建存储帐户

创建存储帐户是先决条件之一。 需要有一个 Azure 订阅来创建 Azure Blob 存储帐户。 可以在 [Azure 网站](https://azure.microsoft.com/pricing/free-trial/)注册免费试用版。

1. 打开浏览会话并导航到 [Azure 门户](https://portal.azure.com)。
2. 使用管理凭据登录。
3. 单击“创建资源” > “存储” > “存储帐户”。  此时会打开“创建存储帐户”窗格。
4. 在“名称”中，提供存储帐户的名称，例如 *contoso369b2c*。 此值稍后将引用为 *storageAccountName*。
5. 选择适当的定价层、资源组和订阅。 确保已选中“固定到启动板”选项。 单击“创建”。
6. 返回到启动板，并单击创建的存储帐户。
7. 在“服务”部分中单击“Blob”。 此时会打开“Blob 服务”窗格。
8. 单击“+ 容器”。
9. 在“名称”中提供容器的名称，例如 *b2c*。 此值稍后将引用为 *containerName*。
9. 为“访问类型”选择“Blob”。 单击“创建”。
10. 创建的容器会显示在“Blob 服务”窗格上的列表中。
11. 关闭“Blob”窗格。
12. 在“存储帐户”窗格中，单击“密钥”图标。 此时会打开“访问密钥”窗格。  
13. 记下“密钥 1”的值。 此值稍后将引用为 *key1*。

## <a name="downloading-the-helper-tool"></a>下载帮助程序工具

1.  从 [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) 下载帮助程序工具。
2.  将 *B2C-AzureBlobStorage-Client-master.zip* 文件保存在本地计算机上。
3.  将 B2C-AzureBlobStorage-Client-master.zip 文件的内容解压缩到本地磁盘，例如，解压缩到 **UI-Customization-Pack** 文件夹下，该文件夹在其下创建 *B2C-AzureBlobStorage-Client-master* 文件夹。
4.  打开该文件夹，并解压缩其中的存档文件 *B2CAzureStorageClient.zip* 的内容。

## <a name="upload-the-ui-customization-pack-sample-files"></a>上传 UI-Customization-Pack 示例文件

1.  使用 Windows 资源管理器导航到上一部分在 *UI-Customization-Pack* 文件夹下创建的 *B2C-AzureBlobStorage-Client-master* 文件夹。
2.  运行 *B2CAzureStorageClient.exe* 文件。 此程序会将指定目录中的所有文件上传到存储帐户，并启用对这些文件的 CORS 访问。
3.  出现提示时，请指定：a.  存储帐户 *storageAccountName* 的名称，例如 *contoso369b2c*。
    b.  Azure Blob 存储的主访问密钥（*密钥 1*），例如 *contoso369b2c*。
    c.  存储 Blob 存储容器 *containerName* 的名称，例如 *b2c*。
    d.  *Starter-Pack* 示例文件的路径，例如 *..\B2CTemplates\wingtiptoys*。

如果遵循了上述步骤，虚构公司 **wingtiptoys** 的 *UI-Customization-Pack* 的 HTML5 和 CSS 文件现在会指向存储帐户。  可以在 Azure 门户中打开相关的容器窗格，验证是否已正确上传内容。 也可以通过从浏览器访问页面来验证是否已正确上传内容。 有关详细信息，请参阅 [Azure Active Directory B2C：用于演示页面用户界面 (UI) 自定义功能的帮助程序工具](active-directory-b2c-reference-ui-customization-helper-tool.md)。

## <a name="ensure-the-storage-account-has-cors-enabled"></a>确保存储帐户已启用 CORS

必须在终结点上启用 CORS（跨域资源共享）才能让 Azure AD B2C 加载内容。 这是因为，该内容未承载在 Azure AD B2C 要从中提供页面的域上。

若要验证托管内容的存储是否已启用 CORS，请继续执行以下步骤：

1. 打开浏览会话，并使用页面 *unified.html* 在存储帐户中的位置的完整 URL `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html` 导航到该页面。 例如，https://contoso369b2c.blob.core.windows.net/b2c/unified.html。
2. 导航到 http://test-cors.org。在此站点中，可以验证所用的页面是否已启用 CORS。  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. 在“远程 URL”中，输入 unified.html 内容的完整 URL，并单击“发送请求”。
4. 验证“结果”部分的输出是否包含“XHR 状态: 200”（表明已启用 CORS）。
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
存储帐户现在应包含本演示中所述的名为 *b2c* 的 Blob 容器，该容器包含 *Starter-Pack* 中的以下 wingtiptoys 模板。

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

下表描述了上述 HTML5 页面的用途。

| HTML5 模板 | 说明 |
|----------------|-------------|
| *phonefactor.html* | 此页面可用作多重身份验证页面的模板。 |
| *resetpassword.html* | 此页面可用作“忘记密码”页面的模板。 |
| *selfasserted.html* | 此页面可用作社交帐户注册页面、本地帐户注册页面或本地帐户登录页面的模板。 |
| *unified.html* | 此页面可用作统一注册或登录页面的模板。 |
| *updateprofile.html* | 此页面可用作个人资料更新页面的模板。 |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>将 HTML5/CSS 模板的链接添加到用户旅程

可以通过编辑自定义策略，将 HTML5/CSS 模板的链接直接添加到用户旅程。

要在用户旅程中使用的自定义 HTML5/CSS 模板必须在可在用户旅程中使用的内容定义列表中指定。 为此，必须在自定义策略 XML 文件的 *<BuildingBlocks>* 节下面声明一个可选的 *<ContentDefinitions>* XML 元素。

下表描述了 Azure AD B2C 标识体验引擎识别的内容定义 ID 集及其相关的页面类型。

| 内容定义 ID | 说明 |
|-----------------------|-------------|
| *api.error* | **错误页面**。 遇到异常或错误时显示此页面。 |
| *api.idpselections* | **标识提供者选择页面**。 此页面包含有可供用户在登录期间选择的标识提供者列表。 这些提供者包括企业标识提供者、社交标识提供者（如 Facebook 和 Google+）或本地帐户（基于电子邮件地址或用户名）。 |
| *api.idpselections.signup* | **用于注册的标识提供者选项**。 此页面包含有可供用户在注册期间选择的标识提供者列表。 这些提供者包括企业标识提供者、社交标识提供者（如 Facebook 和 Google+）或本地帐户（基于电子邮件地址或用户名）。 |
| *api.localaccountpasswordreset* | **忘记密码页面**。 此页面包含一个窗体，用户必须填写该窗体才能发起密码重置。  |
| *api.localaccountsignin* | **本地帐户登录页面**。 此页面包含一个登录窗体，用户在使用基于电子邮件地址或用户名的本地帐户登录时必须填写此窗体。 该窗体可以包含文本输入框和密码输入框。 |
| *api.localaccountsignup* | **本地帐户注册页面**。 此页面包含一个注册窗体，用户在注册基于电子邮件地址或用户名的本地帐户时必须填写此窗体。 该窗体可以包含不同的输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。 |
| *api.phonefactor* | **多重身份验证页面** 在此页面上，用户可以在注册或登录期间（使用文字或语音）验证其电话号码。 |
| *api.selfasserted* | **社交帐户注册页面**。 此页面包含一个注册窗体，用户在使用 Facebook 或 Google+ 等社交标识提供者的现有帐户注册时必须填写此窗体。 此页面类似于上述社交帐户注册页面，但密码输入字段例外。 |
| *api.selfasserted.profileupdate* | **个人资料更新页面**。 此页面包含用户可以用来更新其个人资料的窗体。 此页面类似于上述社交帐户注册页面，但密码输入字段例外。 |
| *api.signuporsignin* | **统一注册或登录页面**。  此页面处理用户的注册和登录，他们可以使用企业标识提供者、社交标识提供者（例如 Facebook 或 Google+）或本地帐户。

## <a name="next-steps"></a>后续步骤
[参考：了解如何对 B2C 中的标识体验框架使用自定义策略](active-directory-b2c-reference-custom-policies-understanding-contents.md)
