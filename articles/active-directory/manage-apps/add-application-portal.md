---
title: 向 Azure Active Directory 租户添加应用 | Microsoft Docs
description: 本快速入门使用 Azure 门户向 your Azure Active Directory (Azure AD) 租户添加库应用程序。
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/24/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: af30568d3ab35b35d2a0e0bef00921debb1ea451
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917798"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>快速入门：向 Azure Active Directory 租户添加应用程序

Azure Active Directory (Azure AD) 有一个库，其中包含数千预集成的应用程序。 组织所用的某些应用程序可能在该库中。 本快速入门使用 Azure 门户向 your Azure Active Directory (Azure AD) 租户添加库应用程序。

将某个应用程序添加到 Azure AD 租户以后，即可：

- 使用条件访问策略管理用户对应用程序的访问。
- 将用户配置为使用其 Azure AD 帐户通过单一登录方式登录到应用程序。

## <a name="before-you-begin"></a>开始之前

若要向租户添加应用程序，需具备：

- Azure AD 订阅
- 适合应用程序且启用了单一登录的订阅

以 Azure AD 租户全局管理员、云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

若要测试本教程中的步骤，建议使用非生产环境。 如果没有 Azure AD 非生产环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="add-an-application-to-your-azure-ad-tenant"></a>向 Azure AD 租户添加应用程序

若要向 Azure AD 租户添加库应用程序，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory”。

2. 在“Azure Active Directory”窗格中，选择“企业应用程序”。

    ![打开企业应用程序](media/add-application-portal/open-enterprise-apps.png)

3. 此时会打开“所有应用程序”窗格，其中显示了 Azure AD 租户中应用程序的随机示例。 在“所有应用程序”窗格顶部，选择“新建应用程序”。

    ![新建应用程序](media/add-application-portal/new-application.png)

4. 在“类别”窗格中的“特色应用程序”区域下面会显示一些图标，表示库应用程序的随机示例。  若要查看更多应用程序，可以选择“显示更多内容”。 但是，我们建议不要以这种方式进行搜索，因为库中有数千应用程序。

    ![按名称或类别搜索](media/add-application-portal/categories.png)

5. 若要搜索应用程序，请在“从库中添加”下输入要添加的应用程序的名称。 从结果中选择应用程序，然后选择“添加”。 以下示例演示在搜索 github.com 后显示的“添加应用”窗体。

    ![添加应用程序](media/add-application-portal/add-an-application.png)

6. 在特定于应用程序的窗体中，可以更改属性信息。 例如，可以根据组织需要编辑应用程序的名称。 此示例使用 **GitHub-test** 作为名称。

7. 完成对属性的更改后，选择“添加”。

8. 此时会显示一个入门页面，其中包含为组织配置应用程序所需的选项。

现已添加完应用程序。 请稍作休息。 后续部分介绍如何更改徽标和编辑应用程序的其他属性。

## <a name="find-your-azure-ad-tenant-application"></a>查找 Azure AD 租户应用程序

假设你此前因故必须离开，现在回来继续配置应用程序。 首先找到应用程序。

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择“Azure Active Directory”。

2. 在“Azure Active Directory”窗格中，选择“企业应用程序”。

3. 在“应用程序类型”下拉菜单中，选择“所有应用程序”，然后选择“应用”。 若要详细了解查看选项，请参阅[查看租户应用程序](view-applications-portal.md)。

4. 此时可以看到一个列表，其中包含 Azure AD 租户中的所有应用程序。 此列表为随机示例。 若要查看更多应用程序，请选择“显示更多内容”一次或多次。

5. 若要快速查找租户中的应用程序，请在搜索框中输入应用程序名称，然后选择“应用”。 此示例查找前面添加的 GitHub-test 应用程序。

    ![搜索应用程序](media/add-application-portal/find-application.png)


## <a name="configure-user-sign-in-properties"></a>配置用户登录属性

找到应用程序后，可将其打开并配置其属性。

编辑应用程序属性：

1. 选择应用程序将其打开。
2. 选择“属性”，以打开属性页进行编辑。

    ![“编辑属性”窗格](media/add-application-portal/edit-properties.png)

3. 花点时间了解登录选项。 这些选项确定在应用程序中分配或取消分配的用户如何登录到应用程序。 这些选项还确定用户能否在访问面板中看到该应用程序。

    - “启用以供用户登录”决定了分配给应用程序的用户能否登录。
    - “需要进行用户分配”决定了未分配给应用程序的用户能否登录。
    - “对用户可见”决定了分配给应用的用户能否在访问面板和 O365 启动器中看到它。

4. 参考下表选择最符合需求的选项。

   - **已分配**用户的行为：

       | 应用程序属性设置 | | | 已分配用户的体验 | |
       |---|---|---|---|---|
       | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 已分配用户能否登录? | 已分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 是 | 是  |
       | 是 | 是 | 否  | 是 | 否   |
       | 是 | 否  | 是 | 是 | 是  |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

   - **未分配**用户的行为：

       | 应用程序属性设置 | | | 未分配用户的体验 | |
       |---|---|---|---|---|
       | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 未分配用户能否登录? | 未分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 否  | 否   |
       | 是 | 是 | 否  | 否  | 否   |
       | 是 | 否  | 是 | 是 | 否   |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

     *用户能否在访问面板和 Office 365 应用启动器中看到应用程序?

## <a name="use-a-custom-logo"></a>使用自定义徽标

若要使用自定义徽标，请执行以下操作：

1. 创建一个 215 x 215 像素的徽标，将其保存为 PNG 格式。
2. 既然已找到该应用程序，请将其选中。
2. 在左窗格中选择“属性”。
4. 上传该徽标。
5. 完成后，选择“保存”。

    ![更改徽标](media/add-application-portal/change-logo.png)


## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何将库应用程序添加到 Azure AD 租户， 以及如何编辑应用程序的属性。

现在，可以配置进行单一登录的应用程序了。

> [!div class="nextstepaction"]
> [配置单一登录](configure-single-sign-on-portal.md)


