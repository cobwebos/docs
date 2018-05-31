---
title: 管理 Azure IoT Central 应用程序 | Microsoft Docs
description: 如何以管理员身份管理 Azure IoT Central 应用程序
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: b60b9e851a3b6612964e67e7764ad8d43d606b4e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199963"
---
# <a name="how-to-administer-your-application"></a>如何管理应用程序

创建 Microsoft Azure IoT Central 应用程序后，可以使用 Azure IoT Central 用户界面的“管理”部分对其进行管理。 若要导航到“管理”部分，请在左侧导航菜单中选择“管理”。

在“管理”部分可以：

- 管理用户

- 管理角色

- 查看计费信息

- 管理应用程序设置

- 延长免费试用期

“管理”部分提供了一个辅助导航菜单，其中包含各种管理任务的链接。

只有 Azure IoT Central 应用程序中的“管理员”角色才能访问和使用“管理”部分。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”角色。 本文的“管理用户”部分详细介绍了如何向其他用户分配“管理员”角色。

## <a name="change-application-name"></a>更改应用程序名称

若要更改应用程序的名称，请在“管理”部分使用辅助导航菜单导航到“应用程序设置”页。

在“应用程序设置”页上，输入在“应用程序名称”字段中选择的名称，然后选择“保存”。

## <a name="change-the-application-url"></a>更改应用程序 URL

若要更改应用程序的 URL，请在“管理”部分使用辅助导航菜单导航到“应用程序设置”页。

![应用程序设置页](media\howto-administer\image0-a.png)

在“应用程序设置”页上，输入在“URL”字段中选择的 URL，然后选择“保存”。 URL 的长度不能超过 200 个字符。 如果该 URL 不可用，会显示验证错误

> [!Note]
> 如果更改了 URL，旧 URL 可由其他 Azure IoT Central 客户使用。 在这种情况下，你再也不能使用旧 URL。 更改 URL 后，旧 URL 不再有效，因此必须告知用户要使用的新 URL。

## <a name="change-the-application-image"></a>更改应用程序图像

有关在 Azure IoT Central 应用程序中使用图像的详细信息，请参阅[准备图像并将其上传到 Azure IoT Central 应用程序](howto-prepare-images.md)。

## <a name="delete-an-application"></a>删除应用程序

若要删除应用程序，请在“管理”部分使用辅助导航菜单导航到“应用程序设置”页。

选择“删除”。

> [!Note]
> 删除某个应用程序会删除与该应用程序关联的所有数据，并且此操作不可恢复。 若要删除某个应用程序，还必须有权删除在创建应用程序时所选的 Azure 订阅中的资源。 有关详细信息，请参阅[使用基于角色的访问控制管理 Azure 订阅资源的访问权限](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。

## <a name="roles-in-azure-iot-central"></a>Azure IoT Central 中的角色

使用角色可以控制组织中的哪些人可以执行各种 Azure IoT Central 任务。 可将 Azure IoT Central 提供的三个角色分配给应用程序的用户。 角色由每个应用程序分配。 同一个用户可以在不同的应用程序中充当不同的角色。 可将同一个用户分配到应用程序中的多个角色。

### <a name="administrator"></a>管理员

充当“管理员”角色的用户有权访问 Azure IoT Central 应用程序中的所有功能。

创建应用程序的用户会自动分配到“管理员”角色。 必须始终有一个用户充当“管理员”角色。

### <a name="application-builder"></a>应用程序构建人员

充当“应用程序构建人员”角色的用户可以在 Azure IoT Central 应用程序中执行任何操作，但无法管理应用程序。

### <a name="application-operator"></a>应用程序操作员

充当“应用程序操作员”角色的用户无权访问“应用程序构建人员”页。 他们无法管理应用程序。

## <a name="manage-users"></a>管理用户

应用程序管理员可将用户分配到应用程序中的角色。

### <a name="add-users"></a>添加用户

每个用户必须有一个用户帐户才能登录和访问 Azure IoT Central 应用程序。 Azure IoT Central 支持 Microsoft 帐户 (MSA) 和 Azure Active Directory (AAD) 帐户。 Azure IoT Central 目前不支持 Azure Active Directory 组。

有关详细信息，请参阅 [Microsoft 帐户帮助](https://support.microsoft.com/products/microsoft-account?category=manage-account)和[快速入门：将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

1. 若要将用户帐户添加到 Azure IoT Central 应用程序，请在“管理”部分使用辅助导航菜单导航到“用户”页。

    ![用户列表](media\howto-administer\image1.png)

1. 在“用户”页上，选择“+ 添加用户”以添加用户。

    ![添加用户](media\howto-administer\image2.png)

1. 将用户添加到 Azure IoT Central 应用程序时，请从“角色”下拉列表中选择用户的角色。 请在本文的“Azure IoT Central 中的角色”部分详细了解角色。

    ![角色选择](media\howto-administer\image3.png)

    > [!NOTE]
    >  若要批量添加用户，请输入想要添加的所有用户的用户 ID，并用分号分隔。 从“角色”下拉列表中选择一个角色，然后选择“保存”。

1. 添加用户后，“用户”页上会显示该用户对应的条目。

    ![用户列表](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>编辑分配给用户的角色

角色在经分配后不可更改。 若要更改分配给某个用户的角色，请删除该用户，然后使用不同的角色再次添加该用户。

### <a name="delete-users"></a>删除用户

若要删除用户，请在“用户”页上选中一个或多个相应的复选框，然后选择“删除”。

## <a name="view-your-bill"></a>查看帐单

若要查看帐单，请在“管理”部分导航到“计费”页，然后选择“计费”。 Azure 计费页将在新选项卡中打开，其中显示了每个 Azure IoT Central 应用程序的帐单。

## <a name="convert-your-trial-to-a-paid-application"></a>将试用版转换为付费版应用程序

试用 IoT Central 后，可将试用版转换为付费版应用程序。 若要完成此自助服务过程，请遵循以下步骤：

1. 在“管理”部分使用辅助导航菜单导航到“计费”页。 如果尚未延长试用期，该页的外观如下所示：

    ![免费试用状态](media/howto-administer/freetrial.png)

2. 单击“转换为付费版”。 如果尚未延长试用期，弹出窗口的外观如下所示：

    在弹出窗口中，选择相应的 Azure Active Directory 租户，然后选择要对 IoT Central 应用程序使用的 Azure 订阅。

    ![延长免费试用期](media/howto-administer/extend.png)

3. 单击“转换”后，试用版将转换为付费版应用程序，并开始计费。

## <a name="extend-your-free-trial"></a>延长免费试用期

默认情况下，所有免费试用版可用 7 天。 如果想要将试用期增加到 30 天，请遵循以下步骤：

1. 在“管理”部分使用辅助导航菜单导航到“计费”页：

1. 单击“延长试用期”。 在弹出窗口中，选择相应的 Azure Active Directory 租户，然后选择要对 IoT Central 应用程序使用的 Azure 订阅：

1. 然后单击“延期”。 现在，试用版的有效期为 30 天。

## <a name="next-steps"></a>后续步骤

了解如何管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [设置设备模板](howto-set-up-template.md)