---
title: "Azure Active Directory B2C：使用自定义策略进行 UI 自定义 | Microsoft Docs"
description: "有关在 Azure AD B2C 中使用自定义策略进行用户界面 (UI) 自定义的主题"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: gsacavdm
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 215f9baeda7cd0bcf3fd66893919575647849e7d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C：在自定义策略中进行 UI 自定义

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文后，将会生成一个带有你的公司品牌和外观的注册与登录自定义策略。  在 Azure AD B2C 中，几乎可以完全控制呈现给最终用户的 HTML 和 CSS 内容。  使用自定义策略时，自定义 UI 将在 XML 中配置，而不是在 Azure 门户中使用控件配置。

## <a name="prerequisites"></a>先决条件

在继续之前，必须先完成[自定义策略入门](active-directory-b2c-get-started-custom.md)。  应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。

### <a name="confirming-your-b2c-tenant"></a>确认 B2C 租户

由于自定义策略仍以个人预览提供，因此请确认为自定义策略上传启用了 Azure AD B2C 租户：

1. 在 [Azure 门户](https://portal.azure.com)中，[切换到 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后打开 Azure AD B2C 边栏选项卡。
1. 单击“所有策略”。
1. 确保“上传策略”按钮可用。  如果该按钮被禁用，请向 AADB2CPreview@microsoft.com 发送电子邮件。

## <a name="the-page-ui-customization-feature"></a>页面 UI 自定义功能

使用页面 UI 自定义功能可对任何自定义策略的外观进行自定义。  在应用程序与 Azure AD B2C 之间保持品牌和视觉一致性。

工作原理如下：Azure AD B2C 在使用者的浏览器中运行代码，并使用称为[跨域资源共享 (CORS)](http://www.w3.org/TR/cors/) 的现代方法。  首先，请在包含自定义 HTML 内容的自定义策略中指定 URL。  Azure AD B2C 会将 UI 元素与从 URL 加载的 HTML 内容合并，并向使用者显示页面。

## <a name="creating-your-html5-content"></a>创建 HTML5 内容

让我们在标题中使用产品的品牌名称创建 HTML 内容。

1. 在此 html 片段中单击“复制”。  这是一段采用适当格式的 HTML5 代码，它在 `<body>` 中的某个位置包含名为 `<div id="api"></div>` 的空元素。 此元素标记 Azure AD B2C 内容的插入位置。

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

1. 请将它粘贴到文本编辑器中，然后将文件保存为 `customize-ui.html`

## <a name="create-a-blob-storage-account"></a>创建 Blob 存储帐户

>[!NOTE]
> 在本指南中，我们使用 Azure Blob 存储来托管内容。  也可以在 Web 服务器上托管内容，但这需要[在 Web 服务器上启用跨域资源共享 (CORS)](https://enable-cors.org/server.html)。

我们在 Azure Blob 存储中托管此 HTML。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“中心”菜单上，选择“新建” -> “存储” -> “存储帐户”。
1. 输入存储帐户的唯一**名称**。
1. 可将“部署模型”保留为“Resource Manager”。
1. 将“帐户类型”更改为“Blob 存储”。
1. “性能”可以保留为“标准”。
1. “复制”可以保留为“RA-GRS”。
1. “访问层”可以保留为“热”。
1. “存储服务加密”可以保留为“已禁用”。
1. 选择存储帐户的**订阅**。
1. 创建**资源组**，或选择现有的资源组。
1. 选择存储帐户的**地理位置**。
1. 单击“创建”以创建存储帐户。
1. 等待部署完成，随后存储帐户边栏选项卡将自动打开。

## <a name="create-a-container"></a>创建容器

让我们在 Azure Blob 存储中创建公共容器。

1. 切换到左侧名为“概述”的选项卡。
1. 单击“+ 容器”。
1. 对于“名称”，请键入 `$root`。
1. 将“访问类型”设置为“Blob”。
1. 单击“$root”打开新容器。
1. 单击“上载” 。
1. 单击 `Select a file` 旁边的文件夹图标。
1. 浏览到在[前面部分](#the-page-ui-customization-feature)创建的 `customize-ui.html`。
1. 单击“上载” 。
1. 选择上传的、名为 `customize-ui.html` 的 Blob。
1. 单击“URL”旁边的复制按钮。
1. 打开浏览器并导航到此 URL。  如果无法访问此 URL，请确保容器访问类型设置为 Blob。

## <a name="configure-cors"></a>配置 CORS

接下来，为跨域资源共享 (CORS) 配置 Azure Blob 存储。

>[!NOTE]
>想要使用我们的示例 HTML 和 CSS 内容来尝试 UI 自定义功能？  我们提供了[一个简单的帮助工具](active-directory-b2c-reference-ui-customization-helper-tool.md)，它可以在 Azure Blob 存储帐户中配置示例内容。  如果使用该工具，请直接跳到[修改注册或登录自定义策略](#modify-your-sign-up-or-sign-in-custom-policy)

1. 在“存储”边栏选项卡中的“设置”下面，打开“CORS”。
1. 单击“+ 添加”。
1. 将 `Allowed origins` 设置为 `*`。
1. 在名为 `Allowed verbs` 的下拉列表中，同时选择 `GET` 和 `OPTIONS`。
1. 将 `Allowed headers` 设置为 `*`。
1. 将 `Exposed headers` 设置为 `*`。
1. 将 `Maximum age (seconds)` 设置为 `200`。
1. 单击 **“添加”**。

## <a name="testing-cors"></a>测试 CORS

让我们验证是否已准备就绪。

1. 导航到 http://test-cors.org/ 并将 URL 粘贴到 `Remote URL` 字段中。
1. 单击“发送请求”
1. 如果收到错误，请确保[CORS 设置](#configure-cors)正确。  可能还需要清除浏览器缓存，或尝试建立专用浏览会话 `CTRL-SHIFT-P`。

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>修改注册或登录自定义策略

1. 在顶级 `<TrustFrameworkPolicy>` 标记下面，应会看到 `<BuildingBlocks>` 标记。  在 `<BuildingBlocks>` 标记中，通过复制此示例来添加 `ContentDefinitions` 标记。  将 `{your_storage_account}` 替换为你的存储帐户的名称。

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

1. 在 [Azure 门户](https://portal.azure.com)中，[切换到 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后打开 Azure AD B2C 边栏选项卡。
1. 单击“所有策略”。
1. 单击“上传策略”。
1. 上传包含我们添加的 `SignUpOrSignin.xml` 标记的 `ContentDefinitions`。

## <a name="test-the-custom-policy-using-run-now"></a>使用“立即运行”测试自定义策略

1. 打开 **Azure AD B2C 边栏选项卡**并导航到“所有策略”。
1. 选择上传的自定义策略，然后单击“立即运行”按钮。
1. 现在，应该可以使用电子邮件地址注册。

## <a name="next-steps"></a>后续步骤

这篇[有关对内置策略进行 UI 自定义的参考指南](active-directory-b2c-reference-ui-customization.md)包含有关可以自定义哪些 UI 元素的其他信息。  内置策略与自定义策略的 UI 自定义过程没有差别。

