---
title: 如何为 Azure Cosmos 帐户配置 IP 防火墙
description: 了解如何配置 IP 访问控制策略，以对 Azure Cosmos DB 数据库帐户提供防火墙支持。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: d9a00bccb83fc60c96594ffacc5abde98c0f8470
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632571"
---
# <a name="how-to-configure-ip-firewall-for-your-azure-cosmos-account"></a>如何为 Azure Cosmos 帐户配置 IP 防火墙

可以使用 IP 防火墙保护存储在 Azure Cosmos 帐户中的数据。 Azure Cosmos DB 支持使用基于 IP 的访问控制来提供入站防火墙支持。 可以通过以下一种方式为 Azure Cosmos 帐户设置 IP 防火墙：

1. 通过 Azure 门户
2. 通过使用 Azure 资源管理器模板来以声明方式进行配置
3. 通过更新 ipRangeFilter 属性并借助 Azure CLI 或 Azure Powershell 来以编程方式进行配置。

## <a id="configure-ip-policy"></a>使用 Azure 门户配置 IP 防火墙

若要在 Azure 门户中设置 IP 访问控制策略，请导航到 Azure Cosmos DB 帐户页，在导航菜单中单击“防火墙和虚拟网络”，将“允许从以下位置访问”值更改为“选定的网络”，并单击“保存”。 

![此屏幕截图显示了如何在 Azure 门户中打开“防火墙”页](./media/how-to-configure-firewall/azure-portal-firewall.png)

启用 IP 访问控制后，可在 Azure 门户中指定 IP 地址和 IP 地址范围，并使用相应的开关来启用对其他 Azure 服务和 Azure 门户的访问。 以下各节提供了有关这些开关的详细信息。

> [!NOTE]
> 为 Azure Cosmos 帐户启用 IP 访问控制策略后，将拒绝从 IP 地址范围允许列表外部的计算机到 Azure Cosmos 帐户的所有请求。 此外，还会阻止通过门户浏览 Azure Cosmos DB 资源，以确保访问控制的完整性。

### <a name="allow-requests-from-the-azure-portal"></a>允许来自 Azure 门户的请求 

以编程的方式启用 IP 访问控制策略时，需将 Azure 门户的 IP 地址添加到 ipRangeFilter 属性以维持访问。 门户 IP 地址是：

|区域|IP 地址|
|------|----------|
|德国|51.4.229.218|
|中国|139.217.8.252|
|美国政府|52.244.48.71|
|除上述三个区域外的所有区域|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

要想允许访问 Azure 门户，可以选择“允许从 Azure 门户访问”选项，如以下屏幕截图所示： 

