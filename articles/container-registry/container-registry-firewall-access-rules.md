---
title: 防火墙访问规则
description: 通过允许访问（"允许列表"） REST API 和数据终结点域名或特定于服务的 IP 地址范围，配置从防火墙后面访问 Azure 容器注册表的规则。
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: b3560fe769a97c8d3a4e5a3580d42d7c0b3a3f08
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978342"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>配置规则以访问防火墙后面的 Azure 容器注册表

本文介绍如何在防火墙上配置规则，以允许访问 Azure 容器注册表。 例如，防火墙或代理服务器后面的 Azure IoT Edge 设备可能需要访问容器注册表才能请求容器映像。 或者，本地网络中的锁定服务器可能需要访问来推送映像。

如果只是想要在 Azure 虚拟网络中配置对容器注册表的入站网络访问，请参阅[配置 azure 容器注册表的 Azure 专用链接](container-registry-private-link.md)。

## <a name="about-registry-endpoints"></a>关于注册表终结点

若要将映像或其他项目请求或推送到 Azure 容器注册表，需要使用两个不同终结点的客户端（例如 Docker 后台程序）进行交互。 对于从防火墙后面访问注册表的客户端，需要为这两个终结点配置访问规则。

* **注册表 REST API 终结点**-身份验证和注册表管理操作通过注册表的公共 REST API 终结点进行处理。 此终结点是注册表的登录服务器名称。 示例：`myregistry.azurecr.io`

* **存储（数据）终结点**-azure 代表每个注册表在 azure 存储帐户中[分配 blob 存储](container-registry-storage.md)，以管理容器映像和其他项目的数据。 当客户端访问 Azure 容器注册表中的映像层时，它会使用注册表提供的存储帐户终结点发出请求。

如果你的注册表是[异地复制](container-registry-geo-replication.md)的，则客户端可能需要与特定区域或多个已复制区域中的数据终结点交互。

## <a name="allow-access-to-rest-and-data-endpoints"></a>允许访问 REST 和数据终结点

* **REST 终结点**-允许访问完全限定的注册表登录服务器名称、 `<registry-name>.azurecr.io`或关联的 IP 地址范围
* **存储（数据）终结点**-允许使用通配符`*.blob.core.windows.net`或关联的 IP 地址范围访问所有 Azure blob 存储帐户。
> [!NOTE]
> Azure 容器注册表介绍[专用数据终结点](#enable-dedicated-data-endpoints-preview)（预览版），使你可以将注册表存储的客户端防火墙规则紧密地作用域。 还可以选择使用窗体`<registry-name>.<region>.data.azurecr.io`在查找或复制注册表的所有区域中启用数据终结点。

## <a name="allow-access-by-ip-address-range"></a>允许按 IP 地址范围进行访问

如果你的组织具有仅允许访问特定 IP 地址或地址范围的策略，请下载[AZURE IP 范围和服务标记–公有云](https://www.microsoft.com/download/details.aspx?id=56519)。

若要查找需要允许访问的 ACR REST 终结点 IP 范围，请在 JSON 文件中搜索**AzureContainerRegistry** 。

> [!IMPORTANT]
> Azure 服务的 IP 地址范围可以更改，更新每周发布一次。 定期下载 JSON 文件，并在访问规则中进行必要的更新。 如果你的方案涉及在 Azure 虚拟网络中配置网络安全组规则，或者使用 Azure 防火墙，请改为使用**AzureContainerRegistry** [服务标记](#allow-access-by-service-tag)。
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

搜索特定的区域，例如**AzureContainerRegistry. AustraliaEast**。

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

搜索特定的区域，如**AustraliaCentral**。

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

在 Azure 虚拟网络中，使用网络安全规则筛选从虚拟机等资源到容器注册表的流量。 若要简化 Azure 网络规则的创建，请使用**AzureContainerRegistry** [服务标记](../virtual-network/security-overview.md#service-tags)。 服务标记代表一组用于全局或每个 Azure 区域访问 Azure 服务的 IP 地址前缀。 当地址更改时，将自动更新标记。 

例如，创建包含目标**AzureContainerRegistry**的出站网络安全组规则，以允许流量流向 Azure 容器注册表。 若要仅允许在特定区域中访问服务标记，请按以下格式指定区域： **AzureContainerRegistry**。[*region name*]。

## <a name="enable-dedicated-data-endpoints-preview"></a>启用专用数据终结点（预览）

> [!WARNING]
> 如果以前配置了客户端防火墙对现有`*.blob.core.windows.net`终结点的访问权限，则切换到专用数据终结点会影响客户端连接，从而导致请求失败。 若要确保客户端具有一致的访问权限，请将新的数据终结点规则添加到客户端防火墙规则。 完成后，使用 Azure CLI 或其他工具为你的注册表启用专用数据终结点。

专用数据终结点是**高级**容器注册表服务层的一项可选功能。 有关注册表服务层和限制的信息，请参阅[Azure 容器注册表层](container-registry-skus.md)。 若要使用 Azure CLI 启用数据终结点，请使用 Azure CLI 版本2.4.0 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

下面的[az acr update][az-acr-update]命令启用注册表*myregistry*上的专用数据终结点。 出于演示目的，假设在两个区域复制注册表：

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

数据终结点使用区域模式`<registry-name>.<region>.data.azurecr.io`。 若要查看数据终结点，请使用[az acr show-终结点][az-acr-show-endpoints]命令：

```azurecli
az acr show-endpoints --name myregistry
```

输出：

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

如果需要从防火墙后面访问 Microsoft 容器注册表（MCR），请参阅配置[MCR 客户端防火墙规则](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)指南。 MCR 是所有 Microsoft 发布的 docker 映像的主注册表，例如 Windows Server 映像。

## <a name="next-steps"></a>后续步骤

* 了解[网络安全的 Azure 最佳实践](../security/fundamentals/network-best-practices.md)

* 详细了解 Azure 虚拟网络中的[安全组](/azure/virtual-network/security-overview)

* 了解有关 Azure 容器注册表[专用数据终结点](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/)的详细信息



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints

