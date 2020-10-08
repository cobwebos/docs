---
title: 将根或顶点域载入现有前门 Azure 门户
description: 了解如何使用 Azure 门户将根或顶点域载入现有前门。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 44813a7662420ab4dedcd0bf99cc1eec7e9d9d2d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819084"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>在 Front Door 上载入根或顶点域
Azure 前门使用 CNAME 记录来验证自定义域的加入的域所有权。 前门不会公开与前门配置文件关联的前端 IP 地址。 如果要将顶点域载入 Azure 前门，则无法将其映射到 IP 地址。

DNS 协议会阻止在区域顶点分配 CNAME 记录。 例如，如果你的域为 `contoso.com` ; 你可以为创建 cname 记录 `somelabel.contoso.com` ; 但你无法为自身创建 cname `contoso.com` 。 对于在 Azure 前门之后具有负载平衡应用程序的应用程序所有者，此限制会出现问题。 由于使用前门配置文件需要创建 CNAME 记录，因此无法指向区域顶点中的前门配置文件。

使用 Azure DNS 中的别名记录可以解决此问题。 与 CNAME 记录不同的是，在区域顶点创建别名记录。 应用程序所有者可以使用它将区域顶点记录指向具有公共终结点的前端配置文件。 应用程序所有者指向其 DNS 区域中的任何其他域使用的同一前门配置文件。 例如， `contoso.com` 和 `www.contoso.com` 可指向同一前门配置文件。 

将顶点或根域映射到前门配置文件基本上需要 CNAME 平展或 DNS 跟踪。 一种机制，其中 DNS 提供程序在达到 IP 地址之前以递归方式解析 CNAME 条目。 对于前门终结点 Azure DNS 支持此功能。 

> [!NOTE]
> 此外，还有其他 DNS 提供程序支持 CNAME 平展或 DNS 跟踪，但是，Azure 前门建议为其客户使用 Azure DNS 来托管其域。

你可以使用 Azure 门户在前门上载入顶点域，并通过将其与 TLS 终止证书相关联，对其启用 HTTPS。 顶点域也称为根域或裸域。

## <a name="create-an-alias-record-for-zone-apex"></a>为区域顶点创建别名记录

1. 打开要载入的域 **Azure DNS** 配置。

1. 创建或编辑区域顶点的记录。

1. 选择记录**类型***作为记录*，然后选择 *"是" 作为 "* **别名记录集**"。 **别名类型** 应设置为 " *Azure 资源*"。

1. 选择用于托管前门配置文件的 Azure 订阅。 然后从 " **Azure 资源** " 下拉列表中选择前门资源。

1. 选择 **"确定"** 提交更改。

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="区域顶点的别名记录&quot;:::

1. 上面的步骤将创建一个指向前门资源的区域顶点记录，还会创建一个的 CNAME 记录映射 &quot;afdverify" (`afdverify.contosonews.com`) 例如，将用于在前门配置文件中加入域的。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>在前门上载入自定义域

1. 在 "前门设计器" 选项卡上，选择 "前端主机" 部分的 "+" 图标以添加新的自定义域。

1. 在 "自定义主机名" 字段中输入 root 或顶点域名，例如 `contosonews.com` 。

1. 验证从域到前门的 CNAME 映射后，选择 " **添加** " 以添加自定义域。

1. 选择 " **保存** " 以提交更改。

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="区域顶点的别名记录&quot;:::

1. 上面的步骤将创建一个指向前门资源的区域顶点记录，还会创建一个的 CNAME 记录映射 &quot;afdverify":::

## <a name="enable-https-on-your-custom-domain"></a>在自定义域上启用 HTTPS

1. 选择已添加的自定义域，并在自定义域 **HTTPS**部分下，将状态更改为 " **已启用**"。

1. 选择  **证书管理类型** *"使用我自己的证书"*。

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="区域顶点的别名记录&quot;:::

1. 上面的步骤将创建一个指向前门资源的区域顶点记录，还会创建一个的 CNAME 记录映射 &quot;afdverify" 几分钟后，然后再次选择自定义域，查看证书设置的进度。 

> [!WARNING]
> 确保已为顶点域创建了适当的路由规则，或已将域添加到现有路由规则。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
