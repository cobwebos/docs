---
title: "将现有的自定义 DNS 名称映射到 Azure Web 应用 | Microsoft Docs"
description: "了解如何在 Azure 应用服务中向 Web 应用、移动应用后端或 API 应用添加现有的自定义 DNS 域名（虚域）。"
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
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 000440fb2c38eadc0ffdcab84a3c23bb034e834f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017

---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>将现有的自定义 DNS 名称映射到 Azure Web 应用

本教程介绍如何将现有的自定义 DNS 名称映射到 [Azure Web 应用](app-service-web-overview.md)。 

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

本教程介绍如何：

> [!div class="checklist"]
> * 使用 CNAME 记录映射子域（例如 `www.contoso.com`）
> * 使用 A 记录映射根域（例如 `contoso.com`）
> * 使用 CNAME 记录映射通配符域（例如 `*.contoso.com`）
> * 使用脚本自动执行域映射

可以使用 **CNAME 记录**或 **A 记录**将自定义 DNS 名称映射到应用服务。

> [!NOTE]
> 我们建议对除根域（例如 `contoso.com`）以外的所有自定义 DNS 名称使用 CNAME。 
> 
> 

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要能够访问域提供商（例如 GoDaddy）的 DNS 注册表，并且有权编辑域的配置。 

例如，若要添加 `contoso.com` 和 `www.contoso.com` 的 DNS 条目，必须有权配置 `contoso.com` 根域的 DNS 设置。 

> [!NOTE]
> 如果没有现有的域名，请考虑遵照[应用服务域教程](custom-dns-web-site-buydomains-web-app.md)使用 Azure 门户购买一个域。 
>
>

