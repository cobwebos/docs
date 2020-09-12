---
title: 将品牌添加到组织的登录页面-Azure AD
description: 有关如何将组织的品牌添加到 Azure Active Directory 登录页面的说明。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04e1d35ab17a49dd1c4e9bd2bd19289de2b8658a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565848"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>将品牌添加到组织的 Azure Active Directory 登录页面
使用组织的徽标和自定义颜色方案，在 Azure Active Directory (Azure AD) 登录页面上提供一致外观和体验。 当用户登录到组织的基于 web 的应用（如 Microsoft 365）时，将显示登录页，该应用使用 Azure AD 作为标识提供者。

>[!NOTE]
>添加自定义品牌要求使用 Azure Active Directory Premium 1、Premium 2 或 Basic 版本，或者使用 Microsoft 365 许可证。 有关许可和版本的详细信息，请参阅[注册 Azure AD Premium](active-directory-get-started-premium.md)。<br><br>在中国，使用 Azure Active Directory 全球实例的客户可以使用 Azure AD 高级和基本版。 中国区 21Vianet 运营的 Azure 服务目前不支持 Azure AD Premium 和 Azure AD Basic 版本。 有关详细信息，请通过 [Azure Active Directory 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。

## <a name="customize-your-azure-ad-sign-in-page"></a>自定义 Azure AD 登录页面
可以自定义 Azure AD 登录页面，这些页面会在用户登录到组织的特定于租户的应用时显示（例如 `https://outlook.com/contoso.com`），或者在传递域变量时显示（例如 `https://passwordreset.microsoftonline.com/?whr=contoso.com`）。

用户访问 www\.office.com 等站点时，自定义品牌不会立即显示。 相反，用户必须先登录才会显示自定义品牌。 用户登录后，品牌可能需要 15 分钟或更长时间才能出现。 

> [!NOTE]
> 所有品牌元素都是可选的。 例如，如果您指定一个没有背景图像的横幅徽标，则登录页将使用目标站点中的默认背景图像显示您的徽标 (例如 Microsoft 365) 。<br><br>此外，登录页品牌不会传输到 Microsoft 个人帐户。 如果用户或企业访客使用个人 Microsoft 帐户登录，则其登录页面不会显示组织的品牌。

### <a name="to-customize-your-branding"></a>自定义品牌
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“Azure Active Directory”，然后依次选择“公司品牌”、“配置”************。

    ![Contoso - 公司品牌页面，突出显示了“配置”选项](media/customize-branding/company-branding-configure-button.png)

3. 在“配置公司品牌”页面上，提供以下任意或全部信息****。

    >[!IMPORTANT]
    >在此页面上添加的所有自定义图像都具有图像尺寸（像素）限制，可能有文件大小 (KB) 限制。 由于这些限制，很可能需要使用照片编辑器来创建合适尺寸的图像。

    - **常规设置**

        ![配置公司品牌页面，其中常规设置已完成](media/customize-branding/configure-company-branding-general-settings.png)

        - **语言.** 语言自动设置为默认语言，无法更改。
        
        - **登录页面背景图像。** 选择要显示为登录页面背景的 .png 或 .jpg 图像文件。 此图像将锚定到浏览器的中心，并将缩放到可查看空间的大小。 不能选择大于1920x1080 像素大小或文件大小超过 300 KB 的图像。
        
            建议在没有强主题焦点的情况下使用图像，例如，屏幕中心出现一个不透明的白色框，并且可能会根据可查看空间的尺寸覆盖图像的任何部分。

        - **横幅徽标。** 用户在“我的应用”门户页面上输入用户名后，选择要显示在登录页面上的徽标的 .png 或 .jpg 版本****。
            
            图像不能高于 60 像素或宽于 280 像素。 我们建议使用透明图像，因为背景可能与徽标背景不匹配。 我们还建议不要在图像周围添加填充，否则可能会使徽标看起来很小。

        - **用户名提示。** 键入提示文本，如果用户忘记用户名将会显示此提示文本。 此文本必须是 Unicode，不带链接或代码，且不能超过 64 个字符。 如果访客登录到应用，我们建议不添加此提示。

        - **登录页文本和格式设置。** 键入显示在登录页面底部的文本。 可使用此文本传达其他信息，例如技术支持的电话号码或法律声明。 此文本必须是 Unicode 且不能超过1024个字符。

           你可以自定义输入的登录页文本。 若要开始新段落，请使用 enter 键两次。 还可以更改文本格式，使其包含粗体、斜体、下划线或可单击的链接。 使用以下语法将格式添加到文本： 

          > Hyperlink ```[text](link)``` 
          
          > 粗体： ``` **text** ``` 或 ``` __text__ ``` 
          
          > 斜体： ``` *text* ``` 或 ``` _text_ ``` 
          
          > 划线 ``` ++text++ ``` 

    - **高级设置**
            
        ![配置公司品牌页面，其中高级设置已完成](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **登录页背景色。** 指定在低带宽连接的情况下替代背景图像显示的十六进制颜色（例如，白色为 #FFFFFF）。 我们建议使用横幅徽标的主颜色或组织的颜色。

        - **方形徽标图像。** 选择要在新的 Windows 10 企业版设备的设置过程中向用户显示的 png (首选) 或 .jpg 图像。 此图像仅用于 Windows 身份验证，并且仅显示在使用 [Windows Autopilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) 进行部署的租户上；或者在其他 Windows 10 体验中用于密码输入页面。 在某些情况下，它也可能出现在“同意”对话框中。
        
            图像的大小不能大于240x240 像素像素，并且文件的大小必须小于 10 KB。 我们建议使用透明图像，因为背景可能与徽标背景不匹配。 我们还建议不要在图像周围添加填充，否则可能会使徽标看起来很小。
    
        - **方形徽标，深色主题。** 与上面的方形徽标图像相同。 当在深色背景下使用（例如在开箱即用体验 (OOBE) 中使用已加入 Windows 10 Azure AD 的屏幕）时，此徽标图像取代方形徽标图像。  如果徽标在白色、深蓝和黑色背景上看起来很好，则不需要添加此图像。 
        
        - **显示保持登录状态的选项。** 你可以选择让用户保持登录到 Azure AD，直到显式注销。如果选择 " **否**"，则隐藏此选项，用户必须在每次关闭并重新打开浏览器时登录。

            此功能只能用于默认署名对象，而不能用于任何特定于语言的对象。 若要详细了解如何配置选项并对其进行故障排除，请参阅 [为 Azure AD 帐户配置 "保持登录状态？" 提示](keep-me-signed-in.md)
        
            >[!NOTE]
            >SharePoint Online 和 Office 2010 的某些功能取决于用户能否选择保持登录状态。 如果将此选项设置为“否”，用户会在登录时可能看到其他意外的提示。****
   

3. 已完成添加品牌后，选择“保存”****。

    如果此过程创建了首个自定义品牌配置，则它将成为租户的默认配置。 如果有其他配置，你可以选择默认配置。
    
    >[!IMPORTANT]
    >要向租户添加更多公司品牌配置，你必须在“Contoso - 公司品牌”页面上选择“新语言”********。 这将打开“配置公司品牌”页面，你可以按照上述相同步骤进行操作****。

## <a name="update-your-custom-branding"></a>更新自定义品牌
创建自定义品牌后，可以返回并更改任何所需内容。

### <a name="to-edit-your-custom-branding"></a>编辑自定义品牌
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“Azure Active Directory”，然后依次选择“公司品牌”、“配置”************。

    ![“Contoso - 公司品牌”页面，其中显示默认配置](media/customize-branding/company-branding-default-config.png)

3. 在“配置公司品牌”页面上，根据本文的[自定义 Azure AD 登录页面](#customize-your-azure-ad-sign-in-page)部分中的说明添加、删除或更改任何信息****。

4. 选择“保存”。

   最长可能需要一个小时才能显示用户对登录页的品牌元素所做的任何更改。

## <a name="add-language-specific-company-branding-to-your-directory"></a>将特定语言的公司品牌元素添加到目录
无法将更改原始配置的语言从默认语言更改为其他语言。 但是，如果需要其他语言的配置，则可以创建新的配置。

### <a name="to-add-a-language-specific-branding-configuration"></a>添加特定于语言的品牌配置

1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“Azure Active Directory”，然后依次选择“公司品牌”、“新语言”************。

    ![“Contoso - 公司品牌”页面，突出显示了“新语言”选项](media/customize-branding/company-branding-new-language.png)

3. 在“配置公司品牌”页面上，选择语言（例如法语），然后根据本文的[自定义 Azure AD 登录页面](#customize-your-azure-ad-sign-in-page)部分中的说明添加已翻译的信息****。

4. 选择“保存”。

    “Contoso - 公司品牌”页面更新以显示新的法语配置****。

    ![Contoso-公司品牌页面，其中显示了新语言配置](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>将自定义品牌添加到页面
通过使用文本 `?whr=yourdomainname` 修改 URL 的结尾，将自定义品牌添加到页面。 此修改适用于多个页面，包括多重身份验证 (MFA) 设置页面、自助服务密码重置 (SSPR) 设置页面和登录页面。

**示例：**

**原始 URL：** https://aka.ms/MFASetup<br>
**自定义 URL：**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**原始 URL：** https://aka.ms/SSPR<br>
**自定义 URL：**`https://passwordreset.microsoftonline.com/?whr=contoso.com`