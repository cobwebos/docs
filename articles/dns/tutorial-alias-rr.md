---
title: 教程：创建别名记录以引用区域中的资源记录
titleSuffix: Azure DNS
description: 本教程介绍如何配置 Azure DNS 别名记录以引用区域内的资源记录。
services: dns
author: asudbring
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: allensu
ms.openlocfilehash: 59ffe9781d97880044da5eedbdf84181bf1b2fa1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082880"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>教程：创建别名记录以引用区域资源记录

别名记录可引用相同类型的其他记录集。 例如，可以使 DNS CNAME 记录集成为相同类型的另一 CNAME 记录集的别名。 如果要使部分记录集成为别名，部分成为有关行为的非别名，请使用此功能。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为区域中的资源记录创建别名记录。
> * 测试别名记录。


如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
必须具有可用于在 Azure DNS 中托管以供测试的域名。 必须能够完全控制此域。 完全控制包括能够为域设置名称服务器 (NS) 记录。

有关在 Azure DNS 中托管域的说明，请参阅[教程：在 Azure DNS 中托管域](dns-delegate-domain-azure-dns.md)。


## <a name="create-an-alias-record"></a>创建别名记录

创建指向区域中资源记录的别名记录。

### <a name="create-the-target-resource-record"></a>创建目标资源记录
1. 选择 Azure DNS 区域以打开该区域。
2. 选择“记录集”。 
3. 在“名称”  文本框中，输入“server”  。
4. 对于“类型”  ，选择“A”  。
5. 在“IP 地址”  文本框中，输入“10.10.10.10”  。
6. 选择“确定”  。

### <a name="create-the-alias-record"></a>创建别名记录
1. 选择 Azure DNS 区域以打开该区域。
2. 选择“记录集”。 
3. 在“名称”  文本框中，输入“test”  。
4. 对于“类型”  ，选择“A”  。
5. 在“别名记录集”复选框中选择“是”  。  然后选择“区域记录集”  选项。
6. 对于“区域记录集”  ，请选择“server”  记录。
7. 选择“确定”  。

## <a name="test-the-alias-record"></a>测试别名记录

1. 启动最喜欢的 nslookup 工具。 一种方法是浏览到 [https://network-tools.com/nslook ](https://network-tools.com/nslook)。
2. 设置 A 记录的查询类型并查找 test.\<你的域名\>  。 答案为 **10.10.10.10**。
3. 在 Azure 门户中，将 server  A 记录更改为 10.11.11.11  。
4. 等待几分钟，然后将 nslookup 再次用于 test  记录。 答案为 **10.11.11.11**。

## <a name="clean-up-resources"></a>清理资源

不再需要为本教程创建的资源时，请在区域中删除 **server** 和 **test** 资源记录。


## <a name="next-steps"></a>后续步骤

在本教程中，你已创建可引用区域中的资源记录的别名记录。 若要了解 Azure DNS 和 Web 应用，请继续学习适用于 Web 应用的教程。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)
