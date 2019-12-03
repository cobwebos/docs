---
title: 购买自定义域名
description: 了解如何购买应用服务域，并将其用作应用 Azure App Service 的自定义域。
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 0723a3b0c96339ba86b98ab851c44d2a65e6f34b
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672382"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>为 Azure 应用服务购买自定义域名

应用服务域是直接在 Azure 中管理的顶级域。 使用这些域可以轻松管理 [Azure 应用服务](overview.md)的自定义域。 本教程介绍如何购买应用服务域并将 DNS 名称分配到 Azure 应用服务。

对于 Azure VM 或 Azure 存储，请查看[Assign App Service domain to Azure VM or Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/)（将应用服务分配到 Azure VM 或 Azure 存储）。 对于云服务，请查看[为 Azure 云服务配置自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。

## <a name="prerequisites"></a>必备组件

完成本教程需要：

* [创建应用服务应用](/azure/app-service/)，或使用为另一教程创建的应用。
* [移除订阅中的支出限制](../billing/billing-spending-limit.md#remove)。 无法购买具有免费订阅信用额度的应用服务域。

## <a name="prepare-the-app"></a>准备应用

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

若要在 Azure 应用服务中使用自定义域，应用的[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于付费层（“共享”、“基本”、“标准”或“高级”）。 在此步骤中，请确保应用位于受支持的定价层。

### <a name="sign-in-to-azure"></a>登录 Azure

打开 [Azure 门户](https://portal.azure.com)，然后使用 Azure 帐户登录。

### <a name="navigate-to-the-app-in-the-azure-portal"></a>导航到 Azure 门户中的应用

从左侧菜单中选择“应用服务”，然后选择应用名称。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/select-app.png)

将看到应用服务应用的管理页。  

### <a name="check-the-pricing-tier"></a>检查定价层

在应用页的左侧导航窗格中，向下滚动到“设置”部分，然后选择“增加(应用服务计划)”。

![扩展菜单](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

蓝色边框突出显示了应用的当前层。 检查以确保应用不在 **F1** 层中。 **F1** 层不支持自定义 DNS。 

![检查定价层](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

如果应用服务计划不在 **F1** 层中，请关闭“纵向扩展”页并跳转到[购买域](#buy-the-domain)。

### <a name="scale-up-the-app-service-plan"></a>增加应用服务计划

选择任何非免费层（**D1**、**B1**、**B2**、**B3**，或“生产”类别中的任何层）。 有关其他选项，请单击“查看其他选项”。

单击“应用”。

![检查定价层](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

看到以下通知时，说明缩放操作已完成。

![缩放操作确认](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>购买域

### <a name="pricing-information"></a>定价信息
有关 Azure App Service 域的定价信息，请访问[应用服务定价页](https://azure.microsoft.com/pricing/details/app-service/windows/)并向下滚动到 "应用服务域"。

### <a name="sign-in-to-azure"></a>登录 Azure
打开 [Azure 门户](https://portal.azure.com/)，然后使用 Azure 帐户登录。

### <a name="launch-buy-domains"></a>启动“购买域”
在“应用服务”选项卡中，单击应用的名称，选择“设置”，并选择“自定义域”
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

在“自定义域”页中，单击“购买域”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> 如果看不到“应用服务域”部分，则需要移除 Azure 帐户上的支出限制（请参阅[先决条件](#prerequisites)）。
>
>

### <a name="configure-the-domain-purchase"></a>配置域购买

在“应用服务域”页上的“搜索域”框中，键入要购买的域名并按 `Enter`。 文本框的正下方会显示建议的可用域。 选择要购买的一个或多个域。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> 应用服务域支持以下[顶级域](https://wikipedia.org/wiki/Top-level_domain)：com、net、co.uk、org、nl、in、biz、org.uk 和 co.in。
>
>

单击“联系人信息”，并填写域的联系人信息表单。 完成后，单击“确定”返回“应用服务域”页。

请务必尽量准确填写所有必填字段。 如果联系人信息数据不正确，可能会导致无法购买域。

接下来，选择域的所需选项。 参阅下表中的说明：

| 设置 | 建议的值 | 描述 |
|-|-|-|
|隐私保护 | 启用 | 选择“隐私保护”，已在采购价免费包含。 某些顶级域由不支持隐私保护的注册机构托管，并在“隐私保护”页上列出。 |
| 分配默认主机名 | **www** 和 **\@** | 根据需要选择主机名绑定。 完成域购买操作后，即可通过选定的主机名访问自己的应用。 如果应用在 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)的后面，则不会显示用于分配根域 (@) 的选项，因为流量管理器不支持 A 记录。 完成域购买过程后，可以更改主机名分配。 |

### <a name="accept-terms-and-purchase"></a>接受条款并购买

单击“法律条款”查看条款和费用，再单击“购买”。

> [!NOTE]
> 应用服务域使用 GoDaddy 进行域注册，并 Azure DNS 来托管域。 除了域注册费以外，还会产生 Azure DNS 的使用费。 有关信息，请参阅 [Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)。
>
>

返回“应用服务域”页，单击“确定”。 操作正在进行时，会显示以下通知：

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>测试主机名

如果已将默认主机名分配到应用，则每个选定的主机名还会出现对应的成功通知。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

此外，“自定义域”页上的“自定义主机名”部分也会显示选定的主机名。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> 自定义域的 "**不安全**" 标签意味着尚未绑定到 SSL 证书，从浏览器到自定义域的任何 HTTPS 请求都将收到错误或警告，具体取决于浏览器。 若要配置 SSL 绑定，请参阅[在 Azure App Service 中使用 SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)。
>

若要测试主机名，请在浏览器中导航到列出的主机名。 在上面的屏幕截图的示例中，请尝试导航到_kontoso.net_和_www\.kontoso.net_。

## <a name="assign-hostnames-to-app"></a>将主机名分配到应用

如果选择不在购买过程中将一个或多个默认主机名分配到应用，或者需要分配未列出的主机名，则可以随时分配主机名。

还可以将应用服务域中的主机名分配到任何其他应用。 执行的步骤取决于应用服务域和应用是否属于同一个订阅。

- 不同订阅：将来自应用服务域的自定义 DNS 记录映射到应用，例如外部购买的域。 有关将自定义 DNS 名称添加到应用服务域的信息，请参阅[管理自定义 DNS 记录](#custom)。 若要将外部购买的域映射到应用，请参阅[将现有的自定义 DNS 名称映射到 Azure 应用服务](app-service-web-tutorial-custom-domain.md)。 
- 相同的订阅：使用以下步骤。

### <a name="launch-add-hostname"></a>启动“添加主机名”
在“应用服务”页中，选择要将主机名分配到的应用的名称，选择“设置”，并选择“自定义域”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

确保购买的域已列在“应用服务域”部分中，但不要选择它。 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> 同一订阅中的所有应用服务域显示在应用的“自定义域”页中。 如果域在应用的订阅中，但应用的“自定义域”页中未显示该域，请尝试重新打开“自定义域”页或刷新网页。 另外，请通过 Azure 门户顶部的通知铃铛来查看进度或创建失败结果。
>
>

选择“添加主机名”。

### <a name="configure-hostname"></a>配置主机名
在“添加主机名”对话框中，键入应用服务域或任何子域的完全限定域名。 例如：

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

完成后，选择“验证”。 系统会自动选择主机名记录类型。

选择“添加主机名”。

完成该操作后，会看到所分配主机名的成功通知。  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>关闭“添加主机名”
在“添加主机名”页中，根据需要将任何其他主机名分配到应用。 完成后，关闭“添加主机名”页。

现在，应用的“自定义域”页中应会显示新分配的主机名。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>测试主机名

在浏览器中导航到列出的主机名。 在上面屏幕截图所示的示例中，请尝试导航到 _abc.kontoso.net_。

## <a name="renew-the-domain"></a>续订域

你所购买的应用服务域自购买之日起的一年内有效。 默认情况下，通过收取下一年费用的付款方式将域配置为自动续订。 你可以手动续订你的域名。

如果要关闭自动续订，或者手动续订域，请按照以下步骤操作。

在“应用服务”选项卡中，单击应用的名称，选择“设置”，并选择“自定义域”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

在“应用服务域”部分，选择要配置的域。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

从域的左侧导航窗格，选择“域续订”。 若要停止自动续订域，请选择“关闭”，然后“保存”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

若要手动续订域，选择“续订域”。 但是，此按钮在[域过期之前90天内](#when-domain-expires)处于非活动状态。

如果域续订成功，则会在24小时内收到电子邮件通知。

## <a name="when-domain-expires"></a>域过期时间

Azure 会处理过期或过期的应用服务域，如下所示：

* 如果禁用自动续订：在域过期之前90天，将向你发送续订通知电子邮件，并在门户中激活 "**续订域**" 按钮。
* 如果启用自动续订：在你的域到期日期之后的那一日，Azure 将尝试为你的域名续订付费。
* 如果在自动续订期间出现错误（例如，你的卡已过期），或者如果禁用自动续订，并且你允许域过期，则 Azure 会通知你域过期，并使你的域名过期。 你可以[手动续订](#renew-the-domain)你的域。
* 在过期后的第4天和第12天，Azure 会向你发送其他通知电子邮件。 你可以[手动续订](#renew-the-domain)你的域。
* 在过期后的第19天，你的域仍保持不变，但会收取兑换费。 你可以致电客户支持人员续订你的域名，但需遵守任何适用的续订和兑换费用。
* 在过期后的第25天，Azure 将使用域名行业拍卖服务使你的域与拍卖联系。 你可以致电客户支持人员续订你的域名，但需遵守任何适用的续订和兑换费用。
* 过期后的30天后，你将无法再兑换你的域。

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>管理自定义 DNS 记录

在 Azure 中，可以使用 [Azure DNS](https://azure.microsoft.com/services/dns/) 管理应用服务域的 DNS 记录。 可以添加、删除和更新 DNS 记录，就像使用外部购买的域时一样。

### <a name="open-app-service-domain"></a>打开“应用服务域”

在 Azure 门户的左侧菜单中，选择“所有服务” > “应用服务域”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

选择要管理的域。 

### <a name="access-dns-zone"></a>访问“DNS 区域”

在域的左侧菜单中，选择“DNS 区域”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

此操作会在 Azure DNS 中打开应用服务域的[“DNS 区域”](../dns/dns-zones-records.md)页。 有关如何编辑 DNS 记录的信息，请参阅[如何在 Azure 门户中管理 DNS 区域](../dns/dns-operations-dnszones-portal.md)。

## <a name="cancel-purchase-delete-domain"></a>取消购买（删除域）

购买应用服务域后，可在五天内取消购买以获得全额退款。 五天之后，可以删除应用服务域，但无法获得退款。

### <a name="open-app-service-domain"></a>打开“应用服务域”

在 Azure 门户的左侧菜单中，选择“所有服务” > “应用服务域”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

选择要取消或删除的域。 

### <a name="delete-hostname-bindings"></a>删除主机名绑定

在域的左侧菜单中，选择“主机名绑定”。 此处列出了所有 Azure 服务的主机名绑定。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

只有在删除所有主机名绑定之后，才能删除应用服务域。

通过选择“...” > “删除”来删除每个主机名绑定。 删除所有绑定后，选择“保存”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>取消或删除

在域的左侧菜单中，选择“概述”。 

如果所购买域的取消期限未过，请选择“取消购买”。 否则，请参阅“删除”按钮。 若要删除该域且不想要获得退款，请选择“删除”。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

选择“是”以确认操作。

完成该操作后，该域将从订阅中释放，可供任何人再次购买。 

## <a name="direct-default-url-to-a-custom-directory"></a>将默认 URL 定向到自定义目录

默认情况下，应用服务将 Web 请求定向到应用代码的根目录下。 若要将它们定向到一个子目录，例如 `public`，请参阅[将默认 URL 定向到自定义目录](app-service-web-tutorial-custom-domain.md#virtualdir)。
