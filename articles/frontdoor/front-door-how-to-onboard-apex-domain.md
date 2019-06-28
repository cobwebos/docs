---
title: 载入到现有第一道防线使用 Azure 门户中的根或顶点域
description: 了解如何载入到使用 Azure 门户的现有前门域根或顶点。
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 464c38b0ece274d2ea7df89ab9fd7c0a60dc4b58
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332486"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>载入根或顶点域中的对你第一道防线
Azure 的第一道防线使用 CNAME 记录来验证域加入自定义域的所有权。 此外，第一道防线不公开与你的第一道防线配置文件关联的前端 IP 地址，因此您不能将顶点域映射到 IP 地址，如果目的是为载入它到 Azure 第一道防线。

DNS 协议会阻止在区域顶点分配 CNAME 记录。 例如，如果你的域是`contoso.com`; 你可以创建的 CNAME 记录`somelabel.contoso.com`; 但不是能创建的 CNAME`contoso.com`本身。 此限制的应用程序所有者而言具有负载平衡的应用程序背后 Azure 第一道防线面临一个问题。 由于使用的第一道防线配置文件要求创建 CNAME 记录，因此不能指向从区域顶点的第一道防线配置文件。

在 Azure DNS 上使用别名记录解决此问题。 与 CNAME 记录，请在区域顶点创建别名记录和应用程序所有者可以使用它为其区域顶点处记录指向具有公共终结点的第一道防线配置文件。 应用程序所有者指向相同的第一道防线配置用于任何其他域中其 DNS 区域文件。 例如，`contoso.com`和`www.contoso.com`可以指向相同的第一道防线配置文件。 

基本上将顶点或根域映射到你的第一道防线配置文件需要 CNAME 平展或 DNS 追踪，这是一种机制，其中在 DNS 中提供程序以递归方式解析 CNAME 条目直到它达到的 IP 地址。 Azure DNS 支持此功能的第一道防线终结点。 

> [!NOTE]
> 其他 DNS 提供程序还支持 CNAME 平展或 DNS 追踪，但是，Azure 第一道防线建议使用 Azure DNS 为客户托管其域。

可以在您指定的地址上使用 Azure 门户加入 apex 域并在其上启用 HTTPS，通过将它与 SSL 终端的证书相关联。 顶点域也称为根或裸域。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建指向您的第一道防线个人资料的别名记录
> * 将根域添加到第一道防线
> * 安装程序上的根域的 HTTPS

> [!NOTE]
> 本教程需要已创建的第一道防线配置文件。 像其他教程，请参阅[快速入门：创建第一道防线](./quickstart-create-front-door.md)或[创建支持 HTTP 到 HTTPS 重定向的第一道防线](./front-door-how-to-redirect-https.md)若要开始。

## <a name="create-an-alias-record-for-zone-apex"></a>创建区域顶点的别名记录

1. 打开**Azure DNS**好要加入域的配置。
2. 创建或编辑区域顶点处的记录。
3. 选择的记录**类型**作为_A_记录，然后选择_是_有关**别名记录集**。 **别名类型**应设置为_Azure 资源_。
4. 选择您的第一道防线个人资料的托管位置的 Azure 订阅，然后选择的第一道防线资源**Azure 资源**下拉列表。
5. 单击**确定**提交所做的更改。

    ![区域顶点的别名记录](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 上述步骤将创建指向您的第一道防线资源以及 CNAME 记录映射 afdverify 的区域顶点处记录 (示例- `afdverify.contosonews.com`) 到`afdverify.<name>.azurefd.net`这将是用于加入域第一道防线的个人资料。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>加入自定义域上在第一道防线

1. 在第一道防线设计器选项卡上，单击 + 前端主机上的图标部分以添加新的自定义域。
2. 在自定义主机名称字段中，输入根或顶点的域名示例`contosonews.com`。
3. 从域到您指定的地址的 CNAME 映射验证后，单击**添加**要添加自定义域。
4. 单击**保存**提交所做的更改。

![自定义域菜单](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>你的自定义域上启用 HTTPS

1. 单击添加自定义域上和部分下**自定义域 HTTPS**，将状态更改为**已启用**。
2. 选择**证书管理类型**到_使用我自己的证书_。

> [!WARNING]
> 前端托管的门证书管理类型目前不支持顶点或根域。 可用于启用 HTTPS 上的顶点或根域的第一道防线的唯一选项使用 Azure 密钥保管库上托管您自己自定义 SSL 证书。

3. 请确保已设置的第一道防线，若要访问你的密钥保管库在 UI 中，然后继续执行下一步所述的适当权限。
4. 选择**密钥保管库帐户**从当前订阅，然后选择相应**机密**并**机密版本**将映射到正确的证书。
5. 单击**更新**以保存所做选择，然后单击**保存**。
6. 单击**刷新**后几分钟，然后单击自定义的域再次以查看证书预配的进度。 

> [!WARNING]
> 请确保您有适当的路由规则创建顶点域或域添加到现有的路由规则。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。