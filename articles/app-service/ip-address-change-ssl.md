---
title: 如何为 SSL IP 地址更改做好准备 - Azure
description: 如果 SSL IP 地址将要更改，请了解如何在更改后继续运行应用。
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.openlocfilehash: e8558b4c3c7dafca8d4fff7e2aae0597a66c031d
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576536"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>如何为 SSL IP 地址更改做好准备

如果收到有关 Azure 应用服务应用的 SSL IP 地址即将更改的通知，请遵照本文中的说明释放现有的 SSL IP 地址并分配新地址。

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>释放 SSL IP 地址并分配新地址

1.  打开 [Azure 门户](https://portal.azure.com)。

2.  在左侧导航菜单中选择“应用服务”。

3.  从列表中选择自己的应用服务应用。

4.  在左侧导航窗格中，单击“设置”标头下的“SSL 设置”。

5. 在“SSL 绑定”部分，选择主机名记录。 在打开的编辑器中，从“SSL 类型”下拉菜单中选择“SNI SSL”，然后单击“添加绑定”。 如果出现操作成功的消息，则表示已释放现有的 IP 地址。

6.  在“SSL 绑定”部分，再次选择包含证书的同一主机名记录。 在打开的编辑器中，这次请从“SSL 类型”下拉菜单中选择“基于 IP 的 SSL”，然后单击“添加绑定”。 如果出现操作成功的消息，表示已获取新的 IP 地址。

7.  如果在域注册门户（第三方 DNS 提供程序或 Azure DNS）中配置了 A 记录（直接指向你的 IP 地址的 DNS 记录），请将现有 IP 地址替换为新生成的 IP 地址。 遵照下一部分中的说明可以找到新 IP 地址。

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>在 Azure 门户中找到新的 SSL IP 地址

1.  等待几分钟时间，然后打开 [Azure 门户](https://portal.azure.com)。

2.  在左侧导航菜单中选择“应用服务”。

3.  从列表中选择自己的应用服务应用。

4.  在“设置”标题下，单击左侧导航栏中的“属性”，找到标有“虚拟 IP 地址”的部分。

5. 复制 IP 地址并重新配置域记录或 IP 机制。

## <a name="next-steps"></a>后续步骤

本文介绍了如何对 Azure 发起的 IP 地址更改做好准备。 有关 Azure 应用服务中的 IP 地址的详细信息，请参阅 [Azure 应用服务中的 SSL 和 SSL IP 地址](app-service-ip-addresses.md)。
