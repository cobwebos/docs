---
title: 为 Azure Red Hat OpenShift 的 azure Active Directory 集成 |Microsoft Docs
description: 了解如何创建用于测试 Microsoft Azure Red Hat OpenShift 群集上的应用程序的 Azure AD 安全组和用户。
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: 00609905d09f8d414660c21805c6efca5eb30843
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669389"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>为 Azure Red Hat OpenShift 的 azure Active Directory 集成

如果你尚未创建 Azure Active Directory (Azure AD) 租户，请按照中的说明操作[创建 Azure AD 租户的 Azure Red Hat OpenShift](howto-create-tenant.md)按照说明继续操作之前。

Microsoft Azure Red Hat OpenShift 需要有权代表你的群集执行任务。 如果你的组织还没有 Azure AD 用户、 Azure AD 安全组或 Azure AD 应用注册，以用作服务主体，请按照这些说明来创建它们。

## <a name="create-a-new-azure-active-directory-user"></a>创建新的 Azure Active Directory 用户

在[Azure 门户](https://portal.azure.com)，确保在你的用户名称将显示你的租户门户的权限：

![右上角中列出的与租户的门户屏幕截图](./media/howto-create-tenant/tenant-callout.png)如果显示了错误的租户，请单击右上方，您的用户名称，然后单击**切换目录**，然后选择从正确的租户**所有目录**列表。

创建一个新的 Azure Active Directory 全局管理员用户登录到 Azure Red Hat OpenShift 群集。

1. 转到[用户-所有用户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)边栏选项卡。
2. 单击 **+ 新用户**以打开**用户**窗格。
3. 输入**名称**为此用户。
4. 创建**用户名**根据你创建的租户的名称与`.onmicrosoft.com`附加在后面。 例如， `yourUserName@yourTenantName.onmicrosoft.com` 。 记下此用户名。 你将需要它来登录到你的群集。
5. 单击**目录角色**以打开目录角色窗格中，选择**全局管理员**，然后单击**确定**窗格的底部。
6. 在中**用户**窗格中，单击**显示密码**并记录临时密码。 在第一次登录后，将提示您将其重置。
7. 在窗格的底部，单击**创建**以创建用户。

## <a name="create-an-azure-ad-security-group"></a>创建 Azure AD 安全组

若要授予群集管理员访问权限中的 Azure AD 安全组的成员身份会同步到 OpenShift 组"osa-客户的管理员"。 如果未指定，将不授予任何群集管理员访问权限。

1. 打开[Azure Active Directory 组](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)边栏选项卡。
2. 单击 **+ 新建组**。
3. 提供组名称和描述。
4. 设置**组类型**到**安全**。
5. 设置**成员身份类型**到**已分配**。

    将添加到此安全组在前面步骤中创建的 Azure AD 用户。

6. 单击**成员**以打开**选择成员**窗格。
7. 在成员列表中，选择上面创建的 Azure AD 用户。
8. 在门户的底部，单击**选择**，然后**创建**创建安全组。

    记下的组 ID 值。

9. 创建组后，您将看到它在所有组的列表。 单击新组。
10. 在显示页面上，复制**对象 ID**。 我们将把此值作为`GROUPID`中[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

可以自动创建的 Azure Active Directory (Azure AD) 应用注册客户端通过省略创建群集的一部分`--aad-client-app-id`标记，用于`az openshift create`命令。 本教程演示如何创建 Azure AD 应用注册出于完整性的考虑。

如果你的组织还没有 Azure Active Directory (Azure AD) 应用注册要用作服务主体，请按照以下说明创建一个。

1. 打开[应用注册边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)然后单击 **+ 新注册**。
2. 在中**注册应用程序**窗格中，输入应用程序注册的名称。
3. 确保下**支持的帐户类型**的**此组织目录中的帐户**处于选中状态。 这是最安全的选择。
4. 一旦我们知道群集的 URI，我们将添加的重定向 URI 更高版本。 单击**注册**按钮以创建 Azure AD 应用程序注册。
5. 在显示页面上，复制**应用程序 （客户端） ID**。 我们将把此值作为`APPID`中[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程。

![应用程序对象页面的屏幕截图](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>创建客户端机密

生成您的应用程序到 Azure Active Directory 进行身份验证的客户端机密。

1. 在中**管理**部分中的应用程序注册页上，单击**证书和机密**。
2. 上**证书和机密**窗格中，单击 **+ 新客户端机密**。  **将客户端机密添加**窗格会显示。
3. 提供**说明**。
4. 设置**Expires**到您喜欢的持续时间**2 年内**。
5. 单击**外**密钥值将显示在**客户端机密**页部分。
6. 复制密钥值。 我们将把此值作为`SECRET`中[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程。

![证书和机密窗格的屏幕截图](./media/howto-create-tenant/create-key.png)

有关 Azure 应用程序对象的详细信息，请参阅[应用程序和 Azure Active Directory 中的服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

详细了解如何创建一个新的 Azure AD 应用程序，请参阅[使用 Azure Active Directory v1.0 终结点注册应用](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)。

## <a name="add-api-permissions"></a>添加 API 权限

1. 在中**管理**部分中，单击**API 权限**。
2. 单击**添加权限**，然后选择**Azure Active Directory Graph**然后**委托的权限**
3. 展开**用户**在下面的列表并确保**User.Read**已启用。
4. 向上滚动并选择**应用程序权限**。
5. 展开**Directory**在下面的列表并启用**Directory.ReadAll**
6. 单击**添加权限**以接受更改。
7. API 的权限面板中现在应显示两者*User.Read*并*Directory.ReadAll*。 请注意在警告**所需的管理员同意**列下一步*Directory.ReadAll*。
8. 你是否*Azure 订阅管理员*，单击**授予管理员许可*订阅名称***  下面。 如果不是*Azure 订阅管理员*，向管理员请求同意。
![API 权限面板的屏幕截图。 User.Read 和 Directory.ReadAll 添加，管理员同意所需的权限 Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 仅在已获得许可之后，群集管理员组的同步将起作用。 将显示一个复选标记和一条消息的绿色圆圈"授予*订阅名称*"中*需要管理员许可*列。

管理管理员和其他角色的详细信息，请参阅[添加或更改 Azure 订阅管理员](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)。

## <a name="resources"></a>资源

* [应用程序和 Azure Active Directory 中的服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [快速入门：使用 Azure Active Directory v1.0 终结点注册应用](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>后续步骤

如果你已满足所有[Azure Red Hat OpenShift 先决条件](howto-setup-environment.md)，已准备好创建第一个群集 ！

请尝试教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)
