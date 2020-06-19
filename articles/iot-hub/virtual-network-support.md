---
title: Azure IoT 中心的虚拟网络支持
description: 如何将虚拟网络连接模式与 IoT 中心配合使用
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: jlian
ms.openlocfilehash: 7d7e04c526f7327a000ac26e255d2c8363c01f5c
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871237"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT 中心支持具有专用链接和托管标识的虚拟网络

默认情况下，IoT 中心的主机名会映射到一个公共终结点，该终结点具有可通过 internet 以公开方式路由的 IP 地址。 不同的客户将共享此 IoT 中心公共终结点，广域网和本地网络中的 IoT 设备均可对其进行访问。

![IoT 中心公共终结点](./media/virtual-network-support/public-endpoint.png)

IoT 中心的功能，包括[消息路由](./iot-hub-devguide-messages-d2c.md)、[文件上传](./iot-hub-devguide-file-upload.md)和[批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)，还需要配置有从 IoT 中心到客户拥有的 Azure 资源的连接（通过其公共终结点）。 这些连接路径上的流量共同构成了从 IoT 中心到客户资源的出口流量。

你可能会想通过自己拥有和运行的某台 VNet 来限制与你的 Azure 资源（包括 IoT 中心）之间的连接。 其原因包括：

* 通过阻止与公共 internet 的连接，为 IoT 中心引入网络隔离。

* 提供来自本地网络资产的专用连接体验，确保数据和流量直接传输到 Azure 主干网络。

* 阻止来自敏感的本地网络的渗透攻击。 

* 采用 Azure 范围内常见的[专用终结点](../private-link/private-endpoint-overview.md)连接模式。

本文介绍如何通过在与 IoT 中心的入口连接中使用 [Azure 专用链接](../private-link/private-link-overview.md)以及在从 IoT 中心到其他 Azure 资源之间的出口连接中使用受信任的 Microsoft 服务异常来实现上述目标。

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>使用 Azure 专用链接实现到 IoT 中心的出口连接