## <a name="prepare-your-app"></a>准备应用程序
若要映射自定义 DNS 名称，[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于付费层（“共享”、“基本”、“标准”或“高级”）。 在此步骤中，需确保应用服务计划位于受支持的定价层。

### <a name="sign-in-to-azure"></a>登录 Azure

打开 Azure 门户。 为此，请使用你的 Azure 帐户登录到 [https://portal.azure.com](https://portal.azure.com)。

### <a name="navigate-to-your-app"></a>导航到你的应用
在左侧菜单中单击“应用服务”，然后单击应用的名称。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/select-app.png)

现已进入应用服务应用的边栏选项卡（_边栏选项卡_：水平打开的门户页）。  

### <a name="check-the-pricing-tier"></a>检查定价层

在应用边栏选项卡的左侧导航窗格中，向下滚动到“设置”部分，然后选择“扩大(应用服务计划)”。

![扩展菜单](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

检查以确保应用不在“免费”层中。 深蓝色的框突出显示了应用的当前层。 

![检查定价层](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

**免费**层不支持自定义 DNS。 如果你需要进行纵向扩展，请按照下一部分操作。 否则，请关闭“选择定价层”边栏选项卡并跳到[映射 CNAME 记录](#cname)或[映射 A 记录](#a)。

### <a name="scale-up-your-app-service-plan"></a>纵向扩展应用服务计划

选择任一非免费层（“共享”、“基本”、“标准”或“高级”）。 

单击“选择”。

![检查定价层](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

当看到以下通知时，说明缩放操作已完成。

![缩放操作确认](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>映射 CNAME 记录

教程示例将为 `www` 子域 (`www.contoso.com`) 添加 CNAME 记录。 

### <a name="access-dns-records-with-domain-provider"></a>通过域提供商访问 DNS 记录

首先，请登录到域提供商的网站。

查找管理 DNS 记录的页面。 每个域提供商都有其自己的 DNS 记录界面，因此你应当查阅你的提供商的文档。 查找站点中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。 

通常可通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到连接。 然后，找到用于管理 DNS 记录的链接。 此链接可能命名为“区域文件”、“DNS 记录”或“高级配置”。

以下屏幕截图是 DNS 记录页的一个示例：

![示例 DNS 记录页](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

在示例屏幕截图中，单击“添加”以创建记录。 某些提供商提供了不同的链接来添加不同的记录类型。 同样，你需要查阅你的提供商的文档。

> [!NOTE]
> 对于某些提供商（例如 GoDaddy），在你单击单独的“保存更改”链接之前，这些 DNS 记录不会生效。 
>
>

### <a name="create-the-cname-record"></a>创建 CNAME 记录

添加一条 CNAME 记录，以便将子域映射到应用的默认主机名 (`<app_name>.azurewebsites.net`)。

在 `www.contoso.com` 域示例中，CNAME 记录应会将名称 `www` 指向 `<app_name>.azurewebsites.net`。

DNS 记录页如以下屏幕截图所示：

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>在应用中启用 CNAME 记录映射

现在，可将配置的 DNS 名称添加到应用。

在应用边栏选项卡的左侧导航窗格中，单击“自定义域”。 

![自定义域菜单](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

在应用的“自定义域”边栏选项卡中，需要将完全限定的自定义 DNS 名称 (`www.contoso.com`) 添加到列表。

单击“添加主机名”旁边的 **+** 图标。

![添加主机名](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

键入前面为其配置了 CNAME 记录的完全限定域名（例如 `www.contoso.com`），然后单击“验证”。

否则，“添加主机名”按钮会被激活。 

确保“主机名记录类型”设置为“CNAME 记录(example.com)”。

单击“添加主机名”将 DNS 名称添加到应用。

![将 DNS 名称添加到应用](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页面中。 请尝试刷新浏览器来更新数据。

![已添加 CNAME 记录](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

如果你之前错过了某个步骤或者在某个位置的输入不正确，则会在页面的底部看到验证错误。

![验证错误](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>映射 A 记录

在教程示例中，你希望为根域 `contoso.com` 添加 A 记录。 

<a name="info"></a>

### <a name="copy-your-apps-ip-address"></a>复制应用的 IP 地址

若要映射 A 记录，需要应用的外部 IP 地址。 可以在“自定义域”边栏选项卡中找到此 IP 地址。

在应用边栏选项卡的左侧导航窗格中，单击“自定义域”。 

![自定义域菜单](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

在“自定义域”页中，复制应用的 IP 地址。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="access-dns-records-with-domain-provider"></a>通过域提供商访问 DNS 记录

首先，请登录到域提供商的网站。

查找管理 DNS 记录的页面。 每个域提供商都有其自己的 DNS 记录界面，因此你应当查阅你的提供商的文档。 查找站点中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。 

通常可通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到连接。 然后，找到用于管理 DNS 记录的链接。 此链接可能命名为“区域文件”、“DNS 记录”或“高级配置”。

以下屏幕截图是 DNS 记录页的一个示例：

![示例 DNS 记录页](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

在示例屏幕截图中，单击“添加”以创建记录。 某些提供商提供了不同的链接来添加不同的记录类型。 同样，你需要查阅你的提供商的文档。

> [!NOTE]
> 对于某些提供商（例如 GoDaddy），在你单击单独的“保存更改”链接之前，这些 DNS 记录不会生效。 
>
>

<a name="create-a"></a>

### <a name="create-the-a-record"></a>创建 A 记录

若要将 A 记录映射到应用，应用服务通常需要**两个** DNS 记录：

- 要映射到应用 IP 地址的 **A** 记录。
- 要映射到应用默认主机名 `<app_name>.azurewebsites.net` 的 **TXT** 记录。 此记录使得应用服务可以验证你是否拥有要映射的自定义域。

`www.contoso.com` 域示例根据下表创建 A 和 TXT 记录（`@` 通常表示根域）。 

| 记录类型 | 主机 | 值 |
| - | - | - |
| A | `@` | 在[复制应用的 IP 地址](#info)步骤中复制的 IP 地址 |
| TXT | `@` | `<app_name>.azurewebsites.net` |

DNS 记录页应如以下屏幕截图所示：

![DNS 记录页](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-your-app"></a>在应用中启用 A 记录映射

现在，可将配置的 DNS 名称添加到应用。

在 Azure 门户中返回到应用的“自定义域”页。需要将完全限定的自定义 DNS 名称 (`contoso.com`) 添加到列表。

单击“添加主机名”旁边的 **+** 图标。

![添加主机名](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

键入前面为其配置了 A 记录的完全限定域名（例如 `contoso.com`），然后单击“验证”。

否则，“添加主机名”按钮会被激活。 

确保“主机名记录类型”设置为“A 记录 (example.com)”。

单击“添加主机名”将 DNS 名称添加到应用。

![将 DNS 名称添加到应用](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页面中。 请尝试刷新浏览器来更新数据。

![已添加 A 记录](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

如果你之前错过了某个步骤或者在某个位置的输入不正确，则会在页面的底部看到验证错误。

![验证错误](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>映射通配符域

还可以将[通配符 DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record)（例如 `*.contoso.com`）映射到应用服务应用。 

此处的步骤说明如何使用 CNAME 记录映射通配符域 `*.contoso.com`。 

### <a name="access-dns-records-with-domain-provider"></a>通过域提供商访问 DNS 记录

首先，请登录到域提供商的网站。

查找管理 DNS 记录的页面。 每个域提供商都有其自己的 DNS 记录界面，因此你应当查阅你的提供商的文档。 查找站点中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。 

通常可通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到连接。 然后，找到用于管理 DNS 记录的链接。 此链接可能命名为“区域文件”、“DNS 记录”或“高级配置”。

以下屏幕截图是 DNS 记录页的一个示例：

![示例 DNS 记录页](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

在示例屏幕截图中，单击“添加”以创建记录。 某些提供商提供了不同的链接来添加不同的记录类型。 同样，你需要查阅你的提供商的文档。

> [!NOTE]
> 对于某些提供商（例如 GoDaddy），在你单击单独的“保存更改”链接之前，这些 DNS 记录不会生效。 
>
>

### <a name="create-the-cname-record"></a>创建 CNAME 记录

添加一条 CNAME 记录，以便将通配符域名映射到应用的默认主机名 (`<app_name>.azurewebsites.net`)。

在 `*.contoso.com` 域示例中，CNAME 记录应会将名称 `*` 指向 `<app_name>.azurewebsites.net`。

DNS 记录页如以下屏幕截图所示：

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>在应用中启用 CNAME 记录映射

现在，可以添加与通配符域名匹配的任何子域。

对于 `*.contoso.com` 通配符域示例，现在可以添加 `sub1.contoso.com` 和 `sub2.contoso.com`。 

在应用边栏选项卡的左侧导航窗格中，单击“自定义域”。 

![自定义域菜单](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

单击“添加主机名”旁边的 **+** 图标。

![添加主机名](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

键入与通配符域匹配的子域的完全限定域名（例如 `sub1.contoso.com`），然后单击“验证”。

否则，“添加主机名”按钮会被激活。 

确保“主机名记录类型”设置为“CNAME 记录(example.com)”。

单击“添加主机名”将 DNS 名称添加到应用。

![将 DNS 名称添加到应用](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页面中。 请尝试刷新浏览器来更新数据。

可以再次单击 **+** 图标，添加另一个与通配符域匹配的主机名。

例如，使用上述相同步骤添加 `sub2.contoso.com`。

![已添加 CNAME 记录](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>在浏览器中测试

在浏览器中，浏览至你之前配置的 DNS 名称（`contoso.com` 和 `www.contoso.com`）。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>使用脚本自动化

可以在 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview) 中使用脚本自动管理自定义域。 

### <a name="azure-cli"></a>Azure CLI 

以下命令将配置的自定义 DNS 名称添加到应用服务应用。 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resourece_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

有关详细信息，请参阅[将自定义域映射到 Web 应用](scripts/app-service-cli-configure-custom-domain.md) 

### <a name="azure-powershell"></a>Azure PowerShell 

以下命令将配置的自定义 DNS 名称添加到应用服务应用。 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resourece_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

有关详细信息，请参阅[将自定义域分配到 Web 应用](scripts/app-service-powershell-configure-custom-domain.md)。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 CNAME 记录映射子域
> * 使用 A 记录映射根域
> * 使用 CNAME 记录映射通配符域
> * 使用脚本自动执行域映射

转到下一教程，了解如何向它绑定自定义 SSL 证书。

> [!div class="nextstepaction"]
> [将现有的自定义 SSL 证书绑定到 Azure Web 应用](app-service-web-tutorial-custom-ssl.md)

