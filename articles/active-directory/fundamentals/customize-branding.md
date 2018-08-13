---
title: 自定义 Azure AD 租户的登录页 | Microsoft Docs
description: 了解如何在 Azure 登录页中添加公司品牌元素
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 07/20/2018
ms.author: lizross
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 45637122af3df1906a8c3b4b16233f6361eecca3
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528320"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>快速入门：在 Azure AD 登录页中添加公司品牌元素
许多公司想要在他们管理的所有网站和服务上应用一致的外观，以免用户感到困惑。 Azure Active Directory (Azure AD) 提供了此功能，允许用户使用自己的公司徽标和自定义的颜色方案来自定义登录页外观。 登录到使用 Azure AD 作为标识提供者的基于 Web 的应用程序（例如 Office 365）时将显示登录页。 用户与此页进行交互以输入其凭据。

> [!NOTE]
> * 只有购买了 Azure AD 的高级或基本版许可证时，或者拥有 Office 365 许可证时，才能使用公司品牌功能。 要了解你的许可证类型是否支持某一功能，请查看 [Azure Active Directory 定价信息页](https://azure.microsoft.com/pricing/details/active-directory/)。
> 
> * 在中国，使用 Azure Active Directory 全球实例的客户可以使用 Azure AD 高级和基本版。 由中国 21Vianet 运营的 Azure 服务目前不支持 Azure AD 高级和基本版。 有关详细信息，请在 [Azure Active Directory 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)上与我们联系。

## <a name="customizing-the-sign-in-page"></a>自定义登录页

<!--You can customize the following elements on the sign-in page: <attach image>-->

当用户访问特定于租户的 URL（例如 [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com)）或者在 URL 中传递了域变量（例如 [*https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com)）时，公司品牌自定义就会显示在 Azure AD 登录页上。

例如，当用户访问 www.office.com 时，登录页不会显示任何公司品牌自定义项，因为用户尚未输入凭据。 在用户输入其用户 ID 或选择一个用户磁贴后，将会显示公司品牌。

> [!NOTE]
> * 用户的域名在已配置品牌元素的 Azure 门户的“域”部分必须显示为“活动”。 有关详细信息，请参阅[添加自定义域名](add-custom-domain.md)。
> * 登录页品牌不会带到个人 Microsoft 帐户的登录页。 如果员工或商业客户使用个人 Microsoft 帐户登录，则其登录页不会反映出组织的品牌。


### <a name="banner-logo"></a>横幅徽标 

Description | 约束 | 建议
------- | ------- | ----------
横幅徽标会显示在登录页和访问面板页上。<br>在登录页上，输入用户名后将会显示徽标。 | 透明 JPG 或 PNG<br>最大高度：36 px<br>最大宽度：245 px | 在此处使用组织的徽标。<br>使用透明图像。 请勿假设背景为白色。<br>请勿在映像中的徽标周围添加填充，否则徽标看起来会很小。

### <a name="username-hint"></a>用户名提示   
Description | 约束 | 建议
------- | ------- | ----------
此选项自定义用户名字段中的提示文本。 | Unicode 文本，最多 64 个字符<br>仅纯文本 | 如果希望组织外的来宾用户可登录到你的应用，建议不要设置此选项。
            
### <a name="sign-in-page-text"></a>登录页文本   
Description | 约束 | 建议
------- | ------- | ----------
此选项显示在登录窗体底部，可用于传达其他信息，例如技术支持的电话号码，或法律声明。 | Unicode 文本，最多 256 个字符<br>仅纯文本（没有链接或 HTML 标记）    

### <a name="sign-in-page-image"></a>登录页图像  
Description | 约束 | 建议
------- | ------- | ----------
此选项显示在登录页的背景中，定位在可视空间的中心，并可通过缩放和裁剪来适应浏览器窗口。    <br>在诸如手机这类窄屏上，将不会显示此图像。<br>当页面加载时，将对此图像应用一个不透明度为 0.55 的黑色蒙板。 | JPG 或 PNG<br>图像尺寸：1920x1080 px<br>文件大小：&lt; 300 KB | <br>在没有鲜明主题的位置处使用图像。 不透明的登录窗体出现在此图像的中心之上，可以覆盖图像的任何部分，具体取决于浏览器窗口的大小。<br>请使该文件保持较小，以确保快速加载。 

### <a name="sign-in-page-background-color"></a>登录页背景色
Description | 约束 | 建议
------- | ------- | ----------
在低带宽连接时，此颜色将代替背景图。 | 十六进制格式的 RGB 颜色（示例：#FFFFFF | 建议使用横幅徽标的主颜色或你的组织颜色。

### <a name="square-logo-image"></a>方形徽标图像
Description | 约束 | 建议
------- | ------- | ----------
在设置新的 Enterprise Windows 10 PC 时会显示此图像。 它在员工设置新的工作 PC 时为他们提供上下文。 将为使用 [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) 来部署其工作设备的租户显示此图像，还会在其他 Windows 10 体验的密码输入页面上显示此图像。<br>方形徽标专用于 Windows 身份验证。 深色主题版本已弃用，不再在任意位置使用。 | 透明 PNG（首选）或 JPG<br>图像尺寸：240 x 240 像素<br>文件大小：&lt; 10 KB | 在此处使用组织的徽标。<br> 使用透明图像。<br>请勿假设背景为白色。<br>请勿向图像中的徽标添加填充，否则徽标看起来会很小。

### <a name="show-option-to-remain-signed-in"></a>显示保持登录状态的选项
Description | 约束 | 建议
------- | ------- | ----------
Azure AD 登录为用户提供了当关闭并重新打开其浏览器时保持登录状态的选项。 此设置会隐藏该选项。<br>设置为“否”将对用户隐藏此选项。 | &nbsp; | 隐藏此选项不会影响会话生存期。<br>SharePoint Online 和 Office 2010 的某些功能取决于用户能否选择保持登录状态。 如果将此选项设置为“否”，用户会在登录时可能看到其他意外的提示。

> [!NOTE]
> 所有元素都是可选的。 例如，如果指定一个没有背景图像的横幅徽标，则登录页将显示你的徽标及目标网站的背景图像（例如 Office 365）。

## <a name="add-company-branding-to-your-directory"></a>将公司品牌元素添加到目录

1. 使用租户的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“Azure Active Directory” > “公司品牌” > “编辑”。
  
  ![打开自定义品牌](./media/customize-branding/navigation-to-branding.png)
3. 修改要自定义的元素。 所有元素都是可选的。
  
  ![编辑自定义品牌元素](./media/customize-branding/edit-branding.png)
4. 完成后，选择“保存”。

最长可能需要一个小时才能显示用户对登录页的品牌元素所做的任何更改。

## <a name="add-language-specific-company-branding-to-your-directory"></a>将特定语言的公司品牌元素添加到目录

1. 使用目录的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“Azure Active Directory” > “公司品牌” > “新语言”。
  
  ![添加特定于语言的品牌元素](./media/customize-branding/add-language.png)
3. 修改要自定义的元素。 所有元素都是可选的。
4. 完成后，选择“保存”。

最长可能需要一个小时才能显示用户对登录页的品牌元素所做的任何更改。

## <a name="next-steps"></a>后续步骤
在本快速入门中，你已了解如何将公司品牌元素添加到 Azure AD 目录。 

可使用以下链接在 Azure 门户中的 Azure AD 中配置公司品牌元素。

> [!div class="nextstepaction"]
> [配置公司品牌](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 