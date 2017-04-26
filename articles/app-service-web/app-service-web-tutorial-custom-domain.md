---
title: "将自定义 DNS 名称映射到 Azure Web 应用 | Microsoft Docs"
description: "了解如何在 Azure 应用服务中向 Web 应用、移动应用后端或 API 应用添加自定义 DNS 域名（即虚域）。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 25ddbd0c89ec7eee8eb240cf111245bb8b5add48
ms.lasthandoff: 04/04/2017


---
# <a name="map-a-custom-dns-name-to-an-azure-web-app"></a>将自定义 DNS 名称映射到 Azure Web 应用

本教程将展示如何在 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)中将自定义 DNS 名称（即虚域）映射到 Web 应用、移动应用后端或 API 应用。 

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

本教程的前导内容是在应用服务中将两个 DNS 名称映射到应用的示例方案：

- `contoso.com` - 根域。 你将使用 [A 记录](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)将其映射到 Azure。 
- `www.contoso.com` - `contoso.com` 的子域。 你可以使用 [A 记录](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)或 [CNAME 记录](https://en.wikipedia.org/wiki/CNAME_record)。

本教程还将展示如何将[通配符 DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) 映射到应用服务（例如 `*.contoso.com`）。

## <a name="before-you-begin"></a>开始之前

由于本教程将展示如何将 DNS 名称映射到 Azure 应用服务，因此你必须对你的相应域提供商（例如 GoDaddy）的 DNS 配置页面具有管理访问权限。 例如，若要为 `contoso.com` 和 `www.contoso.com` 添加映射，你需要能够配置 `contoso.com` 的 DNS 条目。

如果你尚未向域提供商注册域，始终可以[直接从 Azure 购买域并将其映射到应用](custom-dns-web-site-buydomains-web-app.md)。

## <a name="step-1---prepare-your-app"></a>第 1 步 - 准备你的应用
若要映射自定义 DNS 名称，你的[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须处于**共享**层或更高层。 在此步骤中，你将确保 Azure 应用处于受支持的定价层。

### <a name="log-in-to-azure"></a>登录 Azure

打开 Azure 门户。 

为此，请使用你的 Azure 帐户登录到 [https://portal.azure.com](https://portal.azure.com)。

### <a name="navigate-to-your-app"></a>导航到你的应用
从左侧菜单中单击“应用服务”，然后单击你的 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/select-app.png)

现已进入你的应用的_边栏选项卡_（一个水平打开的门户页面）。  

### <a name="check-the-pricing-tier"></a>检查定价层
在默认打开的“概述”页面中，进行检查以确保你的应用未处于**免费**层。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

**免费**层不支持自定义 DNS。 如果你需要进行纵向扩展，请按照下一部分操作。 否则，请跳到[步骤 2]()。

### <a name="scale-up-your-app-service-plan"></a>纵向扩展应用服务计划

若要纵向扩展计划，请在左侧窗格中单击“纵向扩展(应用服务计划)”。

选择要扩展到的层。 例如，选择“共享”。 准备就绪后，单击“选择”。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

当看到以下通知时，说明缩放操作已完成。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="info"></a>

## <a name="step-2---get-mapping-information"></a>第 2 步 - 获取映射信息

在此步骤中，你将获取稍后创建 DNS 记录时需要的信息。 

### <a name="open-the-custom-domain-ui"></a>打开自定义域 UI

在应用的边栏选项卡中，在菜单中单击“自定义域”。 

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

### <a name="copy-the-mapping-information"></a>复制映射信息

在“自定义域”页面中，在“分配到站点的主机名”下，复制应用的 **IP 地址**及其默认主机名。

稍后你需要将该 IP 地址用于 A 记录映射，需要将默认主机名用于 CNAME 记录映射。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

## <a name="step-3---add-dns-records"></a>第 3 步 - 添加 DNS 记录 

在此步骤中，你将添加将自定义 DNS 映射到应用时需要的 DNS 记录。 你将通过你自己的域提供商执行此步骤。

### <a name="access-dns-records-with-domain-provider"></a>通过域提供商访问 DNS 记录

首先，登录到你的域提供商的网站。

然后，你需要查找用于管理 DNS 记录的页面。 每个域提供商都有其自己的 DNS 记录界面，因此你应当查阅你的提供商的文档。 通常，你应当查找站点中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。 通常可通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到连接。 

然后，查找允许管理 DNS 记录的链接。 此链接可能命名为“区域文件”、“DNS 记录”或“高级配置”。

以下屏幕截图是 DNS 记录页面的可能外观的一个示例：

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

在示例屏幕截图中，单击“添加”以创建记录。 某些提供商提供了不同的链接来添加不同的记录类型。 同样，你需要查阅你的提供商的文档。

> [!NOTE]
> 对于某些提供商（例如 GoDaddy），在你创建 DNS 记录后单击单独的“保存更改”链接之前，这些 DNS 记录不会生效。 
>
>

<a name="a"></a>

### <a name="create-an-a-record"></a>创建 A 记录

在教程示例中，你希望为根域 `contoso.com` 添加 A 记录。 

若要将 A 记录映射到应用，应用服务通常需要_两个_ DNS 记录：

- **A** 记录映射到应用的 IP 地址。
- **TXT** 记录映射到应用的默认主机名。 此记录使得应用服务可以验证你是否拥有要映射的自定义域。

下表显示了你将如何为受支持的域类型配置 A 记录映射（`@` 通常表示根域）。 

<table cellspacing="0" border="1">
  <tr>
    <th>域类型</th>
    <th>记录类型</th>
    <th>主机</th>
    <th>值</th>
  </tr>
  <tr>
    <td rowspan="2" align="left">根域<br>（例如 <code>contoso.com</code>）</td>
    <td>A</td>
    <td>@</td>
    <td><a href="#info">第 2 步</a>中的 IP 地址</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>@</td>
    <td><a href="#info">第 2 步</a>中的默认主机名</td>
  </tr>
  <tr>
    <td rowspan="2" align="left">子域<br>（例如 <code>www.contoso.com</code>）</td>
    <td>A</td>
    <td>www</td>
    <td><a href="#info">第 2 步</a>中的 IP 地址</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>www</td>
    <td><a href="#info">第 2 步</a>中的默认主机名</td>
  </tr>
  <tr>
    <td rowspan="2" align="left">通配符域<br>（例如 <code>*.contoso.com</code>）</td>
    <td>A</td>
    <td>\*</td>
    <td><a href="#info">第 2 步</a>中的 IP 地址</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>\*</td>
    <td><a href="#info">第 2 步</a>中的默认主机名</td>
  </tr>
</table>

对于 `contoso.com` 域示例，你的 DNS 记录页面如以下屏幕截图所示：

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="cname"></a>

### <a name="create-a-cname-record"></a>创建 CNAME 记录
在教程示例中，你希望为根域 `contoso.com` 添加 CNAME 记录。 

与[映射 A 记录](#a)不同，将 CNAME 记录映射到应用不需要任何额外的记录。

> [!IMPORTANT]
> 建议你使用 CNAME 映射。 如果你删除并重新创建应用，或者从专用托管层更改回**共享**层，则应用的虚拟 IP 地址可能会发生更改。 在经历这样的更改后，CNAME 映射仍然保持有效，但 A 映射可能会因新的 IP 地址而失效。 
>
> 不过，不要为根域（即“根记录”）创建 CNAME 记录。 有关详细信息，请参阅 [Why can't a CNAME record be used at the root domain](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)（为什么不能将 CNAME 记录用于根域）。
> 若要将根域映射到 Azure 应用，请改用 [A 记录](#a)。
> 
> 

下表显示了你将如何为受支持的域类型配置 CNAME 记录映射。

如下所示配置 CNAME 记录。

<table cellspacing="0" border="1">
  <tr>
    <th>域类型</th>
    <th>CNAME 主机</th>
    <th>CNAME 值</th>
  </tr>
  <tr>
    <td>子域<br>（例如 <code>www.contoso.com</code>）</td>
    <td>www</td>
    <td><a href="#info">第 2 步</a>中的默认主机名</td>
  </tr>
  <tr>
    <td>通配符域<br>（例如 <code>*.contoso.com</code>）</td>
    <td>\*</td>
    <td><a href="#info">第 2 步</a>中的默认主机名</td>
  </tr>
</table>

对于 `www.contoso.com` 域示例，你的 DNS 记录页面如以下屏幕截图所示：

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/cname-record.png)

<a name="enable"></a>

## <a name="step-4---enable-the-custom-dns-in-your-app"></a>第 4 步 - 在应用中启用自定义 DNS

现在可以向应用中添加你已配置的 DNS 名称（例如 `contoso.com` 和 `www.contoso.com`）了。

在 Azure 门户中返回到应用的“自定义域”页面（请参阅[第 1 步](#info)），你需要将自定义域的完全限定域名 (FQDN) 添加到列表中。

### <a name="add-the-a-hostname-to-your-app"></a>向应用中添加 A 主机名

单击“添加主机名”旁边的 **+** 图标。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

键入你之前为其配置了 A 记录的 FQDN（例如 `contoso.com`），然后单击“验证”。

如果你之前错过了某个步骤或者在某个位置的输入不正确，则会在页面的底部看到验证错误。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/verification-error.png)

否则，“添加主机名”按钮会被激活。 

确保“主机名记录类型”设置为“A 记录 (example.com)”。

单击“添加主机名”将 DNS 名称添加到应用。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页面中。 请尝试刷新浏览器来更新数据。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

<a name="add-cname"></a>
### <a name="add-the-cname-hostname-to-your-app"></a>向应用中添加 CNAME 主机名

单击“添加主机名”旁边的 **+** 图标。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

键入你之前为其配置了 CNAME 记录的 FQDN（例如 `www.contoso.com`），然后单击“验证”。

如果你之前错过了某个步骤或者在某个位置的输入不正确，则会在页面的底部看到验证错误。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

否则，“添加主机名”按钮会被激活。 

确保“主机名记录类型”设置为“A 记录 (example.com)”。

单击“添加主机名”将 DNS 名称添加到应用。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页面中。 请尝试刷新浏览器来更新数据。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

### <a name="step-5---test-in-browser"></a>第 5 步 - 在浏览器中进行测试

在浏览器中，浏览至你之前配置的 DNS 名称（`contoso.com` 和 `www.contoso.com`）。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="more-resources"></a>更多资源

[在 Azure 应用服务中购买和配置自定义域名](custom-dns-web-site-buydomains-web-app.md)

