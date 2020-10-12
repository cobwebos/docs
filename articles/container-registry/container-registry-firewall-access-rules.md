---
title: 防火墙访问规则
description: 通过允许访问（“加入允许列表”）REST API 和数据终结点域名或特定于服务的 IP 地址范围，来配置规则以访问防火墙后的 Azure 容器注册表。
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 679dbcaf30653b855d35825f94e93f87ac68c322
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86246973"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>配置规则以访问防火墙后的 Azure 容器注册表

本文介绍如何在防火墙上配置规则，以允许访问 Azure 容器注册表。 例如，防火墙或代理服务器后面的 Azure IoT Edge 设备可能需要访问容器注册表以拉取容器映像。 或者，本地网络中的锁定服务器可能需要访问权限以推送映像。

相反，如果只是要在 Azure 虚拟网络中配置对容器注册表的入站网络访问，请参阅[为 Azure 容器注册表配置 Azure 专用链接](container-registry-private-link.md)。

## <a name="about-registry-endpoints"></a>关于注册表终结点

若要将映像或其他项目请求或推送到 Azure 容器注册表，客户端（例如 Docker 后台程序）需要通过 HTTPS 与两个不同的终结点进行交互。 对于从防火墙后面访问注册表的客户端，需要为两个终结点配置访问规则。 通过端口 443 可以访问两个终结点。

* 注册表 REST API 终结点 - 身份验证和注册表管理操作通过注册表的公共 REST API 终结点进行处理。 此终结点是注册表的登录服务器名称。 示例： `myregistry.azurecr.io`

* **存储（数据）终结点** - Azure 代表每个注册表在 Azure 存储帐户中[分配 blob 存储](container-registry-storage.md)，以管理容器映像和其他项目的数据。 当客户端访问 Azure 容器注册表中的映像层时，它会使用注册表提供的存储帐户终结点发出请求。

如果你的注册表是[异地复制](container-registry-geo-replication.md)的，则客户端可能需要与特定区域或多个已复制区域中的数据终结点交互。

## <a name="allow-access-to-rest-and-data-endpoints"></a>允许访问 REST 和数据终结点

* **REST 终结点** - 允许访问完全限定的注册表登录服务器名称、`<registry-name>.azurecr.io` 或关联的 IP 地址范围
* 存储（数据）终结点 - 允许访问所有使用通配符 `*.blob.core.windows.net` 或关联的 IP 地址范围的 Azure blob 存储帐户。
> [!NOTE]
> Azure 容器注册表即将引入[专用数据终结点](#enable-dedicated-data-endpoints)，这样你可以为注册表存储严格限定客户端防火墙规则的范围。 视需要使用格式 `<registry-name>.<region>.data.azurecr.io` 在其中找到或复制注册表的所有区域中启用数据终结点。

## <a name="allow-access-by-ip-address-range"></a>允许按 IP 地址范围访问

如果组织有只允许访问特定 IP 地址或地址范围的策略，请下载 [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)。

若要查找需要允许访问的 ACR REST 终结点 IP 范围，请在 JSON 文件中搜索“AzureContainerRegistry”。

> [!IMPORTANT]
> Azure 服务的 IP 地址范围可以更改，每周发布一次更新。 定期下载 JSON 文件，并在访问规则中进行必要的更新。 如果你的方案涉及在 Azure 虚拟网络中配置网络安全组规则，或者使用 Azure 防火墙，请改为使用 AzureContainerRegistry [服务标记](#allow-access-by-service-tag)。
>

### <a name="rest-ip-addresses-for-all-regions"></a>所有区域的 REST IP 地址

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>特定区域的 REST IP 地址

搜索特定区域（如“AzureContainerRegistry.AustraliaEast”）。

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>所有区域的存储 IP 地址

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>特定区域的存储 IP 地址

搜索特定区域（如“Storage.AustraliaCentral”）。

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>允许按服务标记访问

在 Azure 虚拟网络中，使用网络安全规则筛选从虚拟机等资源到容器注册表的流量。 若要简化 Azure 网络规则的创建，请使用 AzureContainerRegistry [服务标记](../virtual-network/security-overview.md#service-tags)。 服务标记代表一组用于全局或每个 Azure 区域访问 Azure 服务的 IP 地址前缀。 当地址更改时，将自动更新标记。 

例如，创建包含目标 AzureContainerRegistry 的出站网络安全组规则，以允许流量流向 Azure 容器注册表。 若要只允许在特定区域中访问服务标记，请按以下格式指定区域：AzureContainerRegistry.[区域名称]。

## <a name="enable-dedicated-data-endpoints"></a>启用专用数据终结点 

> [!WARNING]
> 如果以前配置了对现有 `*.blob.core.windows.net` 终结点的客户端防火墙访问，切换到专用数据终结点会影响客户端连接，进而导致拉取失败。 若要确保客户端具有一致的访问权限，请将新的数据终结点规则添加到客户端防火墙规则。 完成后，使用 Azure CLI 或其他工具为你的注册表启用专用数据终结点。

专用数据终结点是高级容器注册表服务层的可选功能。 若要了解注册表服务层和限制，请参阅 [Azure 容器注册表服务层](container-registry-skus.md)。 

可以使用 Azure 门户或 Azure CLI 启用专用数据终结点。 数据终结点遵循区域模式 `<registry-name>.<region>.data.azurecr.io`。 在异地复制注册表中，启用数据终结点会在所有副本区域中启用终结点。

### <a name="portal"></a>门户

若要使用门户启用数据终结点，请执行以下操作：

1. 转到容器注册表。
1. 依次选择“网络” > “公共访问”。
1. 选中“启用专用数据终结点”复选框。
1. 选择“保存”。

此时，一个或多个数据终结点显示在门户中。

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="门户中的专用数据终结点":::

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 启用数据终结点，请使用 Azure CLI 版本 2.4.0 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

以下 [az acr update][az-acr-update] 命令在注册表 myregistry 上启用专用数据终结点。 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

若要查看数据终结点，请运行 [az acr show-endpoints][az-acr-show-endpoints] 命令：

```azurecli
az acr show-endpoints --name myregistry
```

出于演示目的，输出显示两个区域终结点

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

为注册表设置专用数据终结点后，可以为数据终结点启用客户端防火墙访问规则。 为所有必需的注册表区域启用数据终结点访问规则。

## <a name="configure-client-firewall-rules-for-mcr"></a>配置 MCR 的客户端防火墙规则

如果需要访问防火墙后的 Microsoft 容器注册表 (MCR)，请参阅配置 [MCR 客户端防火墙规则](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)的指南。 MCR 是 Microsoft 发布的所有 docker 映像的主注册表，例如 Windows Server 映像。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure 网络安全最佳做法](../security/fundamentals/network-best-practices.md)

* 详细了解 Azure 虚拟网络中的[安全组](../virtual-network/security-overview.md)

* 详细了解如何为容器注册表设置[专用链接](container-registry-private-link.md)

* 详细了解 Azure 容器注册表的[专用数据终结点](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints
