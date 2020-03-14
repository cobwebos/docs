---
title: Azure IoT 中心对虚拟网络的支持
description: 如何将虚拟网络连接模式与 IoT 中心配合使用
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 1b250bee302cb305ee613010238283ceac4014ed
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375078"
---
# <a name="iot-hub-support-for-virtual-networks"></a>IoT 中心对虚拟网络的支持

本文介绍了 VNET 连接模式，并然后详细阐述如何通过客户拥有的 Azure VNET 为 IoT 中心设置专用连接体验。

> [!NOTE]
> 本文中所述的 IoT 中心功能当前适用于在以下区域创建的 IoT 中心：美国东部、美国中南部和美国西部2。


## <a name="introduction"></a>介绍

默认情况下，IoT 中心主机名映射到一个公共终结点，该终结点具有通过 Internet 通过公共方式路由的 IP 地址。 如下图所示，此 IoT 中心公共终结点在不同客户拥有的集线器之间共享，可由 IoT 设备通过广域网络以及本地网络进行访问。

许多 IoT 中心功能（包括[消息路由](./iot-hub-devguide-messages-d2c.md)、[文件上传](./iot-hub-devguide-file-upload.md)和[批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)）也需要从 IoT 中心连接到客户拥有的 Azure 资源（通过其公共终结点）。 如下所示，这些连接路径共同构成了从 IoT 中心到客户资源的出口流量。
![IoT 中心公共终结点](./media/virtual-network-support/public-endpoint.png)


由于多种原因，客户可能想要通过其拥有和操作的 VNET 限制其 Azure 资源（包括 IoT 中心）的连接。 这些原因包括：

* 通过在公共 Internet 上防止中心连接泄露，为 IoT 中心的网络级隔离引入附加的安全层。

* 通过本地网络资产实现专用连接体验可确保数据和流量直接传输到 Azure 主干网络。

* 阻止来自敏感本地网络的渗透攻击。 

* 遵循使用[专用终结点](../private-link/private-endpoint-overview.md)建立的 Azure 范围的连接模式。


本文介绍如何使用[专用终结点](../private-link/private-endpoint-overview.md)连接到 iot 中心来实现这些目标，如使用 Azure 受信任的第一方服务例外，将 iot 中心的传出连接到其他 Azure 资源。


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>使用专用终结点连接到 IoT 中心

专用终结点是在客户拥有的 VNET 中分配的专用 IP 地址，可通过该地址访问 Azure 资源。 通过让 IoT 中心拥有专用终结点，你将能够允许在 VNET 中运行的服务访问 IoT 中心，而无需将流量发送到 IoT 中心的公共终结点。 同样，在本地运行的设备可以使用[虚拟专用网络（VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)专用对等互连来连接到 Azure 中的 VNET，进而连接到 IoT 中心（通过其专用终结点）。 因此，希望将连接限制到 IoT 中心的公共终结点（或者可能完全阻止它）的客户可以通过使用[IoT 中心防火墙规则](./iot-hub-ip-filtering.md)来实现此目标，同时使用专用终结点将连接保留到其中心。

> [!NOTE]
> 此设置的主要重点在于本地网络中的设备。 对于在广域网络中部署的设备，不建议使用此设置。

![IoT 中心公共终结点](./media/virtual-network-support/virtual-network-ingress.png)

在继续操作之前，请确保满足以下先决条件：

