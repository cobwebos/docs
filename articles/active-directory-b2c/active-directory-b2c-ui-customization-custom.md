---
title: 使用自定义策略自定义 UI - Azure AD B2C | Microsoft Docs
description: 了解在 Azure AD B2C 中使用自定义策略时自定义用户界面 (UI) 的相关事项。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 659910f239087a1d87d650c05af157c085e8c72c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C：在自定义策略中配置 UI 自定义

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文后，会生成一个带有公司品牌和外观的注册与登录自定义策略。 使用 Azure Active Directory B2C (Azure AD B2C)，几乎可以完全控制呈现给用户的 HTML 和 CSS 内容。 使用自定义策略时，需要以 XML 配置 UI 自定义，而不是使用 Azure 门户中的控件进行配置。 

## <a name="prerequisites"></a>先决条件

在开始之前，必须先完成[自定义策略入门](active-directory-b2c-get-started-custom.md)。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。

## <a name="page-ui-customization"></a>页面 UI 自定义

使用页面 UI 自定义功能可对任何自定义策略的外观进行自定义。 还可以在应用程序与 Azure AD B2C 之间保持品牌和视觉一致性。

工作原理如下：Azure AD B2C 在客户的浏览器中运行代码，并使用称为[跨域资源共享 (CORS)](http://www.w3.org/TR/cors/) 的现代方法。 首先，请在包含自定义 HTML 内容的自定义策略中指定 URL。 Azure AD B2C 会将 UI 元素与从 URL 加载的 HTML 内容合并，然后向客户显示页面。

## <a name="create-your-html5-content"></a>创建 HTML5 内容

在标题中创建包含产品的品牌名称的 HTML 内容。

1. 复制以下 HTML 代码段。 它是格式正确的 HTML5，*\<body\>* 标记内包含名为 *\<div id="api"\>\</div\>* 的空元素。 此元素指示要在何处插入 Azure AD B2C 内容。

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

   >[!NOTE]
   >出于安全原因，目前会阻止使用 JavaScript 进行自定义。

2. 将复制的代码段粘贴到文本编辑器中，然后将文件保存为 *customize-ui.html*。

## <a name="create-an-azure-blob-storage-account"></a>创建 Azure Blob 存储帐户

>[!NOTE]
> 在本文中，我们使用 Azure Blob 存储来托管内容。 可以选择将内容托管在 Web 服务器上，但必须[在 Web 服务器上启用 CORS](https://enable-cors.org/server.html)。

若要将此 HTML 内容托管在 Blob 存储中，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“新建” > “存储” > “存储帐户”。
3. 为存储帐户输入唯一的**名称**。
4. “部署模型”可保留为“Resource Manager”。
5. 将“帐户类型”更改为“Blob 存储”。
6. “性能”可保留为“标准”。
7. “复制”可保留为“RA-GRS”。
8. “访问层”可保留为“热”。
9. “存储服务加密”可保留为“已禁用”。
10. 为存储帐户选择一个**订阅**。
11. 创建一个**资源组**或选择现有的资源组。
12. 为存储帐户选择**地理位置**。
13. 单击“创建”以创建存储帐户。  
    在部署完成后，“存储帐户”边栏选项卡将自动打开。

## <a name="create-a-container"></a>创建容器

若要在 Blob 存储中创建公共容器，请执行以下操作：

1. 单击“概述”选项卡。
2. 单击“容器”。
3. 对于“名称”，键入“$root”。
4. 将“访问类型”设置为“Blob”。
5. 单击“$root”以打开新容器。
6. 单击“上传”。
7. 单击“选择文件”旁边的文件夹图标。
8. 转到 **customize-ui.html** ，这是你之前在 [页面 UI 自定义](#the-page-ui-customization-feature) 部分中创建的。
9. 单击“上传” 。
10. 选择你已上传的 customize-ui.html blob。
11. 单击“URL”旁边的“复制”。
12. 在浏览器中，粘贴复制的 URL，然后转到该站点。 如果该站点不可访问，请确保容器访问类型设置为“Blob”。

## <a name="configure-cors"></a>配置 CORS

通过执行以下操作为 Blob 存储配置跨域资源共享：

>[!NOTE]
>想要使用我们的示例 HTML 和 CSS 内容来尝试 UI 自定义功能？ 我们提供了[一个简单的帮助工具](active-directory-b2c-reference-ui-customization-helper-tool.md)，它可以在 Blob 存储帐户中上传和配置示例内容。 如果使用该工具，请直接跳到[修改注册或登录自定义策略](#modify-your-sign-up-or-sign-in-custom-policy)。

1. 在“存储”边栏选项卡中，在“设置”下，打开“CORS”。
2. 单击 **“添加”**。
3. 对于“允许的来源”，键入一个星号 (\*)。
4. 在“允许的谓词”下拉列表中，同时选择“GET”和“OPTIONS”。
5. 对于“允许的标头”，键入一个星号 (\*)。
6. 对于“公开的标头”，键入一个星号 (\*)。
7. 对于“最长存在时间(秒)”，键入“200”。
8. 单击 **“添加”**。

## <a name="test-cors"></a>测试 CORS

通过执行以下操作验证你已准备就绪：

1. 转到 [www.test-cors.org](http://www.test-cors.org/) 网站，然后将 URL 粘贴到“远程 URL”框中。
2. 单击“发送请求”。  
    如果收到错误，请确保 [CORS 设置](#configure-cors)正确。 可能还需要清除浏览器缓存，或通过按 Ctrl+Shift+P 打开专用浏览会话。

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>修改注册或登录自定义策略

在顶级 *\<TrustFrameworkPolicy\>* 标记下，应当会发现 *\<BuildingBlocks\>* 标记。 在 *\<BuildingBlocks\>* 标记内，通过复制以下示例添加一个 *\<ContentDefinitions\>* 标记。 将 *your_storage_account* 替换为你的存储帐户的名称。

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>上传已更新的自定义策略

1. 在 [Azure 门户](https://portal.azure.com)中，[切换到你的 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后打开“Azure AD B2C”边栏选项卡。
2. 单击“所有策略”。
3. 单击“上传策略”。
4. 上传包含你之前添加的 *\<ContentDefinitions\>* 标记的 `SignUpOrSignin.xml`。

## <a name="test-the-custom-policy-by-using-run-now"></a>使用“立即运行”测试自定义策略

1. 在“Azure AD B2C”边栏选项卡上，转到“所有策略”。
2. 选择上传的自定义策略，并单击“立即运行”按钮。
3. 现在，应该可以使用电子邮件地址进行注册了。

## <a name="reference"></a>引用

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

在 [修改注册或登录自定义策略](#modify-your-sign-up-or-sign-in-custom-policy) 部分中，你已配置了 的内容定义`api.idpselections` 。 下表中列出了 Azure AD B2C 标识体验框架可以识别的整组内容定义 ID 及其说明：

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

有关可以自定义的 UI 元素的其他信息，请参阅[有关对内置策略进行 UI 自定义的参考指南](active-directory-b2c-reference-ui-customization.md)。
