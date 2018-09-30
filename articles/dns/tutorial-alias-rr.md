---
title: 教程 - 创建 Azure DNS 别名记录以引用区域中的资源记录。
description: 本教程介绍如何配置 Azure DNS 别名记录以引用区域内的资源记录。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990835"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>教程：创建别名记录以引用区域资源记录

别名记录可引用相同类型的其他记录集。 例如，可以使 DNS CNAME 记录集成为相同类型的另一 CNAME 记录集的别名。 如果要使部分记录集成为别名，部分成为有关行为的非别名，这将有所帮助。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为区域中的资源记录创建别名记录
> * 测试别名记录


如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
必须具有可用于在 Azure DNS 中托管以供测试的域名。 必须对此域具有完全控制权限，包括能够为域设置名称服务器 (NS) 记录。

有关在 Azure DNS 中托管域的说明，请参阅[教程：在 Azure DNS 中托管域](dns-delegate-domain-azure-dns.md)。


## <a name="create-an-alias-record"></a>创建别名记录

创建指向区域中资源记录的别名记录。

### <a name="create-the-target-resource-record"></a>创建目标资源记录
1. 单击 Azure DNS 区域以打开该区域。
2. 单击“记录集”。
3. 在“名称”文本框中，键入“server”。
4. 对于“类型”，选择“A”。
5. 在“IP 地址”文本框中，键入“10.10.10.10”。
6. 单击“确定”。

### <a name="create-the-alias-record"></a>创建别名记录
1. 单击 Azure DNS 区域以打开该区域。
2. 单击“记录集”。
3. 在“名称”文本框中，键入“test”。
4. 对于“类型”，选择“A”。
5. 单击“别名记录集”复选框中的“是”，然后选择“区域记录集”选项。
6. 对于“区域记录集”，请选择“server”记录。
7. 单击“确定”。

## <a name="test-the-alias-record"></a>测试别名记录

1. 启动你最喜欢的 nslookup 工具。 一种方法是浏览到 [https://network-tools.com/nslook ](https://network-tools.com/nslook)。
2. 设置 A 记录的查询类型并查找 test.\<你的域名\>。 应获得答案 10.10.10.10。
3. 在 Azure 门户中，将 server A 记录更改为 10.11.11.11。
4. 等待几分钟，然后将 nslookup 再次用于 test 记录。
5. 应获得答案 10.11.11.11。

## <a name="clean-up-resources"></a>清理资源

如果不再需要，可以删除区域中的 server 和 test 资源记录。


## <a name="next-steps"></a>后续步骤

在本教程中，你已创建可引用区域中的资源记录的别名记录。 若要了解 Azure DNS 和 Web 应用，请继续学习适用于 Web 应用的教程。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)
