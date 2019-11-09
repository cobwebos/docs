---
title: 更改 Azure IoT Central 应用程序设置 |Microsoft Docs
description: 作为管理员，如何通过更改应用程序名称、URL、上传映像和删除应用程序来管理 Azure IoT Central 应用程序
author: viv-liu
ms.author: viviali
ms.date: 10/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5d14ed31b41deb0db44ba452470c45d69a0ec781
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896055"
---
# <a name="change-iot-central-application-settings"></a>更改 IoT Central 应用程序设置

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍如何以管理员身份通过更改应用程序名称和 URL、上传图像以及删除 Azure IoT Central 应用程序中的应用程序来管理应用程序。

只有 Azure IoT Central 应用程序的“管理员”角色才能访问和使用“管理”部分。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”角色。

## <a name="change-application-name-and-url"></a>更改应用程序名称和 URL

在“应用程序设置”页中，可以更改应用程序的名称和 URL，然后选择“保存”。

![“应用程序设置”页](media/howto-administer/image0-a.png)

如果管理员为应用程序创建了自定义主题，此页将包含在 UI 中隐藏**应用程序名称**的选项。 如果自定义主题中的应用程序徽标包含应用程序名称，则此选项很有用。 有关详细信息，请参阅[自定义 Azure IOT CENTRAL UI](./howto-customize-ui.md)。

> [!Note]
> 如果更改了 URL，旧 URL 可由其他 Azure IoT Central 客户使用。 如果出现此情况，你再也不能使用旧 URL。 更改 URL 后，旧 URL 不再有效，因此需要告知用户要使用的新 URL。

## <a name="delete-an-application"></a>删除应用程序

使用“删除”按钮可以永久删除 IoT Central 应用程序。 此操作永久删除与应用程序关联的所有数据。

> [!Note]
> 若要删除某个应用程序，还必须有权删除在创建应用程序时所选的 Azure 订阅中的资源。 有关详细信息，请参阅[使用基于角色的访问控制来管理对 Azure 订阅资源的访问权限](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。

## <a name="manage-programmatically"></a>以编程方式管理

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
