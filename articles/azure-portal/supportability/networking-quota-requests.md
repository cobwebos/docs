---
title: 网络限制增加 |微软文档
description: 网络限制提高
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547701"
---
# <a name="networking-limit-increase"></a>网络限制提高

使用[Azure 门户](https://portal.azure.com)增加网络配额。

要查看 Azure 门户中的当前网络使用情况和配额，请打开订阅，然后选择 **"使用情况 " 和配额**。 您还可以使用以下选项查看网络使用情况和限制。

* [使用 CLI](/cli/azure/network#az-network-list-usages)
* [电源外壳](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [网络使用情况 API](/rest/api/virtualnetwork/virtualnetworks/listusage)

您可以通过在门户中使用**帮助和支持**或 **"使用情况 " 配额**请求增加。

> [!Note]
> 要更改公共 IP**前缀**的默认大小，请从下拉列表中选择**最小公共 IP 网络前缀长度**。

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>使用帮助和支持在订阅级别请求网络配额增加

按照以下说明，在 Azure 门户中使用帮助 **+ 支持**来创建支持请求。

1. 登录到[Azure 门户](https://portal.azure.com)，然后从 Azure 门户菜单中选择 **"帮助 + 支持**"，或搜索并选择 **"帮助 + 支持**"。

    ![帮助 + 支持](./media/networking-quota-request/help-plus-support.png)

1. 选择 **"新建支持请求**"。

    ![新建支持请求](./media/networking-quota-request/new-support-request.png)

1. 对于 **"问题"类型**，请选择 **"服务和订阅限制"（配额）。**

    ![从问题类型下拉列表中选择订阅限制](./media/networking-quota-request/select-quota-issue-type.png)

1. 选择需要增加配额的订阅。

    ![选择订阅新 SR](./media/networking-quota-request/select-subscription-support-request.png)

1. 在**配额类型**下，选择 **"网络**"。 选择 **"下一步"：解决方案**。

    ![选择配额类型](./media/networking-quota-request/select-quota-type-network.png)

1. 在**问题详细信息**中，选择 **"提供详细信息**"并填写其他信息以帮助处理您的请求。

    ![提供详细信息](./media/networking-quota-request/provide-details-link.png)

1. 在 **"配额详细信息"** 面板中，选择部署模型、位置和要包含在请求中的资源。

    ![配额详细信息 DM](./media/networking-quota-request/quota-details-network.png)

1. 输入想对订阅设定的新限制。 要删除行，请从 **"资源"** 菜单中取消选择资源，或选择丢弃的"x"图标。 输入每个资源的配额后，选择 **"保存"并继续**创建支持请求。

    ![新限制](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>使用使用情况和配额在订阅级别请求网络配额增加

请按照这些说明在 Azure 门户中使用 **"使用情况 + 配额**"来创建支持请求。

1. 从https://portal.azure.com中 搜索 并选择 **"订阅**"。

    ![Subscriptions](./media/networking-quota-request/search-for-suscriptions.png)

1. 选择需要增加配额的订阅。

    ![选择订阅](./media/networking-quota-request/select-subscription-change-quota.png)

1. 选择**使用情况和配额**

    ![选择使用情况和配额](./media/networking-quota-request/select-usage-plus-quotas.png)

1. 在右上角，选择 **"请求增加**"。

    ![请求增加](./media/networking-quota-request/request-increase-from-subscription.png)

1. 按照在[订阅级别请求网络配额增加](#request-networking-quota-increase-at-subscription-level-using-help--support)中的步骤 3 开始的步骤。

## <a name="about-networking-limits"></a>关于网络限制

要了解有关网络限制的更多信息，请参阅限制页面[的网络部分](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)或我们的网络限制常见问题解答。
