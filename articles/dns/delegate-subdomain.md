---
title: Delegate a subdomain - Azure DNS
description: With this learning path, get started delegating an Azure DNS subdomain.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: allensu
ms.openlocfilehash: 462282e9674e7a253f61c96338b54174c80fb03f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212380"
---
# <a name="delegate-an-azure-dns-subdomain"></a>委托 Azure DNS 子域

可以使用 Azure 门户委托 DNS 子域。 例如，如果拥有 contoso.com 域，可将名为 *engineering* 的子域委托给另一个可以独立于 contoso.com 区域进行管理的单独区域。

如果需要，可以使用 [Azure PowerShell](delegate-subdomain-ps.md) 委托子域。

## <a name="prerequisites"></a>必备组件

若要委托 Azure DNS 子域，必须先将公共域委托给 Azure DNS。 有关如何为委托配置名称服务器的说明，请参阅[将域委托给 Azure DNS](./dns-delegate-domain-azure-dns.md)。 将域委托给 Azure DNS 区域后，可以委托子域。

> [!NOTE]
> 本文通篇都使用 contoso.com 作为示例。 请将 contoso.com 替换为你自己的域名。

## <a name="create-a-zone-for-your-subdomain"></a>为子域创建区域

首先，为 **engineering** 域创建区域。

1. 在 Azure 门户中，选择“创建资源”。
2. 在搜索框中键入 **DNS**，然后选择“DNS 区域”。
3. 选择**创建**。
4. 在“创建 DNS 区域”窗格中的“名称”文本框内，键入 **engineering.contoso.com**。
5. 选择区域的资源组。 可以使用父区域所在的同一资源组将类似资源放到一起。
6. 单击“创建”。
7. 部署成功后，转到新区域。

## <a name="note-the-name-servers"></a>记下名称服务器

接下来，记下 engineering 子域的四个名称服务器。

在 **engineering** 区域窗格中，记下区域的四个名称服务器。 稍后要使用这些名称服务器。

## <a name="create-a-test-record"></a>创建测试记录

创建用于测试的 **A** 记录。 例如，创建 **www** A 记录，并为其配置 **10.10.10.10** IP 地址。

## <a name="create-an-ns-record"></a>创建 NS 记录

接下来，为 **engineering** 区域创建名称服务器 (NS) 记录。

1. 导航到父域的区域。
2. 选择“+ 记录集”。
3. 在“添加记录集”窗格中的“名称”文本框内，键入 **engineering**。
4. 对于“类型”，请选择“NS”。
5. 在“名称服务器”下，输入前面从 **engineering** 区域记录的四个名称服务器。
6. 单击 **“确定”** 。

## <a name="test-the-delegation"></a>测试委托

使用 nslookup 测试委托。

1. 打开 PowerShell 窗口。
2. 在命令提示符下，键入 `nslookup www.engineering.contoso.com.`
3. 应会收到一条非权威回复，其中显示了地址 **10.10.10.10**。

## <a name="next-steps"></a>后续步骤

了解如何[为 Azure 中托管的服务配置反向 DNS](dns-reverse-dns-for-azure-services.md)。