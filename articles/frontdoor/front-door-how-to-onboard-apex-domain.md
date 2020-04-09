---
title: 将根域或顶点域添加到现有前门 - Azure 门户
description: 了解如何使用 Azure 门户将根域或顶点域板载到现有前门。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 4b74338f22a82d76ef13126ee0862b841bd89a99
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878878"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>在前门上板载根或顶点域
Azure 前门使用 CNAME 记录来验证域所有权以用于加入自定义域。 此外，前门不会公开与前门配置文件关联的前端 IP 地址，因此，如果意图是将其板载到 Azure 前门，则无法将顶点域映射到 IP 地址。

DNS 协议会阻止在区域顶点分配 CNAME 记录。 例如，如果您的域为`contoso.com`。可以为 创建`somelabel.contoso.com`CNAME 记录。但您无法为自己`contoso.com`创建 CNAME。 对于在 Azure 前门后面具有负载平衡应用程序的应用程序所有者来说，此限制带来了问题。 由于使用前门配置文件需要创建 CNAME 记录，因此无法从区域顶点指向前门配置文件。

此问题已使用 Azure DNS 上的别名记录得到解决。 与 CNAME 记录不同，别名记录是在区域顶点创建的，应用程序所有者可以使用它将区域顶点记录指向具有公共终结点的前门配置文件。 应用程序所有者指向用于其 DNS 区域内任何其他域的同一前门配置文件。 例如，`contoso.com`可以`www.contoso.com`指向相同的前门配置文件。 

将顶点或根域映射到前门配置文件基本上需要 CNAME 拼平或 DNS 追逐，这是 DNS 提供程序中递归地解析 CNAME 条目直到其到达 IP 地址的机制。 Azure DNS 支持用于前门端点的此功能。 

> [!NOTE]
> 还有其他 DNS 提供程序支持 CNAME 拼平或 DNS 追逐，但是，Azure 前门建议为其客户使用 Azure DNS 为其托管其域。

您可以使用 Azure 门户将前门上的顶点域上载，并通过将其与 TLS 终止证书相关联，在其中启用 HTTPS。 顶点域也称为根域或裸域。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建指向前门配置文件的别名记录
> * 将根域添加到前门
> * 在根域上设置 HTTPS

> [!NOTE]
> 本教程要求您已创建前门配置文件。 参考其他教程，如[快速入门：创建前门](./quickstart-create-front-door.md)或[创建一个前门与HTTP到HTTPS重定向](./front-door-how-to-redirect-https.md)开始。

## <a name="create-an-alias-record-for-zone-apex"></a>为区域顶点创建别名记录

1. 打开**要**上载的域的 Azure DNS 配置。
2. 创建或编辑区域顶点的记录。
3. 选择记录**类型**作为_记录_，然后为**别名记录集**选择 _"是_"。 **别名类型**应设置为_Azure 资源_。
4. 选择托管前门配置文件的 Azure 订阅，然后从**Azure 资源**下拉列表中选择前门资源。
5. 单击"**确定"** 以提交更改。

    ![区域顶点的别名记录](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 上述步骤将创建指向前门资源的区域顶点记录，以及 CNAME 记录映射"afdverify"（示例 -），`afdverify.contosonews.com``afdverify.<name>.azurefd.net`用于在前门配置文件上加入域。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>在前门上的自定义域上

1. 在"前门设计器"选项卡上，单击"前端主机"部分上的"+"图标以添加新的自定义域。
2. 在自定义主机名字段中输入根或顶点域名，示例`contosonews.com`。
3. 验证从域到前门的 CNAME 映射后，单击"**添加"** 以添加自定义域。
4. 单击 **"保存"** 以提交更改。

![自定义域菜单](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>在自定义域上启用 HTTPS

1. 单击已添加的自定义域，并在 **"自定义域 HTTPS"** 部分下将状态更改为 **"已启用**"。
2. 选择**证书管理类型**以 _"使用我自己的证书"。_

> [!WARNING]
> 顶点或根域当前不支持前门托管证书管理类型。 在前门的顶点或根域上启用 HTTPS 的唯一选项是使用托管在 Azure 密钥保管库上的自定义 TLS/SSL 证书。

3. 确保在继续下一步之前，已设置前门访问密钥保管库的正确权限，以便访问 UI 中所述的密钥保管库。
4. 从当前订阅中选择**密钥保管库帐户**，然后选择适当的**机密**和**机密版本**以映射到正确的证书。
5. 单击 **"更新"** 以保存所选内容，然后单击"**保存**"。
6. 单击几分钟后**刷新**，然后再次单击自定义域以查看证书预配的进度。 

> [!WARNING]
> 确保已为顶点域创建了适当的路由规则，或将域添加到现有路由规则。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。