---
title: "自定义 Azure Active Directory 中的登录页 | Microsoft Docs"
description: "了解如何在 Azure 登录页中添加公司品牌元素"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeffgilb
custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: bddf2542eecce8bdeccda6053203bf2c2ba0ffb2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/15/2017

---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>快速入门：在 Azure AD 登录页中添加公司品牌元素
许多公司想要在他们管理的所有网站和服务上应用一致的外观，以免用户感到困惑。 Azure Active Directory (Azure AD) 提供了此功能，允许用户使用自己的公司徽标和自定义的颜色方案来自定义登录页外观。 登录页是登录到 Office 365 或其他使用 Azure AD 作为标识提供者的基于 Web 的应用程序时显示的页面。 用户与此页进行交互以输入其凭据。

> [!NOTE]
> * 只有在升级到 Azure AD 高级或基本版后，或者在拥有 Office 365 许可证后，才可以使用公司品牌功能。 要了解你的许可证类型是否支持某一功能，请查看 [Azure Active Directory 定价信息页](https://azure.microsoft.com/pricing/details/active-directory/)。
> 
> * 在中国，使用 Azure Active Directory 全球实例的客户可以使用 Azure Active Directory 高级和基本版。 由中国 21Vianet 运营的 Microsoft Azure 服务目前不支持 Azure Active Directory 高级和基本版。 有关详细信息，请在 [Azure Active Directory 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。

## <a name="customizing-the-sign-in-page"></a>自定义登录页

<!--You can customize the following elements on the sign-in page: <attach image>-->

当用户访问特定于租户的 URL（例如 [https://outlook.com/contoso.com](https://outlook.com/contoso.com)）时，公司品牌自定义就会显示在 Azure AD 登录页上。

若用户访问泛型 URL（例如 www.office.com），此时登录页还不会包含公司品牌自定义，因为系统不知道用户是谁。 在用户输入其用户 ID 或选择用户磁贴以后，公司品牌就会显示。

> [!NOTE]
> * 用户的域名在已配置品牌元素的 Azure 门户的“域”部分必须显示为“活动”。 有关详细信息，请参阅[添加自定义域名](add-custom-domain.md)。
> * 登录页品牌不会带到个人 Microsoft 帐户的登录页。 如果员工或商业客户使用个人 Microsoft 帐户登录，则其登录页不会反映出组织的品牌。


### <a name="banner-logo"></a>横幅徽标 

说明 | 约束 | 建议
------- | ------- | ----------
横幅徽标会显示在登录页和访问面板页上。<br>在确定用户的组织之后（通常是在输入用户名后），这便将显示在登录页上。  | 透明 JPG 或 PNG<br>最大高度：36 px<br>最大宽度：245 px | 在此处使用组织的徽标。<br>使用透明图像。 请勿假设背景为白色。<br>请勿在映像中的徽标周围添加填充，否则徽标看起来会很小。

### <a name="username-hint"></a>用户名提示   
说明 | 约束 | 建议
------- | ------- | ----------
它自定义用户名字段中的提示文本。 | Unicode 文本，最多 64 个字符<br>仅纯文本 | 如果希望组织外的来宾用户可登录到应用，建议不要进行此设置。
            
### <a name="sign-in-page-text"></a>登录页文本   
说明 | 约束 | 建议
------- | ------- | ----------
它显示在登录窗体底部，可用于传达其他信息，例如技术支持的电话号码，或法律声明。 | Unicode 文本，最多 256 个字符<br>仅纯文本（没有链接或 HTML 标记）   

### <a name="sign-in-page-image"></a>登录页图像  
说明 | 约束 | 建议
------- | ------- | ----------
它显示在登录页的背景中，定位在可视空间的中心，并可通过缩放和裁剪来适应浏览器窗口。    <br>在诸如手机这类窄屏上，将不会显示此图像。<br>当页面加载时，我们的代码将对此图像应用一个不透明度为 0.55 的黑色蒙板。 | JPG 或 PNG<br>图像尺寸：1920x1080 px<br>文件大小：&gt; 300 KB | <br>在没有鲜明主题的位置处使用图像。 不透明的登录窗体出现在此图像的中心之上，可以覆盖图像的任何部分，具体取决于浏览器窗口的大小。<br>尽可能地缩小文件大小，以确保快速加载。 

### <a name="background-color"></a>背景色
说明 | 约束 | 建议
------- | ------- | ----------
在低带宽连接时，此颜色将代替背景图。 | 十六进制格式的 RGB 颜色（示例：#FFFFFF | 建议使用横幅徽标的主颜色或你的组织颜色。

### <a name="show-option-to-remain-signed-in"></a>显示保持登录状态的选项
说明 | 约束 | 建议
------- | ------- | ----------
Azure AD 登录为用户提供当关闭并重新打开其浏览器时保持登录状态的选项。 使用它来隐藏该选项。<br>将其设置为“否”，以对用户隐藏此选项。 | &nbsp; | 这不会影响会话生存期。<br>SharePoint Online 和 Office 2010 的某些功能取决于用户能否选择保持登录状态。 如果将其设置为隐藏，用户可能会在登录时看到其他意外的提示。

> [!NOTE]
> 所有元素都是可选的。 例如，如果指定一个没有背景图像的横幅徽标，则登录页将显示你的徽标及目标网站的背景图像（例如 Office 365）。

## <a name="add-company-branding-to-your-directory"></a>将公司品牌元素添加到目录

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入“用户和组”，并选择 **Enter**。

   ![打开“用户管理”](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. 在“用户和组”边栏选项卡中，选择“公司品牌元素”。
4. 在“用户和组 - 公司品牌元素”边栏选项卡上，选择“编辑”命令。

    ![编辑自定义品牌元素](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. 修改要自定义的元素。 所有元素都是可选的。
6. 单击“保存” 。

最长可能需要一个小时才能显示用户对登录页的品牌元素所做的任何更改。

## <a name="add-language-specific-company-branding-to-your-directory"></a>将特定语言的公司品牌元素添加到目录

1. 使用目录的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 在文本框中选择“用户和组”，然后按 Enter。

   ![打开“用户管理”](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. 在“用户和组”边栏选项卡中，选择“公司品牌元素”。
4. 在“用户和组 - 公司品牌元素”边栏选项卡上，选择“添加语言”命令。

    ![添加特定于语言的品牌元素](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. 修改要自定义的元素。 所有元素都是可选的。
6. 单击“保存” 。

最长可能需要一个小时才能显示用户对登录页的品牌元素所做的任何更改。

## <a name="next-steps"></a>后续步骤
在本快速入门中，你已了解如何将公司品牌元素添加到 Azure AD 目录。 

可使用以下链接在 Azure 门户中的 Azure AD 中配置公司品牌元素。

> [!div class="nextstepaction"]
> [配置公司品牌](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 