* 必须在一个[受支持的区域](#regional-availability-private-endpoints)中预配 IoT 中心。

* 你已设置了一个 Azure VNET，其中包含将在其中创建专用终结点的子网。 有关更多详细信息，请参阅[使用 Azure CLI 创建虚拟网络](../virtual-network/quick-create-cli.md)。

* 对于在本地网络内部运行的设备，请在 Azure VNET 中设置[虚拟专用网络（VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)专用对等互连。


### <a name="regional-availability-private-endpoints"></a>区域可用性（专用终结点）

在以下区域创建的 IoT 中心支持的专用终结点：

* 美国东部

* 美国中南部

* 美国西部 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>为 IoT 中心入口设置专用终结点

若要设置专用终结点，请执行以下步骤：

1. 运行以下 Azure CLI 命令，将 Azure IoT 中心提供程序重新注册到你的订阅：

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. 导航到 IoT 中心门户中的 "**专用终结点连接**" 选项卡（此选项卡仅适用于[受支持区域](#regional-availability-private-endpoints)中的 IoT 中心），并单击 **+** "签名" 以添加新的专用终结点。

3. 提供订阅、资源组、名称和区域以在中创建新的专用终结点（理想情况下，应在中心所在的同一区域中创建专用终结点; 有关详细信息，请参阅[区域可用性部分](#regional-availability-private-endpoints)）。

4. 单击 "**下一步：资源**"，提供 IoT 中心资源的订阅，然后选择 **"IotHubs"** 作为资源类型、IoT 中心名称作为**资源**，并选择 " **iotHub** " 作为 "目标子资源"。

5. 单击 "**下一步：配置**" 并提供你的虚拟网络和子网，以在中创建专用终结点。 如果需要，请选择 "与 Azure 专用 DNS 区域集成" 选项。

6. 单击 "**下一步：标记**"，并根据需要提供资源的任何标记。

7. 单击 "**查看 + 创建**" 创建专用终结点资源。


### <a name="pricing-private-endpoints"></a>定价（专用终结点）

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>从 IoT 中心到其他 Azure 资源的传出连接

IoT 中心需要访问 Azure blob 存储、事件中心、服务总线资源，以便进行[消息路由](./iot-hub-devguide-messages-d2c.md)、[文件上传](./iot-hub-devguide-file-upload.md)和[批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)，这通常会通过资源的公共终结点进行。 如果将存储帐户、事件中心或服务总线资源绑定到 VNET，则默认情况下，建议的配置会阻止与资源的连接。 因此，这会妨碍 IoT 中心的功能，需要访问这些资源。

若要缓解这种情况，需要通过**Azure 第一方信任服务**选项启用从 IoT 中心资源到存储帐户、事件中心或服务总线资源的连接。

先决条件如下：

* 必须在一个[受支持的区域](#regional-availability-trusted-microsoft-first-party-services)中预配 IoT 中心。

* 必须在中心设置时为 IoT 中心分配托管服务标识。 按照如何[使用托管服务标识创建集线器](#create-a-hub-with-managed-service-identity)的说明进行操作。


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>区域可用性（受信任的 Microsoft 第一方服务）

Azure 受信任的第一方服务例外：绕过 Azure 存储的防火墙限制、事件中心和服务总线资源仅支持以下区域的 IoT 中心：

* 美国东部

* 美国中南部

* 美国西部 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>定价（受信任的 Microsoft 第一方服务）

受信任的 Microsoft 第一方服务例外功能在[受支持区域](#regional-availability-trusted-microsoft-first-party-services)的 IoT 中心内免费。 已设置的存储帐户、事件中心或服务总线资源的费用将单独应用。


### <a name="create-a-hub-with-managed-service-identity"></a>使用托管服务标识创建集线器

可在资源设置时向中心分配托管服务标识（现有中心目前不支持此功能）。 为此，需要使用以下 ARM 资源模板：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-03-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions>",
            "identity": { "type": "SystemAssigned" },
            "properties": { "minTlsVersion": "1.2" },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-02-01",
            "name": "updateIotHubWithKeyEncryptionKey",
            "dependsOn": [ "<provide-a-valid-resource-name>" ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "0.9.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Devices/IotHubs",
                            "apiVersion": "2020-03-01",
                            "name": "<provide-a-valid-resource-name>",
                            "location": "<any-of-supported-regions>",
                            "identity": { "type": "SystemAssigned" },
                            "properties": { "minTlsVersion": "1.2" },
                            "sku": {
                                "name": "<your-hubs-SKU-name>",
                                "tier": "<your-hubs-SKU-tier>",
                                "capacity": 1
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

将资源的值替换 `name`、`location`、`SKU.name` 和 `SKU.tier`后，可以使用 Azure CLI 来部署现有资源组中的资源，如下所示：

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

创建资源后，可以使用 Azure CLI 检索分配给中心的托管服务标识：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

预配具有托管服务标识的 IoT 中心后，请按照相应的部分设置路由终结点到[存储帐户](#egress-connectivity-to-storage-account-endpoints-for-routing)、[事件中心](#egress-connectivity-to-event-hubs-endpoints-for-routing)和[服务总线](#egress-connectivity-to-service-bus-endpoints-for-routing)资源，或配置[文件上传](#egress-connectivity-to-storage-accounts-for-file-upload)和[批量设备导入/导出](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)。


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>传出连接到存储帐户终结点以进行路由

可以将 IoT 中心配置为将消息路由到客户拥有的存储帐户。 若要在防火墙限制就绪时允许路由功能访问存储帐户，IoT 中心需要具有托管服务标识（请参阅如何[使用托管服务标识创建中心](#create-a-hub-with-managed-service-identity)）。 预配托管服务标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，以访问你的存储帐户。

1. 在 Azure 门户中，导航到存储帐户的 "**访问控制（IAM）** " 选项卡，然后单击 "**添加角色分配**" 部分下的 "**添加**"。

2. 选择 "**存储 Blob 数据参与者**" 作为 "**角色**"、 **Azure AD "用户"、"组" 或 "服务主体**" 作为 "**分配访问权限**"，然后在下拉列表中选择 IoT 中心的资源名称。 单击“保存”按钮。

3. 导航到存储帐户中的 "**防火墙和虚拟网络**" 选项卡，并启用 "**允许从所选网络访问**" 选项。 在 "**例外**" 列表中，选中 "**允许受信任的 Microsoft 服务访问此存储帐户**" 框。 单击“保存”按钮。

4. 在 IoT 中心的 "资源" 页上，导航到 "**消息路由**" 选项卡。

5. 导航到 "**自定义终结点**" 部分，然后单击 "**添加**"。 选择 "**存储**" 作为终结点类型。

6. 在显示的页面上，为终结点提供名称，选择要在 blob 存储中使用的容器，提供编码和文件名格式。 选择 "**系统已分配**为存储终结点的**身份验证类型**"。 单击“创建”按钮。

现在，已将自定义存储终结点设置为使用集线器的系统分配的标识，并且即使其防火墙受到限制，它仍有权访问存储资源。 你现在可以使用此终结点来设置路由规则。


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>用于路由的到事件中心终结点的传出连接

可以将 IoT 中心配置为将消息路由到客户拥有的事件中心命名空间。 为了允许路由功能在防火墙限制就绪时访问事件中心资源，IoT 中心需要具有托管服务标识（请参阅如何[使用托管服务标识创建中心](#create-a-hub-with-managed-service-identity)）。 预配托管服务标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，以访问你的事件中心。

1. 在 Azure 门户中，导航到 "事件中心**访问控制（IAM）** " 选项卡，然后单击 "**添加角色分配**" 部分下的 "**添加**"。

2. 选择**事件中心数据发送方**作为**角色**、 **Azure AD 用户、组或服务主体**作为**分配访问权限**，并在下拉列表中选择 IoT 中心的资源名称。 单击“保存”按钮。

3. 导航到事件中心中的 "**防火墙和虚拟网络**" 选项卡，并启用 "**允许从所选网络访问**" 选项。 在 "**例外**" 列表中，选中 "**允许受信任的 Microsoft 服务访问事件中心**" 框。 单击“保存”按钮。

4. 在 IoT 中心的 "资源" 页上，导航到 "**消息路由**" 选项卡。

5. 导航到 "**自定义终结点**" 部分，然后单击 "**添加**"。 选择 "**事件中心**" 作为终结点类型。

6. 在显示的页面上，为终结点提供名称，选择事件中心命名空间和实例，并单击 "**创建**" 按钮。

现在，已将自定义事件中心终结点设置为使用集线器系统分配的标识，并且它有权访问事件中心资源（尽管其防火墙受到限制）。 你现在可以使用此终结点来设置路由规则。


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>传出连接到服务总线终结点以进行路由

可以将 IoT 中心配置为将消息路由到客户拥有的服务总线命名空间。 若要允许路由功能在防火墙限制就绪时访问服务总线资源，IoT 中心需要具有托管服务标识（请参阅如何[使用托管服务标识创建中心](#create-a-hub-with-managed-service-identity)）。 预配托管服务标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，以访问你的服务总线。

1. 在 Azure 门户中，导航到服务总线的 "**访问控制（IAM）** " 选项卡，然后单击 "**添加角色分配**" 部分下的 "**添加**"。

2. 将**服务总线数据发送方**选择为 "**角色**"、 **Azure AD "用户"、"组" 或 "服务主体**" 作为 "**分配访问权限**"，然后在下拉列表中选择 IoT 中心的资源名称。 单击“保存”按钮。

3. 导航到服务总线中的 "**防火墙和虚拟网络**" 选项卡，并启用 "**允许从所选网络访问**" 选项。 在 "**例外**" 列表中，选中 "**允许受信任的 Microsoft 服务访问此服务总线**" 框。 单击“保存”按钮。

4. 在 IoT 中心的 "资源" 页上，导航到 "**消息路由**" 选项卡。

5. 导航到 "**自定义终结点**" 部分，然后单击 "**添加**"。 选择**服务总线队列**或**服务总线主题**（如果适用）作为终结点类型。

6. 在显示的页面上，为终结点提供名称，选择服务总线的命名空间和队列或主题（如果适用）。 单击“创建”按钮。

现在，已将自定义服务总线终结点设置为使用集线器的系统分配的标识，并且它有权访问服务总线资源（尽管其防火墙受到限制）。 你现在可以使用此终结点来设置路由规则。


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>用于文件上传的到存储帐户的传出连接

IoT 中心的文件上传功能允许设备将文件上传到客户拥有的存储帐户。 若要允许文件上传功能，设备和 IoT 中心都需要连接到存储帐户。 如果在存储帐户上设置了防火墙限制，则设备需要使用任何支持的存储帐户机制（包括[专用终结点](../private-link/create-private-endpoint-storage-portal.md)、[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)或[直接防火墙配置](../storage/common/storage-network-security.md)）来获取连接。 同样，如果在存储帐户上设置了防火墙限制，则需要将 IoT 中心配置为通过受信任的 Microsoft 服务异常访问存储资源。 出于此目的，IoT 中心必须具有托管服务标识（请参阅如何[使用托管服务标识创建中心](#create-a-hub-with-managed-service-identity)）。 预配托管服务标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，以访问你的存储帐户。

1. 在 Azure 门户中，导航到存储帐户的 "**访问控制（IAM）** " 选项卡，然后单击 "**添加角色分配**" 部分下的 "**添加**"。

2. 选择 "**存储 Blob 数据参与者**" 作为 "**角色**"、 **Azure AD "用户"、"组" 或 "服务主体**" 作为 "**分配访问权限**"，然后在下拉列表中选择 IoT 中心的资源名称。 单击“保存”按钮。

3. 导航到存储帐户中的 "**防火墙和虚拟网络**" 选项卡，并启用 "**允许从所选网络访问**" 选项。 在 "**例外**" 列表中，选中 "**允许受信任的 Microsoft 服务访问此存储帐户**" 框。 单击“保存”按钮。

4. 在 IoT 中心的 "资源" 页上，导航到 "**文件上传**" 选项卡。

5. 在显示的页面上，选择要在 blob 存储中使用的容器，根据需要配置**文件通知设置**、 **SAS TTL**、**默认 TTL**和**最大传递计数**。 选择 "**系统已分配**为存储终结点的**身份验证类型**"。 单击“创建”按钮。

现在，用于文件上传的存储终结点已设置为使用集线器的系统分配的标识，它有权访问存储资源（尽管其防火墙受到限制）。


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>用于批量设备导入/导出的到存储帐户的传出连接

IoT 中心支持从/向客户提供的存储 blob 批量[导入/导出](./iot-hub-bulk-identity-mgmt.md)设备信息的功能。 若要允许批量导入/导出功能正常工作，设备和 IoT 中心都需要连接到存储帐户。

此功能需要从 IoT 中心连接到存储帐户。 若要在防火墙限制就绪时访问服务总线资源，IoT 中心需要具有托管服务标识（请参阅如何[使用托管服务标识创建中心](#create-a-hub-with-managed-service-identity)）。 预配托管服务标识后，请按照以下步骤为你的中心的资源标识提供 RBAC 权限，以访问你的服务总线。

1. 在 Azure 门户中，导航到存储帐户的 "**访问控制（IAM）** " 选项卡，然后单击 "**添加角色分配**" 部分下的 "**添加**"。

2. 选择 "**存储 Blob 数据参与者**" 作为 "**角色**"、 **Azure AD "用户"、"组" 或 "服务主体**" 作为 "**分配访问权限**"，然后在下拉列表中选择 IoT 中心的资源名称。 单击“保存”按钮。

3. 导航到存储帐户中的 "**防火墙和虚拟网络**" 选项卡，并启用 "**允许从所选网络访问**" 选项。 在 "**例外**" 列表中，选中 "**允许受信任的 Microsoft 服务访问此存储帐户**" 框。 单击“保存”按钮。

你现在可以使用 Azure IoT REST API 来[创建导入导出作业](https://docs.microsoft.com/rest/api/iothub/jobclient/getimportexportjobs)，以获取有关如何使用批量导入/导出功能的信息。 请注意，你将需要在请求正文中提供 `storageAuthenticationType="identityBased"`，并分别使用 `inputBlobContainerUri="https://..."` 和 `outputBlobContainerUri="https://..."` 作为存储帐户的输入和输出 URL。


Azure IoT 中心 SDK 还在服务客户端的注册表管理器中支持此功能。 以下代码片段演示了如何使用C# SDK 启动导入作业或导出作业。

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


若要将此受区域限制版本的 Azure IoT Sdk 用于 VNET 支持C#、Java 和 node.js，请执行以下操作：

1. 创建一个名为 `EnableStorageIdentity` 的环境变量，并将其值设置为 `1`。

2. 下载 SDK：
    - > [Java](https://aka.ms/vnetjavasdk)
    - > [C#](https://aka.ms/vnetcsharsdk)
    - > [Node.js](https://aka.ms/vnetnodesdk)
 
对于 Python，请从 Github 下载我们的有限版本。

1. 导航到 Github 的 "[发布" 页](https://aka.ms/vnetpythonsdk)。

2. 下载以下文件，你将在 "发布" 页底部的 "**资产**" 标题下找到此文件。
    > *azure_iot_hub-2.2.0. limited-py2. py3-none-any. whl*

3. 打开终端并导航到下载的文件所在的文件夹。

4. 运行以下命令以安装支持 Vnet 的 Python 服务 SDK：
    > pip install azure_iot_hub. 2.2.0. limited-py2. py3-none-any. whl


## <a name="next-steps"></a>后续步骤

使用以下链接了解有关 IoT 中心功能的详细信息：

* [消息路由](./iot-hub-devguide-messages-d2c.md)
* [文件上传](./iot-hub-devguide-file-upload.md)
* [批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md) 