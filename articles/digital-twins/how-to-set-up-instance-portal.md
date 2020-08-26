---
title: 设置实例和身份验证（门户）
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 门户设置 Azure 数字孪生服务的实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1f7486f1080c0fbb25b1be6ab70bb647a546ceca
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234975"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>设置 Azure 数字孪生实例和身份验证 (门户) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍 **设置新的 Azure 数字孪生实例**的步骤，包括创建实例和设置身份验证。 完成本文后，你将拥有一个可开始对进行编程的 Azure 数字孪生实例。

本文的此版本通过使用 Azure 门户逐个手动完成这些步骤。 Azure 门户是基于 Web 的统一控制台，提供可替代命令行工具的方法。
* 若要使用 CLI 手动完成这些步骤，请参阅本文的 CLI 版本： how [*to： Set a instance and authentication (CLI) *](how-to-set-up-instance-cli.md)。
* 若要通过使用部署脚本示例的自动安装运行，请参阅本文的脚本编写版本： how [*to： Set a instance and authentication (script) *](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分中，将使用[Azure 门户](https://ms.portal.azure.com/)**创建 Azure 数字孪生的新实例**。 导航到门户，然后用你的凭据登录。

进入门户后，首先在 Azure 服务的 "主页" 菜单中选择 " _创建资源_ "。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="从 "主页" 的 "Azure 门户" 页中选择 "创建资源"":::

在搜索框中搜索 " *Azure 数字孪生* "，然后从结果中选择 " **azure 数字孪生 (预览") ** 服务。 选择 " _创建_ " 按钮以创建服务的新实例。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="从 Azure 数字孪生服务页中选择 "创建"":::

在下面的 " *创建资源* " 页上，填写以下给定值：
* **订阅**：要使用的 Azure 订阅
  - **资源组**：要在其中部署实例的资源组。 如果尚未记住现有的资源组，可通过选择 "新建" 链接并输入新资源组 *的名称* 来创建一个资源组。
* **位置**：用于部署的支持 Azure 数字孪生的区域。 有关区域支持的更多详细信息，请访问 azure [*孪生) 提供 (的 azure 产品 *](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* **资源名称**： Azure 数字孪生实例的名称。 新实例的名称在你的订阅的区域内必须是唯一的 (这意味着，如果你的订阅在已使用所选名称的区域中有另一个 Azure 数字孪生实例，则系统将要求你选择不同的名称) 。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="填写所述的值以创建 Azure 数字孪生资源":::

完成后，选择“查看 + 创建”。 这会转到 "摘要" 页，您可以在其中查看所输入的实例详细信息，然后单击 " _创建_"。 

### <a name="verify-success-and-collect-important-values"></a>验证成功并收集重要值

推送 *创建*后，可以通过门户图标栏在 Azure 通知中查看实例的部署状态。 通知将指示部署成功的时间，并且你将能够选择 " _中转到资源_ " 按钮来查看已创建的实例。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="显示成功部署并突出显示 "中转到资源" 按钮的 Azure 通知视图":::

或者，如果部署失败，通知将指出原因。 观察错误消息中的建议，然后重试创建实例。

>[!TIP]
>创建实例后，可以通过在 Azure 门户搜索栏中搜索实例的名称，随时返回到此页。

在实例的 " *概述* " 页上，记下其 *名称*、 *资源组*和 *主机名*。 这些是你在继续使用 Azure 数字孪生实例时可能需要的所有重要值。 如果其他用户将对该实例进行编程，则应该与它们共享这些值。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="突出显示实例的 "概述" 页中的重要值":::

现已准备好使用 Azure 数字孪生实例。 接下来，你将为适当的 Azure 用户授予管理权限。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

首先，在 Azure 门户中打开 Azure 数字孪生实例的页面。 从实例的菜单中，选择 " *访问控制 (IAM) *。 选择 "*添加角色分配*" 下的 "*添加*" 按钮。

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="选择从 "访问控制 (IAM) " 页添加角色分配":::

在 " *添加角色分配* " 页上，填写值 (必须由在 Azure 订阅中具有 [足够权限](#prerequisites-permission-requirements) 的用户完成) ：
* **角色**：从下拉菜单中选择 " *Azure 数字孪生所有者 (预览") *
* **将访问权限分配给**：从下拉菜单中选择*Azure AD 用户、组或服务主体*
* **选择**：搜索要分配的用户的名称或电子邮件地址。 选择结果后，用户将显示在 " *所选成员* " 部分中。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="将列出的字段填充到 "添加角色分配" 对话框中":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

输入完详细信息后，单击 " *保存* " 按钮。

### <a name="verify-success"></a>验证是否成功

你可以在 " *访问控制" (IAM) > 角色分配*"下查看已设置的角色分配。 用户应在列表中显示 *Azure 数字孪生所有者 (预览) *的角色。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 门户中查看 Azure 数字孪生实例的角色分配":::

现在，你已准备好使用 Azure 数字孪生实例，并已分配了管理它的权限。 接下来，你将设置对客户端应用程序的访问权限。

## <a name="set-up-access-permissions-for-client-applications"></a>设置客户端应用程序的访问权限

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

首先，导航到 Azure 门户中的 [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) (可以使用此链接或通过门户搜索栏) 找到它。 从 "服务" 菜单中选择 " *应用注册* "，然后选择 " *+ 新注册*"。

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Azure 门户中 "Azure AD 服务" 页的视图，突出显示 "应用注册" 菜单选项和 "+ 新建注册" 按钮":::

在下面的 " *注册应用程序* " 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 您可以使用 `http://localhost` 。

完成后，单击 " *注册* " 按钮。

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text=""注册应用程序" 页的视图，其中填写了所述的说明值":::

完成设置注册后，门户会将你重定向到其详细信息页面。

### <a name="provide-azure-digital-twins-api-permission"></a>提供 Azure 数字孪生 API 权限

接下来，将你创建的应用注册配置为具有对 Azure 数字孪生 Api 的基准权限。

从应用注册的门户页中，从菜单中选择 " *API 权限* "。 在 "下列权限" 页上，单击 " *+ 添加权限* " 按钮。

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Azure 门户中的应用注册的视图，突出显示 "API 权限" 菜单选项和 "+ 添加权限" 按钮":::

在下面的 " *请求 API 权限* " 页中，切换到 " *我的组织使用的 api* " 选项卡，然后搜索 " *azure 数字孪生*"。 从搜索结果中选择 " *Azure 数字孪生* "，以继续分配 Azure 数字孪生 api 的权限。

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="显示 Azure 数字孪生的 "请求 API 权限" 页面搜索结果视图":::

>[!NOTE]
> 如果现有的 Azure 数字孪生实例的 (服务早于2020年7月) 之前的公共预览版，则需改为查看 *Azure 智能空间服务* 。 这是同一组 Api 的旧名称，并且不会更改你在此步骤之外的体验。

接下来，选择要为这些 Api 授予的权限。 展开 " **读取 (1) ** " 权限，并选中 " *读取* " 复选框以授予此应用注册读取器和编写器权限。

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text=""请求 API 权限" 页的视图选择 Azure 数字孪生 Api 的 "读取" 权限":::

完成后点击 *添加权限* 。

### <a name="verify-success"></a>验证是否成功

返回到 " *API 权限* " 页，验证现在是否存在用于反映读/写权限的 Azure 数字孪生条目：

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Azure AD 应用注册的 API 权限的门户视图，其中显示了 Azure 数字孪生的 "读取/写入访问权限"":::

你还可以在应用注册的 *manifest.js*中验证与 Azure 数字孪生的连接，该连接已在添加 API 权限时随 Azure 数字孪生信息自动更新。

为此，请从菜单中选择 " *清单* "，以查看应用注册的清单代码。 滚动到代码窗口的底部，在下查找这些字段 `requiredResourceAccess` 。 值应与以下屏幕截图中的值匹配：

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Azure AD 应用注册清单的门户视图。嵌套在 "requiredResourceAccess" 下，有一个 "resourceAppId" 值为 0b07f429-9f4b 4714-9392-cc5e8e80c8b0，"resourceAccess > id" 值为4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>收集重要值

接下来，从菜单栏中选择 " *概述* " 以查看应用注册的详细信息：

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="应用注册重要值的门户视图":::

记下**在页面上显示的***应用程序 (客户端) id*和*目录 (租户) id* 。 稍后将需要这些值对 [Azure 数字孪生 api 的客户端应用进行身份验证](how-to-authenticate-client.md)。 如果您不是将为此类应用程序编写代码的人员，则应与将要进行共享的人员共享这些值。

### <a name="other-possible-steps-for-your-organization"></a>组织的其他可能步骤

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>后续步骤

请参阅如何通过编写客户端应用的身份验证代码将客户端应用程序连接到实例：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)