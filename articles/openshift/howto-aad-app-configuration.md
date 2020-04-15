---
title: Azure 红帽开放移位的 Azure 活动目录集成
description: 了解如何创建 Azure AD 安全组和用户以在 Microsoft Azure 红帽 OpenShift 群集上测试应用。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f6c4fb5caf746650f95872d50afe31e5693422be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382917"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure 红帽开放移位的 Azure 活动目录集成

如果尚未创建 Azure 活动目录 （Azure AD） 租户，请按照[Azure 红帽 OpenShift 创建 Azure AD 租户](howto-create-tenant.md)的说明操作，然后再继续这些说明。

Microsoft Azure 红帽 OpenShift 需要代表群集执行任务的权限。 如果您的组织还没有 Azure AD 用户、Azure AD 安全组或 Azure AD 应用注册用作服务主体，请按照这些说明创建它们。

## <a name="create-a-new-azure-active-directory-user"></a>创建新的 Azure Active Directory 用户

在[Azure 门户](https://portal.azure.com)中，确保租户显示在门户右上角的用户名下：

![右上角列出租户的门户屏幕截图](./media/howto-create-tenant/tenant-callout.png)如果显示了错误的租户，请单击右上角的用户名，然后单击 **"切换目录**"，然后从 **"所有目录"** 列表中选择正确的租户。

创建新的 Azure 活动目录"所有者"用户以登录到 Azure 红帽 OpenShift 群集。

1. 转到["用户-所有用户"](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)边栏选项卡。
2. 单击 **"新建用户**"以打开 **"用户"** 窗格。
3. 输入此用户**的名称**。
4. 基于您创建的租户的名称创建**用户名**，并在末尾`.onmicrosoft.com`追加。 例如，`yourUserName@yourTenantName.onmicrosoft.com` 。 记下此用户名。 您需要它才能登录到群集。
5. 单击 **"目录"角色**以打开目录角色窗格，然后选择 **"所有者"，** 然后单击窗格底部的 **"确定**"。
6. 在 **"用户"** 窗格中，单击"**显示密码"** 并记录临时密码。 首次登录后，系统将提示您重置它。
7. 在窗格的底部，单击"**创建**"以创建用户。

## <a name="create-an-azure-ad-security-group"></a>创建 Azure AD 安全组

要授予群集管理员访问权限，Azure AD 安全组中的成员身份将同步到 OpenShift 组"osa-客户管理员"。 如果未指定，将不会授予群集管理员访问权限。

1. 打开[Azure 活动目录组边](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)栏选项卡。
2. 单击 **"新建组**"。
3. 提供组名称和说明。
4. 将**组类型**设置为 **"安全**"。
5. 将**成员资格类型**设置为**已分配**。

    将在前面的步骤中创建的 Azure AD 用户添加到此安全组。

6. 单击 **"成员**"以打开 **"选择成员**"窗格。
7. 在成员列表中，选择上面创建的 Azure AD 用户。
8. 在门户底部，单击"**选择"** 然后**创建**以创建安全组。

    记下组 ID 值。

9. 创建组时，您将在所有组列表中看到它。 单击新组。
10. 在显示的页面上，向下复制对象**ID**。 我们将引用此值，如`GROUPID`创建 Azure[红帽 OpenShift 群集](tutorial-create-cluster.md)教程。

> [!IMPORTANT]
> 要将此组与 osa-客户管理员 OpenShift 组同步，请使用 Azure CLI 创建群集。 Azure 门户当前缺少一个字段来设置此组。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

通过将标志省略到命令，`--aad-client-app-id`可以自动创建 Azure 活动目录 （Azure AD） 应用注册客户端，作为创建群集`az openshift create`的一部分。 本教程演示如何创建 Azure AD 应用注册以获得完整性。

如果您的组织尚未注册 Azure 活动目录 （Azure AD） 应用注册用作服务主体，请按照这些说明创建一个。

1. 打开[应用注册边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)，然后单击 **"新建注册**"。
2. 在 **"注册应用程序**"窗格中，输入应用程序注册的名称。
3. 确保根据 **"支持"科目类型**，**仅选中此组织目录中的帐户**。 这是最安全的选择。
4. 稍后，我们将在知道群集 URI 后添加重定向 URI。 单击 **"注册"** 按钮可创建 Azure AD 应用程序注册。
5. 在显示的页面上，向下复制**应用程序（客户端）ID**。 我们将引用此值，如`APPID`创建 Azure[红帽 OpenShift 群集](tutorial-create-cluster.md)教程。

![应用对象页面的屏幕截图](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>创建客户端机密

生成客户端密钥，用于将应用验证为 Azure 活动目录。

1. 在应用注册页的 **"管理**"部分中，单击 **"证书&机密**"。
2. 在 **"证书&机密**"窗格中，单击 **"新客户端机密**"。  将显示 **"添加客户端机密**"窗格。
3. 提供**说明**。
4. 设置为**您**喜欢的持续时间，例如 **，在 2 年内**。
5. 单击"**添加**"，键值将显示在页面的 **"客户端机密**"部分中。
6. 向下复制键值。 我们将引用此值，如`SECRET`创建 Azure[红帽 OpenShift 群集](tutorial-create-cluster.md)教程。

![证书和机密窗格的屏幕截图](./media/howto-create-tenant/create-key.png)

有关 Azure 应用程序对象的详细信息，请参阅[Azure 活动目录中的应用程序和服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

有关创建新 Azure AD 应用程序的详细信息，请参阅[使用 Azure 活动目录 v1.0 终结点注册应用](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)。

## <a name="add-api-permissions"></a>添加 API 权限

[//]: # (不要更改为微软图形。它不适用于微软图形。)
1. 在 **"管理**"部分中单击**API 权限**
2. 单击 **"添加权限**"并选择**Azure 活动目录图**，然后**委派权限**。
> [!NOTE]
> 请确保选择了"Azure 活动目录图"，而不是"Microsoft 图形"磁贴。

3. 展开下面的列表中**的用户**并启用**用户.阅读**权限。 如果默认情况下启用**了 User.Read，** 请确保它是 Azure**活动目录图形**权限 **"用户"。**
4. 向上滚动并选择**应用程序权限**。
5. 展开下面列表中的**目录**并启用**目录。阅读全部**。
6. 单击"**添加权限**"以接受更改。
7. API 权限面板现在应同时显示*User.Read*和*目录。ReadAll*。 请注意*目录*旁边的 **"管理员同意"** 列中的警告。
8. 如果您是 Azure*订阅管理员*，请单击下面的 **"授予管理员同意*订阅名称***"。 如果您不是 Azure*订阅管理员*，请请求管理员的同意。

![API 权限面板的屏幕截图。 用户.阅读和目录.阅读添加的所有权限，管理员同意目录.阅读所有](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 群集管理员组的同步仅在获得同意后才起作用。 在 *"管理员同意"要求*列中，您将看到一个绿色圆圈，其中带有复选标记和消息"订阅*名称*授予"。

有关管理管理员和其他角色的详细信息，请参阅[添加或更改 Azure 订阅管理员](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)。

## <a name="resources"></a>资源

* [Azure 活动目录中的应用程序和服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [快速入门：向 Azure Active Directory v1.0 终结点注册应用](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>后续步骤

如果您满足所有[Azure 红帽 OpenShift 先决条件](howto-setup-environment.md)，则可以创建第一个群集！

请尝试本教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)
