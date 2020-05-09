---
title: 配置服务防火墙规则
description: 配置 IP 规则，以便能够从所选的公共 IP 地址或地址范围访问 Azure 容器注册表。
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984611"
---
# <a name="configure-public-ip-network-rules"></a>配置公共 IP 网络规则

默认情况下，Azure 容器注册表接受来自任何网络上的主机的 internet 连接。 本文介绍如何将容器注册表配置为仅允许来自特定公共 IP 地址或地址范围的访问。 提供的等效步骤使用 Azure CLI 和 Azure 门户。

在公共注册表终结点上配置 IP 网络规则。 IP 网络规则不适用于配置有[专用链接](container-registry-private-link.md)的专用终结点

配置 IP 访问规则在**高级**容器注册表服务层中提供。 有关注册表服务层和限制的信息，请参阅[Azure 容器注册表层](container-registry-skus.md)。

## <a name="access-from-selected-public-network---cli"></a>从所选的公共网络进行访问-CLI

### <a name="change-default-network-access-to-registry"></a>更改注册表的默认网络访问权限

若要限制对所选公用网络的访问，请首先将默认操作更改为 "拒绝访问"。 在下面的[az acr update][az-acr-update]命令中替换注册表的名称：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>向注册表添加网络规则

使用[az acr network rule add][az-acr-network-rule-add]命令将网络规则添加到允许从公共 IP 地址或范围进行访问的注册表。 例如，使用虚拟网络中的 VM 的名称和 VM 的公共 IP 地址。

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> 添加规则后，规则将需要几分钟才能生效。

## <a name="access-from-selected-public-network---portal"></a>从所选的公共网络访问-门户

1. 在门户中，导航到容器注册表。
1. 在 "**设置**" 下，选择 "**网络**"。
1. 在 "**公共访问**" 选项卡上，选择允许从**所选网络**进行公共访问。
1. 在 "**防火墙**" 下，输入公共 ip 地址，如虚拟网络中的 VM 的公共 ip 地址。 或者，以 CIDR 表示法输入包含 VM IP 地址的地址范围。
1. 选择“保存”  。

![为容器注册表配置防火墙规则][acr-access-selected-networks]

> [!NOTE]
> 添加规则后，规则将需要几分钟才能生效。

> [!TIP]
> 或者，从本地客户端计算机或 IP 地址范围启用注册表访问。 若要允许此访问，你需要计算机的公共 IPv4 地址。 可以通过在 internet 浏览器中搜索 "我的 IP 地址" 来查找此地址。 当你在门户中的 "**网络**" 页上配置防火墙设置时，当前客户端 IPv4 地址也会自动出现。

## <a name="disable-public-network-access"></a>禁用公共网络访问

若要使用 "[专用链接](container-registry-private-link.md)" 限制到虚拟网络的流量，请在注册表上禁用公共终结点。 禁用公共终结点会覆盖所有的防火墙配置。

### <a name="disable-public-access---portal"></a>禁用公共访问-门户

1. 在门户中，导航到容器注册表，选择 "**设置" > 网络**"。
1. 在 "**公共访问**" 选项卡上的 "**允许公共访问**" 中，选择 "**禁用**"。 再选择“保存”  。

![禁用公共访问][acr-access-disabled]

## <a name="restore-default-registry-access"></a>还原默认注册表访问

若要将注册表还原为默认允许访问，请更新默认操作。 

### <a name="restore-default-registry-access---portal"></a>还原默认注册表访问门户

1. 在门户中，导航到容器注册表，选择 "**设置" > 网络**"。
1. 在 "**防火墙**" 下，选择每个地址范围，然后选择 "删除" 图标。
1. 在 "**公共访问**" 选项卡上的 "**允许公共访问**" 中，选择 "**所有网络**"。 再选择“保存”  。

![从所有网络公开访问][acr-access-all-networks]

## <a name="next-steps"></a>后续步骤

* 若要使用虚拟网络中的专用终结点限制对注册表的访问，请参阅[配置 azure 容器注册表的 Azure 专用链接](container-registry-private-link.md)。
* 如果需要从客户端防火墙后面设置注册表访问规则，请参阅[配置规则以访问防火墙后面的 Azure 容器注册表](container-registry-firewall-access-rules.md)。

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
