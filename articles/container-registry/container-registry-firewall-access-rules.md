---
title: 防火墙访问规则
description: 通过允许访问（"允许列表"） REST API 和存储终结点域名或服务特定的 IP 地址范围，配置从防火墙后面访问 Azure 容器注册表的规则。
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168021"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>配置规则以访问防火墙后面的 Azure 容器注册表

本文介绍如何在防火墙上配置规则，以允许访问 Azure 容器注册表。 例如，防火墙或代理服务器后面的 Azure IoT Edge 设备可能需要访问容器注册表才能请求容器映像。 或者，本地网络中的锁定服务器可能需要访问来推送映像。

如果只是想要在 Azure 虚拟网络或公共 IP 地址范围内的容器注册表上配置入站网络访问规则，请参阅[限制从虚拟网络访问 azure 容器注册表](container-registry-vnet.md)。

## <a name="about-registry-endpoints"></a>关于注册表终结点

若要将映像或其他项目请求或推送到 Azure 容器注册表，需要使用两个不同终结点的客户端（例如 Docker 后台程序）进行交互。

* **注册表 REST API 终结点**-身份验证和注册表管理操作通过注册表的公共 REST API 终结点进行处理。 此终结点是注册表的登录服务器名称或关联的 IP 地址范围。 

* **存储终结点**-azure 代表每个注册表在 azure 存储帐户中[分配 blob 存储](container-registry-storage.md)，以管理容器映像和其他项目的数据。 当客户端访问 Azure 容器注册表中的映像层时，它会使用注册表提供的存储帐户终结点发出请求。

如果你的注册表是[异地复制](container-registry-geo-replication.md)的，则客户端可能需要与特定区域或多个复制区域中的 REST 终结点和存储终结点进行交互。

## <a name="allow-access-to-rest-and-storage-domain-names"></a>允许访问 REST 和存储域名

* **REST 终结点**-允许访问完全限定的注册表登录服务器名称，如 `myregistry.azurecr.io`
* **存储（数据）终结点**-允许使用通配符 `*.blob.core.windows.net` 访问所有 Azure blob 存储帐户


## <a name="allow-access-by-ip-address-range"></a>允许按 IP 地址范围进行访问

如果你的组织具有仅允许访问特定 IP 地址或地址范围的策略，请下载[AZURE IP 范围和服务标记–公有云](https://www.microsoft.com/download/details.aspx?id=56519)。

若要查找需要允许访问的 ACR REST 终结点 IP 范围，请在 JSON 文件中搜索**AzureContainerRegistry** 。

> [!IMPORTANT]
> Azure 服务的 IP 地址范围可以更改，更新每周发布一次。 定期下载 JSON 文件，并在访问规则中进行必要的更新。 如果你的方案涉及在 Azure 虚拟网络中配置网络安全组规则以访问 Azure 容器注册表，请改用**AzureContainerRegistry** [服务标记](#allow-access-by-service-tag)。
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

## <a name="configure-client-firewall-rules-for-mcr"></a>配置 MCR 的客户端防火墙规则

如果需要从防火墙后面访问 Microsoft 容器注册表（MCR），请参阅配置[MCR 客户端防火墙规则](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)指南。 MCR 是所有 Microsoft 发布的 docker 映像的主注册表，例如 Windows Server 映像。

## <a name="next-steps"></a>后续步骤

* 了解[网络安全的 Azure 最佳实践](../security/fundamentals/network-best-practices.md)

* 详细了解 Azure 虚拟网络中的[安全组](/azure/virtual-network/security-overview)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

