---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116996"
---
## <a name="use-custom-page-content"></a>使用自定义页面内容

使用页面 UI 自定义功能可对任何自定义策略的外观进行自定义。 还可以在应用程序与 Azure AD B2C 之间保持品牌和视觉一致性。

### <a name="how-it-works"></a>工作原理

Azure AD B2C 使用[跨源资源共享 （CORS）](https://www.w3.org/TR/cors/)在客户的浏览器中运行代码。 在运行时，内容从您在用户流或自定义策略中指定的 URL 加载。 用户体验中的每个页面从你为该页面指定的 URL 加载其内容。 从 URL 加载内容后，该内容将与 Azure AD B2C 插入的 HTML 片段合并，然后向客户显示页面。

![自定义页面内容边距](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>自定义 HTML 页面内容

创建具有您自己的品牌的 HTML 页面，以提供自定义页面内容。 此页面可以是静态`*.html`页面，也可以是动态页面，如 .NET、Node.js 或 PHP。

自定义页面内容可以包含任何 HTML 元素，包括 CSS 和 JavaScript，但不能包含不安全元素（如 iframe）。 唯一需要的元素是设置为`id``api`的 div 元素，例如 HTML`<div id="api"></div>`页中的此元素。

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>自定义默认的 Azure AD B2C 页面

您可以自定义 Azure AD B2C 的默认页面内容，而不是从头开始创建自定义页面内容。

下表列出了 Azure AD B2C 提供的默认页面内容。 下载文件并将其用作创建您自己的自定义页面的起点。

| 默认页面 | 描述 | 内容定义 ID<br/>（仅限自定义策略） |
|:-----------------------|:--------|-------------|
| [例外.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **错误页**。 遇到异常或错误时显示此页面。 | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **自我断言页**。 使用此文件作为社交帐户注册页面、本地帐户注册页面、本地帐户登录页面、密码重置等的自定义页面内容。 该窗体可以包含各种输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。 | *api.本地帐户signin，* *api.本地帐户注册*， *api.local帐户密码重置*， *api.自我断言* |
| [多因素-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多重身份验证页面** 在此页面上，用户可以在注册或登录期间（使用文字或语音）验证其电话号码。 | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **个人资料更新页面**。 此页面包含用户在更新其个人资料时可以访问的窗体。 除了密码输入字段之外，此页面类似于社交帐户注册页面。 | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **统一注册或登录页面**。 此页面处理用户注册和登录过程。 用户可以使用企业标识提供者、社交标识提供者（例如 Facebook 或 Google+）或本地帐户。 | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>托管页面内容

使用自己的 HTML 和 CSS 文件自定义 UI 时，请在支持 CORS 的任何公开可用的 HTTPS 终结点上托管 UI 内容。 例如[，Azure Blob](../articles/storage/blobs/storage-blobs-introduction.md)存储[、Azure 应用服务](/azure/app-service/)、Web 服务器、CDN、AWS S3 或文件共享系统。

## <a name="guidelines-for-using-custom-page-content"></a>使用自定义页面内容的指南

- 在 HTML 文件中包含媒体、CSS 和 JavaScript 文件等外部资源时，请使用绝对 URL。
- 使用[页面布局版本](../articles/active-directory-b2c/page-layout.md)1.2.0 及以上，可以在`data-preload="true"`HTML 标记中添加属性以控制 CSS 和 JavaScript 的加载顺序。 使用`data-preload=true`时，将构造页面，然后再向用户显示。 此属性通过预加载 CSS 文件，而不向用户显示未样式的 HTML，有助于防止页面"闪烁"。 以下 HTML 代码段显示了标记的使用`data-preload`。
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 我们建议您从默认页面内容开始，并在它之上构建。
- 您可以在自定义内容中包括 JavaScript，用于[用户流](../articles/active-directory-b2c/user-flow-javascript-overview.md)和[自定义策略](../articles/active-directory-b2c/javascript-samples.md)。
- 支持的浏览器版本：
  - Internet Explorer 11、10 和 Microsoft Edge
  - 对 Internet Explorer 9 和 8 的支持有限
  - Google Chrome 42.0 和更高版本
  - Mozilla Firefox 38.0 和更高版本
  - 适用于 iOS 和 macOS 的 Safari，版本 12 及以上
- 由于安全限制，Azure AD B2C 不支持`frame`，`iframe`或`form`HTML 元素。

## <a name="custom-page-content-walkthrough"></a>自定义页面内容演练

以下是流程概述：

1. 准备一个位置来承载自定义页面内容（可公开访问的、支持 CORS 的 HTTPS 终结点）。
1. 下载并自定义默认页面内容文件，例如`unified.html`。
1. 发布自定义页面内容，以公开可用的 HTTPS 终结点。
1. 为 Web 应用设置跨源资源共享 (CORS)。
1. 将策略指向自定义策略内容 URI。

### <a name="1-create-your-html-content"></a>1. 创建 HTML 内容

在标题中使用产品的品牌名称创建自定义页面内容。

1. 复制以下 HTML 代码段。 它是格式良好的 HTML5，具有一个称为*\<div\>\<id_"api"/div\>* 的空元素，位于*\<正文\>* 标记中。 此元素指示要在何处插入 Azure AD B2C 内容。

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
> 如果使用login.microsoftonline.com，则由于安全限制，HTML 表单元素将被删除。 如果要在自定义 HTML 内容中使用 HTML 表单元素，[请使用b2clogin.com](../articles/active-directory-b2c/b2clogin.md)。

### <a name="2-create-an-azure-blob-storage-account"></a>2. 创建 Azure Blob 存储帐户

在本文中，我们使用 Azure Blob 存储来托管内容。 可以选择将内容托管在 Web 服务器上，但必须[在 Web 服务器上启用 CORS](https://enable-cors.org/server.html)。

要在 Blob 存储中托管 HTML 内容，请执行以下步骤：

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 在 **"中心"** 菜单上，选择 **"新建** > **存储** > **存储帐户**"。
1. 为存储帐户选择**订阅**。
1. 创建资源组或选择现有**资源组**。
1. 输入存储帐户的唯一**名称**。
1. 选择存储帐户的**地理位置**。
1. “部署模型”可保留为“Resource Manager”。********
1. “性能”可保留为“标准”。********
1. 将“帐户类型”更改为“Blob 存储”。********
1. “复制”可保留为“RA-GRS”。********
1. “访问层”可保留为“热”。********
1. 选择 **"查看 + 创建**"以创建存储帐户。
    部署完成后，**将自动打开存储帐户**页。

#### <a name="21-create-a-container"></a>2.1 创建容器

要在 Blob 存储中创建公共容器，请执行以下步骤：

1. 在左侧菜单中的**Blob 服务**下，选择**Blob**。
1. 选择 **+容器**。
1. 对于**名称**，请输入*根*。 名称可以是您选择的名称，例如*contoso*，但为了简单起见，我们在此示例中使用*根*。
1. 对于**公共访问级别**，选择**Blob，** 然后**选择"确定**"。
1. 选择**root**以打开新容器。

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 上传自定义页面内容文件

1. 选择 **"上传**"。
1. 选择 **"选择文件"** 旁边的文件夹图标。
1. 导航到并选择**自定义-ui.html**，您之前在"页面 UI 自定义"部分中创建。
1. 如果要上载到子文件夹，请展开 **"高级"，** 并在 **"上载到"文件夹中**输入文件夹名称。
1. 选择 **"上传**"。
1. 选择您上传的**自定义-ui.html** blob。
1. 在**URL**文本框的右侧，选择"**复制到剪贴板**"图标以将 URL 复制到剪贴板。
1. 在 Web 浏览器中，导航到复制的 URL 以验证您上传的 Blob 是可访问的。 如果无法访问它，例如，如果遇到`ResourceNotFound`错误，请确保容器访问类型设置为**blob**。

### <a name="3-configure-cors"></a>3. 配置 CORS

通过执行以下步骤为跨源资源共享配置 Blob 存储：

1. 在菜单中，选择“CORS”****。
1. 对于“允许的源”****，请输入 `https://your-tenant-name.b2clogin.com`。 将 `your-tenant-name` 替换为 Azure AD B2C 租户的名称。 例如，`https://fabrikam.b2clogin.com` 。 输入租户名称时，请使用所有小写字母。
1. 对于“允许的方法”，请同时选择 `GET` 和 `OPTIONS`****。
1. 对于“允许的标头”****，请输入一个星号 (*)。
1. 对于“公开的标头”****，请输入一个星号 (*)。
1. 对于“最大期限”****，请输入 200。
1. 选择“保存”。****

#### <a name="31-test-cors"></a>3.1 测试 CORS

通过执行以下步骤验证您是否已准备就绪：

1. 重复配置 CORS 步骤。 对于**允许的源**，输入`https://www.test-cors.org`
1. 导航到[www.test-cors.org](https://www.test-cors.org/) 
1. 对于 **"远程 URL"** 框，请粘贴 HTML 文件的 URL。 例如： `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. 选择 **"发送请求**"。
    结果应为`XHR status: 200`。 
    如果收到错误，请确保 CORS 设置正确。 可能还需要清除浏览器缓存，或通过按 Ctrl+Shift+P 打开专用浏览会话。
