---
title: 对虚拟网络的 Azure IoT 中心支持
description: 如何使用 IoT 中心虚拟网络连接模式
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501431"
---
# <a name="iot-hub-support-for-virtual-networks"></a>对虚拟网络的 IoT 中心支持

本文介绍了 VNET 连接模式，并详细介绍了如何通过客户拥有的 Azure VNET 将专用连接体验设置为 IoT 中心。

> [!NOTE]
> 本文中描述的 IoT 中心功能目前可用于[以下区域使用托管服务标识创建的](#create-an-iot-hub-with-managed-service-identity)IoT 中心：美国东部、美国中南部和美国西部 2。


## <a name="introduction"></a>介绍

默认情况下，IoT 中心主机名映射到具有 Internet 公开可路由 IP 地址的公共终结点。 如下图所示，此 IoT 中心公共终结点由不同客户拥有的集线器共享，IoT 设备可通过广域网络和本地网络访问。

几个 IoT 中心功能（包括[消息路由](./iot-hub-devguide-messages-d2c.md)、[文件上载](./iot-hub-devguide-file-upload.md)和[批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)）同样需要通过其公共终结点从 IoT 中心连接到客户拥有的 Azure 资源。 如下图所示，这些连接路径共同构成了从 IoT 中心到客户资源的出口流量。
![IoT 中心公共终结点](./media/virtual-network-support/public-endpoint.png)


出于多种原因，客户可能希望通过他们拥有和运营的 VNET 限制与其 Azure 资源（包括 IoT 中心）的连接。 这些原因包括：

* 通过网络级隔离为 IoT 中心引入其他安全层，防止通过公共 Internet 向集线器提供连接性暴露。

* 从本地网络资产中启用专用连接体验，确保数据和流量直接传输到 Azure 骨干网。

* 防止来自敏感本地网络的渗漏攻击。 

* 使用[专用终结点](../private-link/private-endpoint-overview.md)遵循已建立的 Azure 范围连接模式。


本文介绍如何使用[专用终结点](../private-link/private-endpoint-overview.md)实现与 IoT 中心的入口连接，例如使用 Azure 受信任的第一方服务例外从 IoT 中心到其他 Azure 资源进行出口连接。


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>使用专用终结点进入 IoT 中心

专用终结点是在客户拥有的 VNET 中分配的专用 IP 地址，可通过该地址到达 Azure 资源。 通过为 IoT 中心设置专用终结点，您将能够允许在 VNET 内运行的服务到达 IoT 中心，而无需将流量发送到 IoT 中心的公共终结点。 同样，在本地运行的设备可以使用[虚拟专用网络 （VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)专用对等互连来获得与 Azure 中的 VNET 的连接，然后连接到 IoT 中心（通过其专用终结点）。 因此，希望限制与 IoT 中心公共终结点的连接（或可能完全阻止它）的客户可以通过使用[IoT Hub 防火墙规则](./iot-hub-ip-filtering.md)来实现此目标，同时使用专用终结点保留与其中心的连接。

> [!NOTE]
> 此设置的主要重点是本地网络内的设备。 对于部署在广域网络中的设备，不建议进行此设置。

![IoT 中心公共终结点](./media/virtual-network-support/virtual-network-ingress.png)

在继续之前，请确保满足以下先决条件：

* 您的 IoT 中心必须预配[托管服务标识](#create-an-iot-hub-with-managed-service-identity)。

* 您的 IoT 中心必须预配到[受支持的区域](#regional-availability-private-endpoints)之一。

* 您已预配 Azure VNET 与将在其中创建专用终结点的子网。 有关详细信息[，请参阅使用 Azure CLI 创建虚拟网络](../virtual-network/quick-create-cli.md)。

* 对于在本地网络内运行的设备，设置[虚拟专用网络 （VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或到 Azure VNET 的[专用](https://azure.microsoft.com/services/expressroute/)对等互连。


### <a name="regional-availability-private-endpoints"></a>区域可用性（专用终结点）

在以下区域中创建的 IoT 中心中支持的私人终结点：

* 美国东部

* 美国中南部

* 美国西部 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>为 IoT 中心入口设置专用终结点

要设置专用终结点，请按照以下步骤操作：

1. 运行以下 Azure CLI 命令以使用订阅重新注册 Azure IoT 中心提供程序：

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. 导航到 IoT 中心门户中的 **"专用终结点连接**"选项卡（此选项卡仅在[受支持区域](#regional-availability-private-endpoints)中的 IoT 中心中可用），然后单击**+** 该符号添加新专用终结点。

3. 提供订阅、资源组、名称和区域以在 中创建新的专用终结点（理想情况下，私有终结点应在与中心相同的区域中创建;有关详细信息，请参阅[区域可用性部分](#regional-availability-private-endpoints)）。

4. 单击 **"下一步：资源**"，并为 IoT 中心资源提供订阅，然后选择 **"Microsoft.Devices/IotHubs"** 作为资源类型，选择 IoT 中心名称作为**资源**，**将 iotHub**作为目标子资源。

5. 单击"**下一步：配置**"并提供虚拟网络和子网以在 中创建专用终结点。 如果需要，请选择要与 Azure 专用 DNS 区域集成的选项。

6. 单击 **"下一步："标记**"，并可选择为资源提供任何标记。

7. 单击"**审阅 + 创建**"以创建专用终结点资源。


### <a name="pricing-private-endpoints"></a>定价（专用终结点）

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>从 IoT 中心到其他 Azure 资源的出口连接

IoT 中心需要访问 Azure Blob 存储、事件中心、[消息路由](./iot-hub-devguide-messages-d2c.md)的服务总线资源、[文件上载](./iot-hub-devguide-file-upload.md)和[批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)，这通常发生在资源的公共终结点上。 如果将存储帐户、事件中心或服务总线资源绑定到 VNET，则建议的配置将阻止默认情况下与该资源的连接。 因此，这将妨碍 IoT 中心需要访问这些资源的功能。

为了缓解这种情况，您需要通过**Azure 第一方受信任服务**选项启用从 IoT 中心资源到存储帐户、事件中心或服务总线资源的连接。

先决条件如下所述：

* 您的 IoT 中心必须预配到[受支持的区域](#regional-availability-trusted-microsoft-first-party-services)之一。

* 必须在中心预配时为 IoT 中心分配托管服务标识。 按照有关如何[创建具有托管服务标识的集线器的说明](#create-an-iot-hub-with-managed-service-identity)。


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>区域可用性（受信任的 Microsoft 第一方服务）

Azure 受信任的第一方服务例外，以绕过对 Azure 存储、事件中心和服务总线资源的防火墙限制，仅在以下区域中支持 IoT 中心：

* 美国东部

* 美国中南部

* 美国西部 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>定价（受信任的 Microsoft 第一方服务）

受信任的 Microsoft 第一方服务例外功能在[受支持区域](#regional-availability-trusted-microsoft-first-party-services)中的 IoT 中心是免费的。 预配存储帐户、事件中心或服务总线资源的费用单独适用。


### <a name="create-an-iot-hub-with-managed-service-identity"></a>使用托管服务标识创建 IoT 中心

托管服务标识可以在资源调配时间分配给您的中心（现有中心当前不支持此功能），这需要 IoT 中心使用 TLS 1.2 作为最小版本。 为此，您需要使用下面的 ARM 资源模板：

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
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
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
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
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
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
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

替换资源`name`的值 后，`location``SKU.name`和`SKU.tier`， 可以使用 Azure CLI 使用：

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

创建资源后，可以使用 Azure CLI 检索分配给中心的托管服务标识：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

预配具有托管服务标识的 IoT 中心后，请按照相应的部分将路由终结点设置为[存储帐户](#egress-connectivity-to-storage-account-endpoints-for-routing)、[事件中心](#egress-connectivity-to-event-hubs-endpoints-for-routing)[和服务总线](#egress-connectivity-to-service-bus-endpoints-for-routing)资源，或配置[文件上载](#egress-connectivity-to-storage-accounts-for-file-upload)和[批量设备导入/导出](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)。


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>与存储帐户终结点的连接进行路由

可以配置 IoT 中心将消息路由到客户拥有的存储帐户。 要允许路由功能在防火墙限制到位时访问存储帐户，IoT 中心需要具有托管服务标识（请参阅如何[创建具有托管服务标识的集线器](#create-an-iot-hub-with-managed-service-identity)）。 预配托管服务标识后，按照以下步骤向中心的资源标识授予 RBAC 访问存储帐户的权限。

1. 在 Azure 门户中，导航到存储帐户的访问**控制 （IAM）** 选项卡，然后单击"**添加角色分配**"部分下的 **"添加**"。

2. 选择**存储 Blob 数据参与者**作为**角色****、Azure AD 用户、组或服务主体****，以分配对**IoT 中心的资源名称的访问权限，并在下拉列表中选择 IoT 中心的资源名称。 单击“保存”**** 按钮。

3. 导航到存储帐户中的 **"防火墙"和"虚拟网络**"选项卡，并启用 **"允许从选定的网络访问**"选项。 在 **"例外"** 列表下，选中 **"允许受信任的 Microsoft 服务访问此存储帐户**"复选框。 单击“保存”**** 按钮。

4. 在 IoT 中心的资源页面上，导航到**消息路由**选项卡。

5. 导航到**自定义终结点**部分，然后单击"**添加**"。 选择 **"存储**"作为终结点类型。

6. 在显示的页面上，为终结点提供名称，选择要在 Blob 存储中使用容器、提供编码和文件名格式。 选择"为存储终结点的**身份验证类型****分配的系统**"。 单击“创建”**** 按钮。

现在，自定义存储终结点设置为使用集线器的系统分配标识，并且尽管它具有防火墙限制，但它仍有权访问存储资源。 现在，您可以使用此终结点设置路由规则。


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>到事件中心终结点进行路由的入侵连接

可以配置 IoT 中心将消息路由到客户拥有的事件中心命名空间。 要允许路由功能在防火墙限制到位时访问事件中心资源，IoT 中心需要具有托管服务标识（请参阅如何[创建具有托管服务标识的集线器](#create-an-iot-hub-with-managed-service-identity)）。 预配托管服务标识后，按照以下步骤向中心的资源标识授予 RBAC 访问事件中心的权限。

1. 在 Azure 门户中，导航到事件中心**访问控制 （IAM）** 选项卡，然后单击"**添加角色分配**"部分下的 **"添加**"。

2. 选择**事件中心数据发送方**作为**角色****、Azure AD 用户、组或服务主体**，**以分配对**IoT 中心的资源名称的访问权限，并在下拉列表中选择 IoT 中心的资源名称。 单击“保存”**** 按钮。

3. 导航到事件中心中的 **"防火墙"和"虚拟网络**"选项卡，并启用 **"允许从选定的网络访问**"选项。 在 **"例外"** 列表下，选中"**允许受信任的 Microsoft 服务访问事件中心**"复选框。 单击“保存”**** 按钮。

4. 在 IoT 中心的资源页面上，导航到**消息路由**选项卡。

5. 导航到**自定义终结点**部分，然后单击"**添加**"。 选择**事件中心**作为终结点类型。

6. 在显示的页面上，为终结点提供名称，选择事件中心命名空间和实例，然后单击"**创建**"按钮。

现在，自定义事件中心终结点设置为使用集线器的系统分配的标识，并且尽管它具有防火墙限制，它仍有权访问事件中心资源。 现在，您可以使用此终结点设置路由规则。


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>与用于路由的服务总线终结点的进出连接

可以配置 IoT 中心将消息路由到客户拥有的服务总线命名空间。 要允许路由功能在防火墙限制到位时访问服务总线资源，IoT 中心需要具有托管服务标识（请参阅如何[创建具有托管服务标识的集线器](#create-an-iot-hub-with-managed-service-identity)）。 预配托管服务标识后，按照以下步骤向中心的资源标识授予 RBAC 访问服务总线的权限。

1. 在 Azure 门户中，导航到服务总线的访问**控制 （IAM）** 选项卡，然后单击"**添加角色分配**"部分下的 **"添加**"。

2. 选择**服务总线数据发送器**作为**角色****、Azure AD 用户、组或服务主体****，以分配对**IoT 中心的资源名称的访问权限，并在下拉列表中选择 IoT 中心的资源名称。 单击“保存”**** 按钮。

3. 导航到服务总线中的**防火墙和虚拟网络**选项卡，并启用 **"允许从选定的网络访问**"选项。 在 **"例外"** 列表下，选中 **"允许受信任的 Microsoft 服务访问此服务总线**"复选框。 单击“保存”**** 按钮。

4. 在 IoT 中心的资源页面上，导航到**消息路由**选项卡。

5. 导航到**自定义终结点**部分，然后单击"**添加**"。 选择**服务总线队列**或服务**总线主题**（如适用）作为终结点类型。

6. 在显示的页面上，为终结点提供名称，选择服务总线的命名空间和队列或主题（如适用）。 单击“创建”**** 按钮。

现在，自定义服务总线终结点设置为使用集线器的系统分配的标识，并且尽管它具有防火墙限制，它仍有权访问服务总线资源。 现在，您可以使用此终结点设置路由规则。


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>文件上载与存储帐户的入侵连接

IoT 中心的文件上传功能允许设备将文件上载到客户拥有的存储帐户。 为了允许文件上载工作，设备和 IoT 中心都需要连接到存储帐户。 如果存储帐户上设置了防火墙限制，您的设备需要使用任何受支持的存储帐户机制（包括[专用终结点](../private-link/create-private-endpoint-storage-portal.md)、[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)或[直接防火墙配置](../storage/common/storage-network-security.md)）来获得连接。 同样，如果存储帐户上设置了防火墙限制，则需要将 IoT 中心配置为通过受信任的 Microsoft 服务异常访问存储资源。 为此，IoT 中心必须具有托管服务标识（请参阅如何[创建具有托管服务标识的中心](#create-an-iot-hub-with-managed-service-identity)）。 预配托管服务标识后，按照以下步骤向中心的资源标识授予 RBAC 访问存储帐户的权限。

1. 在 Azure 门户中，导航到存储帐户的访问**控制 （IAM）** 选项卡，然后单击"**添加角色分配**"部分下的 **"添加**"。

2. 选择**存储 Blob 数据参与者**作为**角色****、Azure AD 用户、组或服务主体****，以分配对**IoT 中心的资源名称的访问权限，并在下拉列表中选择 IoT 中心的资源名称。 单击“保存”**** 按钮。

3. 导航到存储帐户中的 **"防火墙"和"虚拟网络**"选项卡，并启用 **"允许从选定的网络访问**"选项。 在 **"例外"** 列表下，选中 **"允许受信任的 Microsoft 服务访问此存储帐户**"复选框。 单击“保存”**** 按钮。

4. 在 IoT 中心的资源页面上，导航到**文件上载**选项卡。

5. 在显示的页面上，选择要在 blob 存储中使用的容器，根据需要配置**文件通知设置****、SAS TTL、****默认 TTL**和**最大传递计数**。 选择"为存储终结点的**身份验证类型****分配的系统**"。 单击“创建”**** 按钮。

现在，文件上载的存储终结点设置为使用集线器的系统分配的标识，并且尽管它具有防火墙限制，它仍有权访问存储资源。


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>批量设备导入/导出的与存储帐户的入侵连接

IoT 中心支持从/到客户提供的存储 Blob 批量[导入/导出](./iot-hub-bulk-identity-mgmt.md)设备信息的功能。 为了允许批量导入/导出功能正常工作，设备和 IoT 中心都需要连接到存储帐户。

此功能需要从 IoT 中心连接到存储帐户。 要在防火墙限制到位时访问服务总线资源，IoT 中心需要具有托管服务标识（请参阅如何[创建具有托管服务标识的集线器](#create-an-iot-hub-with-managed-service-identity)）。 预配托管服务标识后，按照以下步骤向中心的资源标识授予 RBAC 访问服务总线的权限。

1. 在 Azure 门户中，导航到存储帐户的访问**控制 （IAM）** 选项卡，然后单击"**添加角色分配**"部分下的 **"添加**"。

2. 选择**存储 Blob 数据参与者**作为**角色****、Azure AD 用户、组或服务主体****，以分配对**IoT 中心的资源名称的访问权限，并在下拉列表中选择 IoT 中心的资源名称。 单击“保存”**** 按钮。

3. 导航到存储帐户中的 **"防火墙"和"虚拟网络**"选项卡，并启用 **"允许从选定的网络访问**"选项。 在 **"例外"** 列表下，选中 **"允许受信任的 Microsoft 服务访问此存储帐户**"复选框。 单击“保存”**** 按钮。

现在，可以使用 Azure IoT REST API[创建导入导出作业](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs)，以获取有关如何使用批量导入/导出功能的信息。 请注意，您需要分别在请求正文`storageAuthenticationType="identityBased"`中提供 ，`inputBlobContainerUri="https://..."`并`outputBlobContainerUri="https://..."`分别用作存储帐户的输入和输出 URL。


Azure IoT 中心 SDK 在服务客户端的注册表管理器中也支持此功能。 以下代码段演示如何使用 C# SDK 启动导入作业或导出作业。

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


要使用此受区域限制的 Azure IoT SDK 版本，支持对 C#、Java 和 Node.js 的虚拟网络支持：

1. 创建名为`EnableStorageIdentity`的环境变量并将其值设置为`1`。

2. 下载 SDK：Java [Java](https://aka.ms/vnetjavasdk) | [C++](https://aka.ms/vnetcsharpsdk) | [节点.js](https://aka.ms/vnetnodesdk)
 
对于 Python，请从 GitHub 下载我们的有限版本。

1. 导航到[GitHub 发布页](https://aka.ms/vnetpythonsdk)。

2. 下载以下文件，您可以在标题下名为 **"资产**"的发布页面底部找到该文件。
    > *azure_iot_hub-2.2.0_limited-py2.py3-无-任何.whl*

3. 打开终端，使用下载的文件导航到文件夹。

4. 运行以下命令以安装支持虚拟网络的 Python 服务 SDK：
    > 点安装 ./azure_iot_hub-2.2.0_limited-py2.py3-无-任何.whl


## <a name="next-steps"></a>后续步骤

使用以下链接了解有关 IoT 中心功能的更多内容：

* [消息路由](./iot-hub-devguide-messages-d2c.md)
* [文件上传](./iot-hub-devguide-file-upload.md)
* [批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md) 
