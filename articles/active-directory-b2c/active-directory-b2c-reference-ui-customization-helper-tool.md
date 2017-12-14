---
title: "Azure Active Directory B2C：页面 UI 自定义帮助程序工具 | Microsoft Docs"
description: "一种用于演示 Azure Active Directory B2C 中的页面 UI 自定义功能的帮助程序工具"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mtillman
editor: bryanla
ms.assetid: ae935d52-3520-4a94-b66e-b35bb40e7514
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: swkrish
ms.openlocfilehash: a9ccdea64213d564b271699afe28f5ae6db0a71a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C：用于演示页面用户界面 (UI) 自定义功能的帮助程序工具
本文是 Azure Active Directory (Azure AD) B2C 中[主 UI 自定义文章](active-directory-b2c-reference-ui-customization.md)的一篇配套文章。 以下步骤介绍了如何使用我们提供的示例 HTML 和 CSS 内容来演练页面 UI 自定义功能。

## <a name="get-an-azure-ad-b2c-tenant"></a>获取 Azure AD B2C 租户
如果尚没有 Azure 租户，在可以自定义任何内容之前，需要[获取 Azure AD B2C 租户](active-directory-b2c-get-started.md)。

## <a name="create-a-sign-up-or-sign-in-policy"></a>创建注册或登录策略
我们提供的示例内容可用于自定义[注册或登录策略](active-directory-b2c-reference-policies.md)中的两个页面：[统一登录页](active-directory-b2c-reference-ui-customization.md)和[自断言属性页](active-directory-b2c-reference-ui-customization.md)。 [创建注册或登录策略](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy)时，请添加本地帐户（电子邮件地址）、Facebook、Google 和 Microsoft 作为 **标识提供者**。 我们的示例 HTML 内容只接受这些 IDP。  如果希望，也可以添加这些 IDP 的子集。

## <a name="register-an-application"></a>注册应用程序
需要在 B2C 租户中[注册一个应用程序](active-directory-b2c-app-registration.md)，该应用程序可用于执行策略。 注册应用程序后，有几个选项可用来实际执行注册策略：

