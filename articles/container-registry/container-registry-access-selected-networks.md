---
title: 配置公共注册表访问
description: 配置 IP 规则，以便能够从所选的公共 IP 地址或地址范围访问 Azure 容器注册表。
ms.topic: article
ms.date: 05/19/2020
ms.openlocfilehash: dc0514fbe7d3e01914965cee5dc547172d4435a4
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702086"
---
# <a name="configure-public-ip-network-rules"></a>配置公共 IP 网络规则

默认情况下，Azure 容器注册表接受来自任何网络上的主机的 Internet 连接。 本文介绍如何将容器注册表配置为仅允许来自特定公共 IP 地址或地址范围的访问。 其中提供了使用 Azure CLI 和 Azure 门户的等效步骤。

IP 网络规则在公共注册表终结点上进行配置。 IP 网络规则不适用于配置有[专用链接](container-registry-private-link.md)的专用终结点

可在“高级”容器注册表服务层级配置 IP 访问规则。 有关注册表服务层级和限制的信息，请参阅 [Azure 容器注册表层级](container-registry-skus.md)。

## <a name="access-from-selected-public-network---cli"></a>从所选的公用网络进行访问 - CLI

### <a name="change-default-network-access-to-registry"></a>更改默认网络对注册表的访问权限

要将访问权限仅授予所选公用网络，请先将默认操作更改为拒绝访问。 在以下 [az acr update][az-acr-update] 命令中，替换注册表的名称：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>向注册表添加网络规则

使用 [az acr network-rule add][az-acr-network-rule-add] 命令向注册表添加允许从公共 IP 地址或范围进行访问的网络规则。 例如，替换容器注册表的名称和虚拟网络中 VM 的公共 IP 地址。

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> 添加规则后，它需要几分钟才能生效。

## <a name="access-from-selected-public-network---portal"></a>从所选的公用网络访问 - 门户

1. 在门户中，导航到容器注册表。
1. 在“设置”下选择“网络” 。
1. 在“公共访问”选项卡上，选择允许从“所选网络”进行公共访问 。
1. 在“防火墙”下，输入公共 IP 地址，如虚拟网络中 VM 的公共 IP 地址。 或者，以 CIDR 表示法输入包含 VM IP 地址的地址范围。
1. 选择“保存”。

![为容器注册表配置防火墙规则][acr-access-selected-networks]

> [!NOTE]
> 添加规则后，它需要几分钟才能生效。

> [!TIP]
> 或者，也可通过本地客户端计算机或 IP 地址范围启用注册表访问。 要允许此访问，需要计算机的公共 IPv4 地址。 可通过在 Internet 浏览器中搜索“我的 IP 地址是多少”来查找此地址。 在门户中的“网络”页面上配置防火墙设置时，当前客户端 IPv4 地址也会自动出现。

## <a name="disable-public-network-access"></a>禁用公用网络访问

（可选）禁用注册表上的公共终结点。 禁用公共终结点会重写所有防火墙配置。 例如，建议使用[专用链接](container-registry-private-link.md)禁用对虚拟网络中受保护注册表的公共访问。

### <a name="disable-public-access---cli"></a>禁用公共访问 - CLI

若要使用 Azure CLI 禁用公共访问，请运行 [az acr update][az-acr-update]，并将 `--public-network-enabled` 设置为 `false`。 

> [!NOTE]
> `public-network-enabled` 参数需要 Azure CLI 2.6.0 或更高版本。 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>禁用公共访问 - 门户

1. 在门户中，导航到容器注册表，选择“设置”>“网络”。
1. 在“公共访问”选项卡上的“允许公用网络访问”中，选择“禁用”  。 再选择“保存”。

![禁用公共访问][acr-access-disabled]


## <a name="restore-public-network-access"></a>还原公用网络访问

要重新启用公共终结点，请更新网络设置以允许公共访问。 启用公共终结点会重写所有防火墙配置。 

### <a name="restore-public-access---cli"></a>还原公共访问 - CLI

运行 [az acr update][az-acr-update] 并将 `--public-network-enabled` 设置为 `true`。 

> [!NOTE]
> `public-network-enabled` 参数需要 Azure CLI 2.6.0 或更高版本。 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>还原公共访问 - 门户

1. 在门户中，导航到容器注册表，选择“设置”>“网络”。
1. 在“公共访问”选项卡上的“允许公用网络访问”中，选择“所有网络”  。 再选择“保存”。

![来自所有网络的公共访问][acr-access-all-networks]

## <a name="next-steps"></a>后续步骤

* 要使用虚拟网络中的专用终结点限制对注册表的访问，请参阅[为 Azure 容器注册表配置 Azure 专用链接](container-registry-private-link.md)。
* 如需设置从客户端防火墙后访问注册表的规则，请参阅[配置从防火墙后访问 Azure 容器注册表的规则](container-registry-firewall-access-rules.md)。

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
