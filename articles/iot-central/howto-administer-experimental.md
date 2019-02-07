---
title: 管理 Azure IoT Central 应用程序 | Microsoft Docs
description: 如何以管理员身份管理 Azure IoT Central 应用程序
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ebca3060e902c0a45619c2bd64240537cd95308d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811845"
---
# <a name="administer-your-iot-central-application"></a>管理 IoT Central 应用程序

创建 IoT Central 应用程序后，可以转到“管理”部分来执行以下操作：

- 管理应用程序设置
- 管理用户
- 管理角色
- 查看帐单
- 将试用版转换为即用即付版
- 导出数据
- 管理设备连接
- 使用访问令牌

只有 Azure IoT Central 应用程序的“管理员”角色才能访问和使用“管理”部分。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”角色。 本文中的[管理用户](#manage-users)部分详细介绍了如何向其他用户分配“管理员”角色。

## <a name="manage-application-settings"></a>管理应用程序设置

### <a name="change-application-name-and-url"></a>更改应用程序名称和 URL
在“应用程序设置”页中，可以更改应用程序的名称和 URL，然后选择“保存”。

![“应用程序设置”页](media/howto-administer-experimental/image0-a.png)

> [!Note]
> 如果更改了 URL，旧 URL 可由其他 Azure IoT Central 客户使用。 如果出现此情况，你再也不能使用旧 URL。 更改 URL 后，旧 URL 不再有效，因此需要告知用户要使用的新 URL。

### <a name="prepare-and-upload-image"></a>准备和上传图像
若要更改应用程序图像，请参阅[准备图像并将其上传到 Azure IoT Central 应用程序](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。

### <a name="copy-an-application"></a>复制应用程序
可以创建任一应用程序的副本，但其中不会包括任何设备实例、设备数据历史记录和用户数据。 副本是付费的即用即付应用程序。 不能以这种方式创建试用版应用程序。

单击“复制”按钮。 在对话框中，输入新的即用即付应用程序的详细信息。 然后单击“复制”按钮以确认继续。 在[创建应用程序](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)快速入门中详细了解此窗体中的字段。

![“应用程序设置”页](media/howto-administer-experimental/appCopy2.png)

应用复制操作成功后，可以使用显示的链接转到通过复制应用程序创建的新应用程序。

![“应用程序设置”页](media/howto-administer-experimental/appCopy3.png)

> [!Note]
> 复制某个应用程序也会复制规则和操作的定义。 但是，由于不会将有权访问原始应用的用户复制到已复制的应用，因此，必须手动将这些用户添加到需要指定用户的操作（例如电子邮件）。 一般而言，最好是检查规则和操作，以确保它们在新应用中是最新的。

### <a name="delete-an-application"></a>删除应用程序

> [!Note]
> 若要删除某个应用程序，还必须有权删除在创建应用程序时所选的 Azure 订阅中的资源。 有关详细信息，请参阅[使用基于角色的访问控制来管理对 Azure 订阅资源的访问权限](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。

使用“删除”按钮可以永久删除 IoT Central 应用程序。 此操作会永久删除与该应用程序关联的所有数据。

## <a name="manage-users"></a>管理用户

### <a name="add-users"></a>添加用户

每个用户必须有一个用户帐户才能登录和访问 Azure IoT Central 应用程序。 Azure IoT Central 支持 Microsoft 帐户 (MSA) 和 Azure Active Directory (Azure AD) 帐户。 Azure IoT Central 目前不支持 Azure Active Directory 组。

有关详细信息，请参阅 [Microsoft 帐户帮助](https://support.microsoft.com/products/microsoft-account?category=manage-account)和[快速入门：将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

1. 若要将用户添加到 IoT Central 应用程序，请转到“用户”页中的“管理”部分。

    ![用户列表](media/howto-administer-experimental/image1.png)

1. 若要添加用户，在“用户”页上，选择“+ 添加用户”。

1. 从“角色”下拉菜单中为用户选择一个角色。 在本文的[管理角色](#manage-roles)部分详细了解角色。

    ![角色选择](media/howto-administer-experimental/image3.png)

    > [!NOTE]
    >  若要批量添加用户，请输入想要添加的所有用户的用户 ID，并用分号分隔。 从“角色”下拉菜单中选择一个角色。 再选择“保存”。

### <a name="edit-the-roles-that-are-assigned-to-users"></a>编辑分配给用户的角色

角色分配后无法再对其进行更改。 若要更改分配给某个用户的角色，请删除该用户，然后使用不同的角色再次添加该用户。

### <a name="delete-users"></a>删除用户

若要删除用户，选择“用户”页上的一个或多个复选框。 然后选择“删除”。

## <a name="manage-roles"></a>管理角色

使用角色可以控制组织中的哪些人可以在 IoT Central 中执行各种任务。 可将三种角色分配给应用程序的用户。 

### <a name="administrator"></a>管理员

充当“管理员”角色的用户有权访问应用程序中的所有功能。

创建应用程序的用户会自动分配到“管理员”角色。 必须始终有一个用户充当“管理员”角色。

### <a name="application-builder"></a>应用程序构建人员

充当“应用程序构建人员”角色的用户可以在应用程序中执行任何操作，但无法管理应用程序。 这意味着，构建人员可以创建、编辑和删除设备模板与设备、管理设备集，以及运行分析和作业。 构建人员无权访问应用程序的“管理”部分。

### <a name="application-operator"></a>应用程序操作员

充当“应用程序操作员”角色的用户无法对设备模板进行更改，也无法管理应用程序。 这意味着，操作员可以添加和删除设备、管理设备集，以及运行分析和作业。 操作员无权访问“应用程序构建人员”和“管理”页。


## <a name="view-your-bill"></a>查看帐单

若要查看帐单，请在“管理”部分转到“计费”页。 Azure 计费页将在新选项卡中打开，可以在此处看到每个 Azure IoT Central 应用程序的帐单。

### <a name="convert-your-trial-to-pay-as-you-go"></a>将试用版转换为即用即付版

可试用版应用程序转换为即用即付应用程序。 下面是这些应用程序类型之间的差异。

- **试用版**应用程序免费 7 天，然后过期。 它们可以在到期之前随时转换为即用即付。
- **即用即付**应用程序按设备收费，前 5 台设备免费。

可在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上了解定价详细信息。
    
若要完成此自助服务过程，请遵循以下步骤：

1. 在“管理”部分转到“计费”页。 

    ![试用状态](media/howto-administer-experimental/freetrialbilling.png)

1. 单击“转换为即用即付”。 

    ![转换试用版](media/howto-administer-experimental/convert.png)

1. 选择相应的 Azure Active Directory，然后选择要对即用即付应用程序使用的 Azure 订阅。

1. 单击“转换”后，应用程序随即会变成即用即付应用程序，并开始计费。

## <a name="export-data"></a>导出数据

可以启用“连续数据导出”，以将度量值、设备和设备模板数据导出到 Azure Blob 存储帐户。 详细了解[如何导出数据](#howto-export-data)。

## <a name="manage-device-connection"></a>管理设备连接

使用此处的密钥和证书在应用程序中大规模连接设备。 详细了解如何[连接设备](#concepts-connectivity)。

## <a name="use-access-tokens"></a>使用访问令牌

生成访问令牌，以便在开发人员工具中使用。 目前，可以使用一个开发人员工具（IoT Central 资源管理器）来监视设备消息以及属性和设置的更改。 详细了解 [IoT Central 资源管理器](#howto-use-iotc-explorer)。 

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>使用 Azure SDK 进行控制平面操作

IoT Central Azure 资源管理器 SDK 程序包适用于 Node、Python、C#、Ruby、Java 和 Go。 这些库支持对 IoT Central 执行控制平面操作，使你能够创建、列出、更新或删除 IoT Central 应用程序。 它们还提供处理身份验证和特定于每种语言的错误处理的帮助程序。 

可以在 [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples) 中找到有关如何使用 Azure 资源管理器 SDK 的示例。

若要了解详细信息，请在 GitHub 上查看这些程序包。

| 语言 | 存储库 | 程序包 |
| ---------| ---------- | ------- |
| 节点 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>后续步骤

了解如何管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [设置设备模板](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
