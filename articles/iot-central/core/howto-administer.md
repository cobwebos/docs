---
title: 管理 Azure IoT Central 应用程序 |Microsoft Docs
description: 作为管理员，如何通过更改应用程序名称、URL、上传映像、复制和删除应用程序来管理 Azure IoT Central 应用程序
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 89c4dd294cbbf0953545e1055e32adfc5f7cce28
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990787"
---
# <a name="manage-your-iot-central-application"></a>管理你的 IoT Central 应用程序

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本文介绍如何以管理员身份通过更改应用程序名称和 URL 来管理应用程序、上传图像，还可以了解如何在 Azure IoT Central 应用程序中复制和删除应用程序。

只有 Azure IoT Central 应用程序的“管理员”角色才能访问和使用“管理”部分。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”角色。 

## <a name="change-application-name-and-url"></a>更改应用程序名称和 URL

在“应用程序设置”页中，可以更改应用程序的名称和 URL，然后选择“保存”。

![“应用程序设置”页](media/howto-administer/image0-a.png)

如果管理员为应用程序创建了自定义主题，此页将包含在 UI 中隐藏**应用程序名称**的选项。 如果自定义主题中的应用程序徽标包含应用程序名称，则这非常有用。 有关详细信息，请参阅[自定义 Azure IOT CENTRAL UI](./howto-customize-ui.md)。

> [!Note]
> 如果更改了 URL，旧 URL 可由其他 Azure IoT Central 客户使用。 如果出现此情况，你再也不能使用旧 URL。 更改 URL 后，旧 URL 不再有效，因此需要告知用户要使用的新 URL。

## <a name="prepare-and-upload-image"></a>准备和上传图像

若要更改应用程序图像，请参阅[准备图像并将其上传到 Azure IoT Central 应用程序](howto-prepare-images.md)。

## <a name="copy-an-application"></a>复制应用程序

可以创建任一应用程序的副本，但其中不会包括任何设备实例、设备数据历史记录和用户数据。 该副本使用您将对其进行计费的标准定价计划。 不能创建以这种方式使用免费定价计划的应用程序。

选择 "**复制**"。 在 "" 对话框中，输入新应用程序的详细信息。 然后选择 "**复制**"，确认是否要继续。 在[创建应用程序](quick-deploy-iot-central.md)快速入门中详细了解此窗体中的字段。

![“应用程序设置”页](media/howto-administer/appcopy2.png)

应用复制操作成功后，可以使用链接导航到新应用程序。

![“应用程序设置”页](media/howto-administer/appcopy3a.png)

复制应用程序还会复制规则和电子邮件操作的定义。 某些操作（例如流、逻辑应用等）通过规则 ID 与特定规则相关联。 将规则复制到其他应用程序时，它将获取其自己的规则 ID。 在这种情况下，用户必须创建一个新的操作，然后将新规则与其关联。 通常情况下，最好检查规则和操作，以确保它们在新应用中处于最新状态。

> [!WARNING]
> 如果仪表板包括显示特定设备相关信息的磁贴，则那些磁贴会显示在新应用程序中**找不到请求的资源**。 您必须重新配置这些磁贴，以显示有关新应用程序中设备的信息。

## <a name="delete-an-application"></a>删除应用程序

使用“删除”按钮可以永久删除 IoT Central 应用程序。 此操作永久删除与应用程序关联的所有数据。

> [!Note]
> 若要删除某个应用程序，还必须有权删除在创建应用程序时所选的 Azure 订阅中的资源。 有关详细信息，请参阅[使用基于角色的访问控制来管理对 Azure 订阅资源的访问权限](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。


## <a name="manage-programatically"></a>以编程方式管理

IoT Central Azure 资源管理器 SDK 程序包适用于 Node、Python、C#、Ruby、Java 和 Go。 你可以使用这些包来创建、列出、更新或删除 IoT Central 应用程序。 这些包包括用于管理身份验证和错误处理的帮助程序。

可以在 [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples) 中找到有关如何使用 Azure 资源管理器 SDK 的示例。

若要了解详细信息，请参阅以下 GitHub 存储库和包：

| 语言 | 存储库 | 程序包 |
| ---------| ---------- | ------- |
| 节点 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>后续步骤
 
现在，你已了解如何管理 Azure IoT Central 应用程序，接下来是了解如何在 Azure IoT Central 中[管理用户和角色](howto-manage-users-roles.md)。