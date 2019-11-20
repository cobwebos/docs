---
title: 将根或顶点域载入现有前门 Azure 门户
description: 了解如何使用 Azure 门户将根或顶点域载入现有前门。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184613"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>在前门中内置 root 或顶点域
Azure 前门使用 CNAME 记录来验证自定义域的加入的域所有权。 此外，前门并不公开与前门配置文件关联的前端 IP 地址，因此，如果要将顶点域载入 Azure 前门，则无法将其映射到 IP 地址。

DNS 协议会阻止在区域顶点分配 CNAME 记录。 例如，如果你的域是 `contoso.com`的;您可以为 `somelabel.contoso.com`创建 CNAME 记录;但无法为 `contoso.com` 本身创建 CNAME。 对于在 Azure 前门之后具有负载平衡应用程序的应用程序所有者，此限制会出现问题。 由于使用前门配置文件需要创建 CNAME 记录，因此无法指向区域顶点中的前门配置文件。

使用 Azure DNS 上的别名记录解决了此问题。 与 CNAME 记录不同，别名记录是在区域顶点创建的，应用程序所有者可以将其区域顶点记录指向具有公共终结点的前端配置文件。 应用程序所有者指向其 DNS 区域中的任何其他域使用的同一前门配置文件。 例如，`contoso.com` 和 `www.contoso.com` 可以指向同一前门配置文件。 

将顶点或根域映射到前门配置文件基本上需要 CNAME 平展或 DNS 追踪，这是在 DNS 提供商中以递归方式解析 CNAME 条目，直到达到 IP 地址的一种机制。 对于前门终结点 Azure DNS 支持此功能。 

> [!NOTE]
> 此外，还有其他 DNS 提供程序支持 CNAME 平展或 DNS 跟踪，但是，Azure 前门建议为其客户使用 Azure DNS 来托管其域。

你可以使用 Azure 门户在前门上载入顶点域，并通过将其与用于 SSL 终止的证书相关联，在其上启用 HTTPS。 顶点域也称为根域或裸域。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建指向你的前门配置文件的别名记录
> * 向前门添加根域
> * 在根域上设置 HTTPS

> [!NOTE]
> 本教程要求已创建前门配置文件。 请参阅其他教程，如[快速入门：创建前门](./quickstart-create-front-door.md) [，或使用 HTTP 到 HTTPS 重定向创建前门](./front-door-how-to-redirect-https.md)，开始使用。

## <a name="create-an-alias-record-for-zone-apex"></a>为区域顶点创建别名记录

1. 打开要载入的域**Azure DNS**配置。
2. 创建或编辑区域顶点的记录。
3. 选择记录**类型**_作为记录_，然后选择 _"是" 作为 "_ **别名记录集**"。 **别名类型**应设置为 " _Azure 资源_"。
4. 选择托管前门配置文件的 Azure 订阅，并从 " **azure 资源**" 下拉列表中选择前门资源。
5. 单击 **"确定"** 提交更改。

    ![区域顶点的别名记录](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 上面的步骤将创建指向你的前门资源的区域顶点记录，还将创建一个用于在前门配置文件上加入域 `afdverify.<name>.azurefd.net` 的 CNAME 记录映射 "afdverify" （`afdverify.contosonews.com`）。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>在前门上载入自定义域

1. 在 "前门设计器" 选项卡上，单击 "前端主机" 部分的 "+" 图标以添加新的自定义域。
2. 在 "自定义主机名" 字段中输入 root 或顶点域名，例如 `contosonews.com`。
3. 验证从域到前门的 CNAME 映射后，请单击 "**添加**" 以添加自定义域。
4. 单击 "**保存**" 以提交更改。

![自定义域菜单](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>在自定义域上启用 HTTPS

1. 单击已添加的自定义域，并在**自定义域 HTTPS**部分下，将状态更改为 "**已启用**"。
2. 选择**证书管理类型** _"使用我自己的证书"_ 。

> [!WARNING]
> 顶点或根域目前不支持前门托管证书管理类型。 用于在顶点或根域上启用 HTTPS 的唯一选项是使用托管在 Azure Key Vault 上的自定义 SSL 证书。

3. 在继续下一步之前，请确保已设置了前门的适当权限以访问密钥保管库（如 UI 中所述）。
4. 从当前订阅中选择**Key Vault 帐户**，然后选择相应的**机密**和**机密版本**以映射到正确的证书。
5. 单击 "**更新**" 以保存所选内容，然后单击 "**保存**"。
6. 几分钟后单击 "**刷新**"，然后再次单击自定义域，查看证书设置的进度。 

> [!WARNING]
> 确保已为顶点域创建了适当的路由规则，或已将域添加到现有路由规则。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。