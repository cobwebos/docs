---
title: 如何为出站 IP 地址更改做准备 - Azure
description: 如果出站 IP 地址将要更改，请了解如何在更改后继续运行应用。
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.openlocfilehash: 5e17638e030ae2827b0de7dc82ecf75062504112
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575924"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>如何为出站 IP 地址更改做准备

如果收到“Azure 应用服务”应用的出站 IP 地址将要更改的通知，请按照本文中的说明进行操作。

## <a name="determine-if-you-have-to-do-anything"></a>确定是否需要做什么

* 选项 1：如果“应用服务”应用不使用 IP 筛选、显式包含列表或出站流量的特殊处理（如路由或防火墙），则不需要执行任何操作。

* 选项 2：如果你的应用确实对出站 IP 地址进行了特殊处理（请参阅下面的示例），请在现有 IP 地址出现的任何地方添加新的出站 IP 地址。 请勿替换现有的 IP 地址。 按照下一部分中的说明可以找到新的出站 IP 地址。

  例如，出站 IP 地址可能显式地包含在应用之外的防火墙中，或者外部支付服务可能有一个允许列表，其中包含应用的出站 IP 地址。 如果出站地址在应用之外的任何位置的列表中配置，则需要更改。

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>在 Azure 门户中找到出站 IP 地址

新的出站 IP 地址在生效之前会显示在门户中。 当 Azure 开始使用新的出站 IP 地址时，将不再使用旧的出站 IP 地址。 每次只使用一组 IP 地址，因此包含列表中的条目必须同时具有旧的和新的 IP 地址，以防止在进行切换时发生中断。 

1.  打开 [Azure 门户](https://portal.azure.com)。

2.  在左侧导航菜单中选择“应用服务”。

3.  从列表中选择自己的应用服务应用。

4.  如果应用是一个函数应用，请参阅[函数应用出站 IP 地址](../azure-functions/ip-addresses.md#function-app-outbound-ip-addresses)。

4.  在“设置”标题下，单击左侧导航栏中的“属性”，找到标有“出站 IP 地址”的部分。

5. 复制 IP 地址，并将其添加到对出站流量的特殊处理中，例如筛选器或允许列表。 请勿删除列表中的现有 IP 地址。

## <a name="next-steps"></a>后续步骤

本文介绍了如何对 Azure 发起的 IP 地址更改做好准备。 有关 Azure 应用服务中的 IP 地址的详细信息，请参阅 [Azure 应用服务中的入站和出站 IP 地址](app-service-ip-addresses.md)。