![此屏幕截图显示了如何启用对 Azure 门户的访问](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>允许来自全球的 Azure 数据中心或 Azure 中的其他源的请求

如果你通过不提供静态 IP 的服务（例如 Azure 流分析、Azure Functions 等）访问 Azure Cosmos 帐户，则仍可使用 IP 防火墙来限制访问。 若要允许从此类服务访问 Azure Cosmos 帐户，则必须启用防火墙设置。 要启用此防火墙设置，请将 IP 地址 - 0.0.0.0 添加到允许的 IP 地址列表。 0.0.0.0 限制从 Azure 数据中心 IP 范围到 Azure Cosmos 帐户的请求。 此设置不允许任何其他 IP 范围访问 Azure Cosmos 帐户。

> [!NOTE]
> 该选项将防火墙配置为允许来自 Azure 的所有请求，包括来自 Azure 中部署的其他客户的订阅的请求。 此选项允许的 IP 地址较为广泛，因为限制了防火墙策略的有效性。 仅当请求并非来自 VNET 中的静态 IP 或子网时，才应使用此选项。 选择此选项将自动允许从 Azure 门户进行访问，因为 Azure 门户在 Azure 中部署。

要想允许访问 Azure 门户，可以选择“接受来自公共 Azure 数据中心的连接”选项，如以下屏幕截图所示： 

![此屏幕截图显示了如何在 Azure 门户中打开“防火墙”页](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>来自当前 IP 的请求

为简化开发，Azure 门户将帮助你识别客户端计算机的 IP 并将其添加到允许列表中，以便计算机上运行的应用可以访问 Azure Cosmos 帐户。 该门户将自动检测客户端 IP 地址。 它可能是计算机的客户端 IP 地址，也可能是网络网关的 IP 地址。 请务必在将工作负荷置于生产环境前删除此 IP 地址。 

若要启用当前 IP，请选择“添加当前 IP”将当前 IP 添加到 IP 列表，并单击“保存”。

![此屏幕截图显示了如何为当前 IP 配置防火墙设置](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>来自云服务的请求

在 Azure 中，云服务是一种使用 Azure Cosmos DB 承载中间层服务逻辑的常用方法。 要从云服务启用对 Azure Cosmos 帐户的访问，必须通过[配置 IP 访问控制策略](#configure-ip-policy)，将云服务的公共 IP 地址添加到与 Azure Cosmos 帐户关联的 IP 地址的允许列表中。 这可确保云服务的所有角色实例都有权访问 Azure Cosmos 帐户。 如以下屏幕截图所示，可以在 Azure 门户中检索云服务的 IP 地址：

![该屏幕截图显示在 Azure 门户中显示的云服务的公共 IP 地址](./media/how-to-configure-firewall/public-ip-addresses.png)

通过添加其他角色实例横向扩展云服务时，这些新的实例会自动具有 Azure Cosmos 帐户的访问权限，因为它们属于同一云服务。

### <a name="requests-from-virtual-machines"></a>来自虚拟机的请求

[虚拟机](https://azure.microsoft.com/services/virtual-machines/)或[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)还可以用于通过 Azure Cosmos DB 来托管中间层服务。 要将 Cosmos 帐户配置为允许从虚拟机访问，必须通过[配置 IP 访问控制策略](#configure-ip-policy)，将虚拟机和/或虚拟机规模集的公共 IP 地址配置为 Azure Cosmos 帐户允许的 IP 地址之一。 如以下屏幕截图所示，可以在 Azure 门户中检索虚拟机的 IP 地址：

![该屏幕截图显示在 Azure 门户中显示的虚拟机的公共 IP 地址](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

将其他虚拟机实例添加到组时，会自动向其提供对 Azure Cosmos 帐户的访问权限。

### <a name="requests-from-the-internet"></a>来自 Internet 的请求

从 Internet 上的计算机访问 Azure Cosmos 帐户时，必须将客户端 IP 地址或计算机的 IP 地址范围添加到帐户 IP 地址的允许列表中。

## <a id="configure-ip-firewall-arm"></a>使用资源管理器模板配置 IP 防火墙

若要配置对 Azure Cosmos 帐户的访问控制，资源管理器模板指定 ipRangeFilter 属性，其中包含应已加入允许列表的一系列 IP 范围。 例如，将以下 JSON 代码添加到模板：

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
   }
```

## <a id="configure-ip-firewall-cli"></a>使用 Azure CLI 配置 IP 访问控制策略

以下命令演示如何创建具有 IP 访问控制的 Azure Cosmos 帐户： 

```azurecli-interactive

name="<Azure Cosmos account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

若要更新现有帐户的防火墙设置，请运行以下命令：

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>如何排查 IP 访问控制策略问题

可以使用以下选项排查 IP 访问控制策略问题： 

### <a name="azure-portal"></a>Azure 门户 
通过为 Azure Cosmos 帐户启用 IP 访问控制策略，将阻止从 IP 地址范围的允许列表外部的计算机到帐户的所有请求。 因此，如果希望启用门户数据平面操作，例如浏览容器和查询文档，则需要使用门户中的“防火墙”窗格显式允许访问 Azure 门户。

### <a name="sdks"></a>SDK 
使用不在允许列表内的计算机访问 Azure Cosmos 资源时，将返回一般的“404 找不到”响应，并且没有任何其他详细信息。 验证帐户的允许 IP 列表并确保 Cosmos 帐户中应用了正确的策略配置。 

### <a name="check-source-ips-in-blocked-requests"></a>查看已阻止请求中的源 IP
对 Azure Cosmos 帐户启用诊断日志记录，这些日志显示每个请求和响应。 与防火墙相关的消息使用 403 返回代码以内部方式记录。 通过筛选这些消息，可以查看已阻止请求的源 IP。 请参阅 [Azure Cosmos DB 诊断日志记录](logging.md)。

### <a name="requests-from-subnet-with-service-endpoint-for-azure-cosmos-database-enabled"></a>来自子网（已启用适用于 Azure Cosmos 数据库的服务终结点）的请求
来自 VNET 中的子网（其中启用了适用于 Azure Cosmos DB 的服务终结点）的请求向 Azure Cosmos 帐户发送 VNET 和子网标识。 这些请求不包含源的公共 IP，因此 IP 筛选器将拒绝它们。 若要允许从 VNET 中的特定子网进行访问，请添加[如何为 Azure Cosmos 帐户配置基于虚拟网络和子网的访问](how-to-configure-vnet-service-endpoint.md)中所述的 VNET 访问控制列表。 应用防火墙规则最多可能需要 15 分钟。


## <a name="next-steps"></a>后续步骤

若要为 Azure Cosmos DB 帐户配置虚拟网络服务终结点，请参阅以下文章：

* [适用于 Azure Cosmos 帐户的 VNET 子网访问控制](vnet-service-endpoint.md)
* [如何为 Azure Cosmos 帐户配置基于虚拟网络和子网的访问](how-to-configure-vnet-service-endpoint.md)


