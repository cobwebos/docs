---
title: 部署管理工具 - Azure
description: 如何安装用户界面工具管理 Windows 虚拟桌面资源。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: ad0c67cea6a5a9b487cd47aa7c10d10da1438050
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384286"
---
# <a name="tutorial-deploy-a-management-tool"></a>教程：部署管理工具

管理工具提供一个用户界面 (UI) 用于管理 Microsoft 虚拟桌面资源。 本教程介绍如何部署和连接管理工具。

>[!NOTE]
>这些说明适用于可与组织的现有过程配合使用的特定于 Windows 虚拟桌面的配置。

## <a name="important-considerations"></a>重要注意事项

由于应用需要获得许可才能与 Windows 虚拟桌面交互，因此，此工具不支持企业到企业 (B2B) 方案。 每个 Azure Active Directory (AAD) 租户的订阅单独需要自身的管理工具部署。

此管理工具只是一个示例。 Microsoft 将提供重要的安全更新和质量更新。 [GitHub 上提供了源代码](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)。 我们建议客户与合作伙伴根据自身的业务需求自定义该工具。

以下浏览器与管理工具兼容：
- Google Chrome 68 或更高版本
- Microsoft Edge 40.15063 或更高版本
- Mozilla Firefox 52.0 或更高版本
- Safari 10 或更高版本（仅限 macOS）

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>运行 Azure 资源管理器模板所要做好的准备

在部署 Azure 资源管理器模板之前，需要以 Azure Active Directory 用户的身份部署管理 UI。 此用户必须：

- 已禁用 Azure 多重身份验证 (MFA)
- 有权在 Azure 订阅中创建资源
- 有权创建 Azure AD 应用程序 请遵循这些步骤检查该用户是否拥有[所需的权限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

部署 Azure 资源管理器模板后，建议启动管理 UI 进行验证。 此用户必须：
- 拥有一个可以查看或编辑你的 Windows 虚拟桌面租户的角色分配

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>运行 Azure 资源管理器模板来预配管理 UI

在开始之前，请访问相应 Azure Active Directory (AAD) 的 [Windows 虚拟桌面许可页](https://rdweb.wvd.microsoft.com)，确保服务器和客户端应用已获得许可。

遵照以下说明部署 Azure 资源管理器模板：

1. 转到 [GitHub Azure RDS 模板页](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)。
2. 将模板部署到 Azure。
    - 如果在“企业”订阅中部署，请向下滚动并选择“部署到 Azure”。  请参阅[有关模板参数的指导](#guidance-for-template-parameters)。
    - 如果在“云解决方案提供商”订阅中部署，请遵照以下说明部署到 Azure：
        1. 向下滚动并右键单击“部署到 Azure”，然后选择“复制链接位置”。  
        2. 打开一个文本编辑器（例如记事本）并在其中粘贴该链接。
        3. 紧接在 <https://portal.azure.com/> 之后、井号 (#) 之前，输入 @ 符号，后接租户域名。 下面格式示例：<https://portal.azure.com/@Contoso.onmicrosoft.com#create/>。
        4. 以对“云解决方案提供商”订阅拥有“管理员/参与者”权限的用户身份登录到 Azure 门户。
        5. 将已复制到文本编辑器的链接粘贴到地址栏中。

### <a name="guidance-for-template-parameters"></a>有关模板参数的指导
下面说明了如何输入用于配置工具的参数：

- 对于 isServicePrincipal 参数，选择 false   。
- 对于凭据，输入已禁用多重身份验证的 Azure Active Directory 凭据。 这些凭据将用于登录到 Azure 并创建 Azure AD 应用程序和 Azure Web 应用资源。 若要了解详细信息，请参阅[运行 Azure 资源管理器模板所要做好的准备](#what-you-need-to-run-the-azure-resource-manager-template)。
- 对于 applicationName，为要在 Azure Active Directory 中注册的应用使用唯一的名称  。 此名称还将用于 Web 应用 URL。 例如，可以使用类似于“Apr3UX”的名称。

## <a name="provide-consent-for-the-management-tool"></a>为管理工具提供许可

GitHub Azure 资源管理器模板完成后，你会在 Azure 门户中看到一个资源组，其中包含两个应用服务以及一个应用服务计划。

在登录和使用管理工具之前，需要为管理工具关联的新 Azure Active Directory 应用程序提供许可。 提供许可即表示允许管理工具代表已登录到该工具的用户发出 Windows 虚拟桌面管理调用。

![该屏幕截图显示许可 UI 管理工具时提供的权限。](media/management-ui-delegated-permissions.png)

若要确定可以使用哪个用户登录到该工具，请转到 [Azure Active Directory 用户设置页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)，并记下“用户可以许可应用代表他们访问公司数据”的值。 

![该屏幕截图显示用户是否可以许可应用程序代表他们访问数据。](media/management-ui-user-consent-allowed.png)

- 如果该值设置为“是”，则你可以使用 Azure Active Directory 中的任何用户帐户登录，并只能为该用户提供许可。  但是，如果以后使用其他用户登录到管理工具，则必须再次执行相同的许可。
- 如果该值设置为“否”，则必须以 Azure Active Directory 中的全局管理员身份登录，并针对目录中的所有用户提供管理员许可。  所有其他用户不会看到许可提示。


决定要使用哪个用户提供许可后，请遵照以下说明为工具提供许可：

1. 转到你的 Azure 资源，选择具有模板中提供的名称（例如 Apr3UX）的 Azure 应用服务资源，然后导航到与它关联的 URL，例如 <https://rdmimgmtweb-210520190304.azurewebsites.net>。
2. 使用相应的 Azure Active Directory 用户帐户登录。
3. 如果已使用全局管理员帐户进行身份验证，则现在可以选中“代表组织提供许可”复选框。  选择“接受”以提供许可。 
   
   ![该屏幕截图显示用户或管理员将看到的整个许可页。](media/management-ui-consent-page.png)

随后你会转到管理工具。

## <a name="use-the-management-tool"></a>使用管理工具

为组织或指定的用户提供许可后，随时可以访问管理工具。

请遵照以下说明启动该工具：

1. 选择具有模板中提供的名称（例如 Apr3UX）的 Azure 应用服务资源，然后导航到与它关联的 URL，例如 <https://rdmimgmtweb-210520190304.azurewebsites.net>。
2. 使用 Windows 虚拟桌面凭据登录。
3. 当系统提示你选择租户组时，请从下拉列表中选择“默认租户组”。 
4. 选择“默认租户组”时，将在窗口右侧显示一个菜单。 在此菜单上，找到租户组的名称并将其选中。

> [!NOTE]
> 如果你有自定义的租户组，请手动输入名称，而不要从下拉列表中选择。

## <a name="report-issues"></a>报告问题

如果在管理工具或其他 Windows 虚拟桌面工具中遇到任何问题，请按照[远程桌面服务的 ARM 模板](https://github.com/Azure/RDS-Templates/blob/master/README.md)中的说明在 GitHub 上报告这些问题。

## <a name="next-steps"></a>后续步骤

了解如何部署和连接管理工具后，接下来可以了解如何使用 Azure 服务运行状况来监视服务问题和获得运行状况方面的建议。

> [!div class="nextstepaction"]
> [设置服务警报教程](./set-up-service-alerts.md)