专用终结点是在客户拥有的 VNet 内部分配的专用 IP 地址，可通过该地址访问 Azure 资源。 通过 Azure 专用链接，可以为 IoT 中心设置专用终结点，以允许 VNet 中的服务访问 IoT 中心，而无需将流量发送到 IoT 中心的公共终结点。 同样，本地设备可以使用[虚拟专用网络 (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 对等互连，通过其专用终结点，连接到 VNet 和 IoT 中心。 这样，便可以通过使用 [IoT 中心 IP 筛选器](./iot-hub-ip-filtering.md)和[将路由配置为不将任何数据发送到内置终结点](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint)，来限制或完全阻止与 IoT 中心公共终结点的连接。 此方法使用设备的专用终结点来保持与中心的连接。 此设置主要适用于本地网络中的设备。 对于广域网中部署的设备，不建议使用此设置。

![IoT 中心公共终结点](./media/virtual-network-support/virtual-network-ingress.png)

在继续操作之前，请确保满足以下先决条件：

* 已[创建了一个 Azure VNet](../virtual-network/quick-create-portal.md)，其中配置了要创建专用终结点的子网。

* 对于在本地网络中运行的设备，在 Azure VNet 中设置[虚拟专用网络 (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 专用对等互连。

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>设置一个用于 IoT 中心入口的专用终结点

1. 在 Azure 门户中，依次选择“网络”、“专用终结点连接”，然后单击 “+专用终结点”  。

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="屏幕截图显示了为 IoT 中心添加专用终结点的位置":::

1. 提供订阅、资源组、名称和要在其中创建新专用终结点的区域。 理想情况下，创建专用终结点的区域应与中心所在区域相同。

1. 单击“下一步:资源”，提供用于 IoT 中心资源的订阅，选择“Microsoft.Devices/IotHubs”作为资源类型，对于“资源”，选择你的 IoT 中心名称，然后选择“iotHub”作为目标子资源  。

1. 单击“下一步:配置”，提供要在其中创建专用终结点的虚拟网络和子网。 如果需要，可选择“与 Azure 专用 DNS 区域集成”选项。

1. 单击“下一步:标记”，（可选）为资源提供标记。

1. 单击“查看 + 创建”以创建专用链接资源。

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>内置的与事件中心兼容的终结点不支持通过专用终结点进行访问

[内置的与事件中心兼容的终结点](iot-hub-devguide-messages-read-builtin.md)不支持通过专用终结点进行访问。 如果配置了 IoT 中心的专用终结点，它仅用于入口连接。 只能通过公共 internet 使用内置的与事件中心兼容的终结点中的数据。 

IoT 中心的 [IP 筛选器](iot-hub-ip-filtering.md)也不会控制对内置终结点的公共访问。 若要完全阻止公共网络访问 IoT 中心，必须执行以下操作： 

1. 为 IoT 中心配置专用终结点访问
1. 通过使用 IP 筛选器阻止所有 IP 来关闭公共网络访问
1. 通过[将路由设置为不向内置事件中心终结点发送数据](iot-hub-devguide-messages-d2c.md)来关闭内置事件中心终结点
1. 关闭[回退路由](iot-hub-devguide-messages-d2c.md#fallback-route)
1. 使用[受信任的 Microsoft 服务](#egress-connectivity-from-iot-hub-to-other-azure-resources)来配置指向其他 Azure 资源的出口

### <a name="pricing-for-private-link"></a>专用链接定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>从 IoT 中心到其他 Azure 资源的出口连接

IoT 中心可以通过（后述资源的）公共终结点连接到 Azure blob 存储、事件中心、服务总线资源，以进行[消息路由](./iot-hub-devguide-messages-d2c.md)、[文件上传](./iot-hub-devguide-file-upload.md)和[批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)操作。 默认情况下，将资源绑定到 VNet 会阻止与资源的连接。 因此，此配置会阻止 IoT 中心将数据发送给资源。 若要解决此问题，可通过“受信任的 Microsoft 服务”选项，启用从 IoT 中心资源到存储帐户、事件中心或服务总线资源的连接。

### <a name="turn-on-managed-identity-for-iot-hub"></a>为 IoT 中心启用托管标识

若要允许其他服务能够查找到 IoT 中心（作为受信任的 Microsoft 服务），该中心必须具有系统分配的托管标识。

1. 在 IoT 中心门户中导航到“标识”

1. 在“状态”下，选择“开”，然后单击“保存”  。

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="屏幕截图显示如何为 IoT 中心启用托管标识":::

### <a name="pricing-for-managed-identity"></a>托管标识的定价

受信任的 Microsoft 第一方服务异常功能是免费的。 配置存储帐户、事件中心或服务总线资源是单独收费的。

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>指向用于路由的存储帐户终结点的出口连接

IoT 中心可将消息路由到客户拥有的存储帐户。 为了允许路由功能在启用了防火墙限制的情况下访问存储帐户，IoT 中心需要具有[托管标识](#turn-on-managed-identity-for-iot-hub)。 预配托管标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，用于访问你的存储帐户。

1. 在 Azure 门户中，导航到存储帐户的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。

2. 为“角色”选择“存储 Blob 数据参与者”（[而不是“参与者”或“存储帐户参与者”角色](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)），为“分配访问权限给”选择“Azure AD 用户”、“组”或“服务主体”，并在下拉列表中选择你的 IoT 中心的资源名称  。 单击“保存”按钮  。

3. 导航到存储帐户中的“防火墙和虚拟网络”选项卡，并启用“允许从所选网络进行访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问此存储帐户”复选框 。 单击“保存”按钮  。

4. 在 IoT 中心的“资源”页上，导航到“消息路由”选项卡。

5. 导航到“自定义终结点”部分，然后单击“添加” 。 选择“存储”作为终结点类型。

6. 在显示的页面上，为终结点提供名称，选择要在 blob 存储中使用的容器，提供编码，以及文件名格式。 选择“系统分配”作为存储终结点的“身份验证类型” 。 单击“创建”  按钮。

现在，已将自定义存储终结点设置为使用中心的系统分配的标识，且即使存在防火墙限制，它仍有权访问存储资源。 现在可以使用此终结点来设置路由规则。

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>指向用于路由的事件中心终结点的出口连接

可以将 IoT 中心配置为将消息路由到客户拥有的事件中心命名空间。 为了允许路由功能在启用了防火墙限制的情况下访问事件中心资源，IoT 中心需要具有托管标识。 创建托管标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，用于访问你的事件中心。

1. 在 Azure 门户中，导航到事件中心的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。

2. 为“角色”选择“事件中心数据发送方”，为“分配访问权限给”选择“Azure AD 用户”、“组”或“服务主体”，并在下拉列表中选择你的 IoT 中心的资源名称   。 单击“保存”按钮  。

3. 导航到事件中心中的“防火墙和虚拟网络”选项卡，并启用“允许从所选网络进行访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问事件中心”复选框 。 单击“保存”按钮  。

4. 在 IoT 中心的“资源”页上，导航到“消息路由”选项卡。

5. 导航到“自定义终结点”部分，然后单击“添加” 。 选择“事件中心”作为终结点类型。

6. 在显示的页面上，为终结点提供名称，选择事件中心命名空间和实例，然后单击“创建”按钮。

现在，已将自定义事件中心终结点设置为使用中心的系统分配的标识，且即使存在防火墙限制，它仍有权访问事件中心资源。 现在可以使用此终结点来设置路由规则。

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>指向用于路由的服务总线终结点的出口连接

可以将 IoT 中心配置为将消息路由到客户拥有的服务总线命名空间。 为了允许路由功能在启用了防火墙限制的情况下访问服务总线中心资源，IoT 中心需要具有托管标识。 预配托管标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，用于访问你的服务总线。

1. 在 Azure 门户中，导航到服务总线的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。

2. 为“角色”选择“服务总线数据发送方”，为“分配访问权限给”选择“Azure AD 用户”、“组”或“服务主体”，并在下拉列表中选择你的 IoT 中心的资源名称   。 单击“保存”按钮  。

3. 导航到服务总线中的“防火墙和虚拟网络”选项卡，并启用“允许从所选网络进行访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问此服务总线”复选框 。 单击“保存”按钮  。

4. 在 IoT 中心的“资源”页上，导航到“消息路由”选项卡。

5. 导航到“自定义终结点”部分，然后单击“添加” 。 选择“服务总线队列”或“服务总线主题”（如果适用）作为终结点类型 。

6. 在显示的页面上，为终结点提供名称，选择服务总线的命名空间和队列或主题（如果适用）。 单击“创建”  按钮。

现在，已将自定义服务总线终结点设置为使用中心的系统分配的标识，且即使存在防火墙限制，它仍有权访问服务总线资源。 现在可以使用此终结点来设置路由规则。

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>指向用于上传文件的存储帐户的出口连接

IoT 中心的文件上传功能允许设备将文件上传到客户拥有的存储帐户。 若要正常上传文件，设备和 IoT 中心都需要连接到存储帐户。 如果存储帐户上存在防火墙限制，你的设备需要使用任何支持的存储帐户机制（包括[专用终结点](../private-link/create-private-endpoint-storage-portal.md)、[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)或[直接防火墙配置](../storage/common/storage-network-security.md)）来实现连接。 同样，如果存储帐户上存在防火墙限制，需要将 IoT 中心配置为通过受信任的 Microsoft 服务异常功能来访问存储资源。 出于此目的，IoT 中心必须具有一个托管标识。 预配托管标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，用于访问你的存储帐户。

1. 在 Azure 门户中，导航到存储帐户的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。

2. 为“角色”选择“存储 Blob 数据参与者”（[而不是“参与者”或“存储帐户参与者”角色](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)），为“分配访问权限给”选择“Azure AD 用户”、“组”或“服务主体”，并在下拉列表中选择你的 IoT 中心的资源名称  。 单击“保存”按钮  。

3. 导航到存储帐户中的“防火墙和虚拟网络”选项卡，并启用“允许从所选网络进行访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问此存储帐户”复选框 。 单击“保存”按钮  。

4. 在 IoT 中心的“资源”页上，导航到“文件上传”选项卡。

5. 在显示的页面上，选择要在 blob 存储中使用的容器，根据需要配置“文件通知设置”、“SAS TTL”、“默认 TT”和“最大传送计数”   。 选择“系统分配”作为存储终结点的“身份验证类型” 。 单击“创建”  按钮。

现在，已将用于上传文件的存储终结点设置为使用中心的系统分配的标识，且即使存在防火墙限制，它仍有权访问存储资源。

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>指向用于批量设备导入/导出的存储帐户的出口连接

IoT 中心支持从/向客户提供的存储 blob 批量[导入/导出](./iot-hub-bulk-identity-mgmt.md)设备的信息。 若要正常使用批量导入/导出功能，设备和 IoT 中心都需要连接到存储帐户。

若要使用此功，需要从 IoT 中心连接到存储帐户。 为了在启用了防火墙限制的情况下访问服务总线资源，IoT 中心需要具有托管标识。 预配托管标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，用于访问你的服务总线。

1. 在 Azure 门户中，导航到存储帐户的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。

2. 为“角色”选择“存储 Blob 数据参与者”（[而不是“参与者”或“存储帐户参与者”角色](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)），为“分配访问权限给”选择“Azure AD 用户”、“组”或“服务主体”，并在下拉列表中选择你的 IoT 中心的资源名称  。 单击“保存”按钮  。

3. 导航到存储帐户中的“防火墙和虚拟网络”选项卡，并启用“允许从所选网络进行访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问此存储帐户”复选框 。 单击“保存”按钮  。

现在可以使用用于[创建导入导出作业](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs)的 Azure IoT REST API 了解有关如何使用批量导入/导出功能的信息。 需要在请求正文中提供 `storageAuthenticationType="identityBased"`，并分别使用 `inputBlobContainerUri="https://..."` 和 `outputBlobContainerUri="https://..."` 作为存储帐户的输入和输出 URL。

Azure IoT 中心 SDK 也支持在服务客户端的注册表管理器中使用此功能。 以下代码段演示如何在使用 C# SDK 时启动导入或导出作业。

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

将此版本的 Azure IoT SDK 与虚拟网络的 C#、Java 和 Node.js 支持配合使用：

1. 创建一个名为 `EnableStorageIdentity` 的环境变量，并将其值设置为 `1`。

2. 下载 SDK：[Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
对于 Python，可从 GitHub 下载功能有限的版本。

1. 导航到[GitHub 发布页](https://aka.ms/vnetpythonsdk)。

2. 下载以下文件，该文件可在“资产”标题下的“发布”页面底部找到。
    > azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl

3. 打开终端并导航到下载的文件所在的文件夹。

4. 运行以下命令以安装支持虚拟网络的 Python 服务 SDK：
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>后续步骤

使用以下链接详细了解 IoT 中心功能：

* [消息路由](./iot-hub-devguide-messages-d2c.md)
* [文件上传](./iot-hub-devguide-file-upload.md)
* [批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md) 