* 构建在[在应用程序中注册并登录使用者](active-directory-b2c-overview.md#get-started)的“入门”部分中列出的 Azure AD B2C 快速入门应用程序之一。
* 使用预建的 [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) 应用程序。 如果选择使用 Playground，则必须使用“重定向 URI”`https://aadb2cplayground.azurewebsites.net/`在 B2C 租户中注册应用程序。
* 在 [Azure 门户](https://portal.azure.com/)中使用策略上的“立即运行”按钮。

## <a name="customize-your-policy"></a>自定义策略
若要自定义策略的外观风格，需要先使用 Azure AD B2C 的特定约定创建 HTML 和 CSS 文件。 然后可以将静态内容上传到公共可用位置，以便 Azure AD B2C 可以对其进行访问。 这可以是自己专用的 Web 服务器、Azure Blob 存储、Azure 内容交付网络或任何其他静态资源托管提供程序。 唯一的要求是内容必须可通过 HTTPS 使用，并且可使用 CORS 访问。 在 Web 上公开静态内容后，可以编辑策略指向此位置，向客户显示该内容。 [主 UI 自定义文章](active-directory-b2c-reference-ui-customization.md)详细介绍了 Azure AD B2C 自定义功能的工作原理。

出于本教程的目的，我们已经创建了一些示例内容并将其托管在 Azure Blob 存储上。 示例内容是我们的虚拟公司“Wingtip Toys”主题下的一个非常基本的自定义。 若要在自己的策略中进行尝试，请按照以下步骤操作：

1. 在 [Azure 门户](https://portal.azure.com/)上登录租户，并导航到 B2C 功能边栏选项卡。
2. 单击“注册或登录策略”，然后单击策略（例如，“b2c\_1\_sign\_up\_sign\_in”）。
3. 单击“页面 UI 自定义”，并单击“统一注册或登录页”。
4. 将“使用自定义页面”切换为“是”。 在“自定义页面 URI”字段中，输入 `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`。 单击 **“确定”**。
5. 单击“本地帐户注册页”。 将“使用自定义模板”切换为“是”。 在“自定义页面 URI”字段中，输入 `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`。
6. 对“社交帐户注册页”重复同样的步骤。
   单击“确定”两次，关闭 UI 自定义边栏选项卡。
7. 单击“保存” 。

现在，可以尝试使用所自定义策略了。 如果希望，可以使用自己的应用程序或 Azure AD B2C Playground，也可以直接单击策略边栏选项卡中的“立即运行”命令。 在下拉框中选择应用程序，并选择适当的重定向 URI。 单击“立即运行”按钮。 将打开一个新的浏览器选项卡，可以通过已有的新内容体验用户注册应用程序的过程！

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>将示例内容上传到 Azure Blob 存储
如果想要使用 Azure Blob 存储托管页面内容，可以创建自己的存储帐户，并使用我们的 B2C 帮助程序工具上传文件。

### <a name="create-a-storage-account"></a>创建存储帐户
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“+ 新建” > “数据 + 存储设备” > “存储帐户”。 需要有一个 Azure 订阅来创建 Azure Blob 存储帐户。 可以在 [Azure 网站](https://azure.microsoft.com/pricing/free-trial/)注册免费试用版。
3. 为存储帐户提供“名称”（例如“contoso”），并为“定价层”、“资源组”和“订阅”选择适当的选项。 确保已选中“固定到启动板”选项。 单击“创建” 。
4. 返回到启动板，并单击刚刚创建的存储帐户。
5. 在“摘要”部分中，单击“容器”，并单击“+ 添加”。
6. 为容器提供“名称”（例如“b2c”），并选择“Blob”作为“访问类型”。 单击 **“确定”**。
7. 创建的容器会显示在“Blob”边栏选项卡上的列表中。 记下容器的 URL；例如，它应该看起来类似于 `https://contoso.blob.core.windows.net/b2c`。 关闭“Blob”边栏选项卡。
8. 在存储帐户边栏选项卡上，单击“密钥”，并记下“存储帐户名称”和“主访问密钥”字段的值。

> [!NOTE]
> “主访问密钥”是一个非常重要的安全凭据。
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>下载帮助程序工具和示例文件
可以下载 [zip 文件格式的 Azure Blob 存储帮助程序工具和示例文件](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)或从 GitHub 进行克隆：

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

此存储库包含 `sample_templates\wingtip` 目录，其中包含示例 HTML、CSS 和映像。 若要使这些模板引用自己的 Azure Blob 存储帐户，需要编辑 HTML 文件。 打开 `unified.html` 和 `selfasserted.html`，并使用自己容器的 URL（之前步骤中记录的 URL）替换 `https://localhost` 的所有实例。 必须使用 HTML 文件的绝对路径，因为在此情况下，HTML 由 Azure AD 在 `https://login.microsoftonline.com` 域下提供。

### <a name="upload-the-sample-files"></a>上传示例文件
在同一存储库中，解压缩 `B2CAzureStorageClient.zip` 并在其中运行 `B2CAzureStorageClient.exe` 文件。 此程序会直接将指定目录中的所有文件上传到存储帐户，并启用对这些文件的 CORS 访问。 如果按照上述步骤操作，则 HTML 和 CSS 文件现在将指向存储帐户。 请注意，存储帐户名称是 `blob.core.windows.net` 之前的部分；例如 `contoso`。 通过在浏览器上访问 `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html`，可以验证是否已正确上传内容。 还可以使用 [ http://test-cors.org/](http://test-cors.org/) 确保内容是否现已启用 CORS。 （在结果中查找“XHR 状态: 200”。）

### <a name="customize-your-policy-again"></a>再次自定义策略
现在，已将示例内容上传到自己的存储帐户，必须编辑注册策略以对其进行引用。 重复上述[“自定义策略”](#customize-your-policy)部分中的步骤，但这次使用自己的存储帐户 URL。 例如，`unified.html` 文件的位置为 `<url-of-your-container>/wingtip/unified.html`。

现在，可以使用“立即运行”按钮或自己的应用程序再次执行策略。 两种情况下使用了相同的示例 HTML 和 CSS，因此结果应该看起应几乎完全相同。 但是，策略现在引用的是自己的 Azure Blob 存储实例，且可以再次自由编辑和上传文件。 有关自定义 HTML 和 CSS 的详细信息，请参阅[主 UI 自定义文章](active-directory-b2c-reference-ui-customization.md)。

