---
title: 如何为入站 IP 地址更改做准备 - Azure
description: 如果入站 IP 地址将要更改，请了解如何在更改后继续运行应用。
services: app-service\web
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: tdykstra
ms.openlocfilehash: 5d30357e5308de0224590703bb5e68fbe73b882b
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343428"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>如何为入站 IP 地址更改做准备

如果收到“Azure 应用服务”应用的入站 IP 地址将要更改的通知，请按照本文中的说明进行操作。

## <a name="determine-if-you-have-to-do-anything"></a>确定是否需要做什么

* 选项 1：如果“应用服务”应用没有自定义域，则不需要执行任何操作。

* 选项 2：如果在域注册门户（第三方 DNS 提供程序或 Azure DNS）中仅配置了一条 CNAME 记录（指向 URI 的 DNS 记录），则不需要执行任何操作。

* 选项 3：如果在域注册门户（第三方 DNS 提供程序或 Azure DNS）中配置了 A 记录（直接指向你的 IP 地址的 DNS 记录），请将现有 IP 地址替换为新 IP 地址。 按照下一部分中的说明可以找到新 IP 地址。

* 选项 4：如果应用程序位于负载均衡器、IP 筛选器或需要应用程序 IP 地址的任何其他 IP 机制之后，请将现有 IP 地址替换为新 IP 地址。 按照下一部分中的说明可以找到新 IP 地址。

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>在 Azure 门户中找到新的入站 IP 地址

为应用提供的新入站 IP 地址位于门户的“虚拟 IP 地址”字段中。 这个新 IP 地址和旧 IP 地址现在都已连接到你的应用，之后旧 IP 地址将断开连接。

1.  打开 [Azure 门户](https://portal.azure.com)。

2.  在左侧导航菜单中选择“应用服务”。

3.  从列表中选择自己的应用服务应用。

4.  如果应用是一个函数应用，请参阅[函数应用入站 IP 地址](../azure-functions/ip-addresses.md#function-app-inbound-ip-address)。

4.  在“设置”标题下，单击左侧导航栏中的“属性”，找到标有“虚拟 IP 地址”的部分。

5. 复制 IP 地址并重新配置域记录或 IP 机制。

## <a name="next-steps"></a>后续步骤

本文介绍了如何对 Azure 发起的 IP 地址更改做好准备。 有关 Azure 应用服务中的 IP 地址的详细信息，请参阅 [Azure 应用服务中的入站和出站 IP 地址](app-service-ip-addresses.md)。
