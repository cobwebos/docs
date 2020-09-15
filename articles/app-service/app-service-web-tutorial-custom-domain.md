---
title: 教程：映射现有的自定义 DNS 名称
description: 了解如何在 Azure 应用服务中向 Web 应用、移动应用后端或 API 应用添加现有的自定义 DNS 域名（虚域）。
keywords: 应用服务, Azure 应用服务, 域映射, 域名, 现有域, 主机名
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 6a74f105525ec8ce28559b47ed4fc9624f518a06
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488331"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>教程：将现有的自定义 DNS 名称映射到 Azure 应用服务

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。 本教程介绍如何将现有的自定义域名系统 (DNS) 名称映射到应用服务。

![显示到 Azure 应用的 Azure 门户导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 CNAME 记录映射子域（例如 `www.contoso.com`）。
> * 使用 A 记录映射根域（例如 `contoso.com`）。
> * 使用 CNAME 记录映射通配符域（例如 `*.contoso.com`）。
> * 将默认 URL 重定向到自定义目录。
> * 使用脚本自动执行域映射。

## <a name="prerequisites"></a>先决条件

为完成此教程：

* [创建应用服务应用](./index.yml)，或使用为另一教程创建的应用。
* 购买域名，确保可以访问域提供商（如 GoDaddy）的 DNS 注册表。

  例如，若要添加 `contoso.com` 和 `www.contoso.com` 的 DNS 条目，必须能够配置 `contoso.com` 根域的 DNS 设置。

  > [!NOTE]
  > 如果现在还没有域名，请考虑[使用 Azure 门户购买域](manage-custom-dns-buy-domain.md)。

## <a name="prepare-the-app"></a>准备应用

若要将自定义 DNS 名称映射到 Web 应用，Web 应用的[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于付费层（Azure Functions 的共享、基本、标准、高级或消耗层）。 在此步骤中，需确保应用服务计划位于受支持的定价层。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>登录 Azure

打开 [Azure 门户](https://portal.azure.com)，然后使用 Azure 帐户登录。

### <a name="select-the-app-in-the-azure-portal"></a>在 Azure 门户中选择应用

1. 搜索并选择“应用服务”。

   ![演示如何选择应用服务的屏幕截图。](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. 在“应用服务”页上，选择 Azure 应用的名称。

   ![显示到 Azure 应用的门户导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/select-app.png)

将看到应用服务应用的管理页。

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>检查定价层

1. 在应用页的左侧窗格中，向下滚动到“设置”部分，然后选择“纵向扩展(应用服务计划)” 。

   ![显示“纵向扩展(应用服务计划)”菜单的屏幕截图。](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. 蓝色边框突出显示了应用的当前层。 检查以确保应用不在 F1 层中。 F1 层不支持自定义 DNS。

   ![显示“建议的定价层”的屏幕截图。](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. 如果应用服务计划不在 F1 层中，请关闭“纵向扩展”页并跳转到[映射 CNAME 记录](#map-a-cname-record) 。

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>增加应用服务计划

1. 选择任何非免费层（**D1**、**B1**、**B2**、**B3**，或“生产”类别中的任何层）。 有关其他选项，请选择“查看其他选项”。

1. 选择“应用”。

   ![显示检查定价层的屏幕截图。](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   看到以下通知时，说明缩放操作已完成。

   ![显示缩放操作确认的屏幕截图。](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>获取域验证 ID

若要将自定义域添加到应用，需要使用域提供程序添加验证 ID 作为 TXT 记录来验证你对域的所有权。 在应用页的左侧窗格中，选择“自定义域”。 复制“自定义域”页中的“自定义域验证 ID”框中的 ID，以便进行下一步操作 。

![显示“自定义域验证 ID”框中的 ID 的屏幕截图。](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> 向自定义域添加域验证 ID 可防止出现无关联的 DNS 条目，并避免子域接管。 有关此常见高严重性威胁的详细信息，请参阅[子域接管](../security/fundamentals/subdomain-takeover.md)。

## <a name="map-your-domain"></a>映射域

可以使用 CNAME 记录或 A 记录将自定义 DNS 名称映射到应用服务。 请按照相应的步骤操作：

- [映射 CNAME 记录](#map-a-cname-record)
- [映射 A 记录](#map-an-a-record)
- [映射通配符域（使用 CNAME 记录）](#map-a-wildcard-domain)

> [!NOTE]
> 应该对除根域（例如 `contoso.com`）以外的所有自定义 DNS 名称使用 CNAME 记录。 对于根域，请使用 A 记录。

### <a name="map-a-cname-record"></a>映射 CNAME 记录

在教程示例中，为 `www` 子域（例如 `www.contoso.com`）添加 CNAME 记录。

如果你有除 `www` 以外的子域，请将 `www` 替换为你的子域（例如，如果你的自定义域为 `sub.constoso.com`，则使用 `sub` 进行替换）。

#### <a name="access-dns-records-with-a-domain-provider"></a>通过域提供商访问 DNS 记录

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>创建 CNAME 记录

将子域映射到应用的默认域名（`<app-name>.azurewebsites.net`，其中 `<app-name>` 是应用的名称）。 若要为 `www` 子域创建 CNAME 映射，请创建两条记录：

| 记录类型 | 主机 | 值 | 注释 |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | 域映射本身。 |
| TXT | `asuid.www` | [之前获得的验证 ID](#get-a-domain-verification-id) | 应用服务访问 `asuid.<subdomain>` TXT 记录以验证你对自定义域的所有权。 |

添加 CNAME 和 TXT 记录后，DNS 记录页如下例所示：

![显示到 Azure 应用的门户导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>在 Azure 中启用 CNAME 记录映射

1. 在 Azure 门户中的应用页左侧窗格中，选择“自定义域”。

    ![显示“自定义域”菜单的屏幕截图。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 在应用的“自定义域”页中，将完全限定的自定义 DNS 名称添加到 (`www.contoso.com`) 列表。

1. 选择“添加自定义域”。

    ![显示“添加主机名”项的屏幕截图。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 键入已添加 CNAME 记录的完全限定的域名，如 `www.contoso.com`。

1. 选择“验证”。 此时将显示“添加自定义域”页。

1. 确保“主机名记录类型”设置为“CNAME (www\.example.com 或任何子域)”。 选择“添加自定义域”。

    ![显示“添加自定义域”按钮的屏幕截图。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    新的自定义域可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。

    ![演示如何添加 CNAME 记录的屏幕截图。](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > 自定义域带有警告标签意味着该域尚未绑定到 TLS/SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告，具体取决于浏览器。 若要添加 TLS 绑定，请参阅[在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)。

    如果之前错过了某个步骤或者在某个位置的输入不正确，则会在页面的底部显示验证错误。

    ![显示验证错误的屏幕截图。](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>映射 A 记录

在教程示例中，为根域（例如 `contoso.com`）添加 A 记录。

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>复制应用的 IP 地址

若要映射 A 记录，需要应用的外部 IP 地址。 可以在 Azure 门户中应用的“自定义域”页中找到此 IP 地址。

1. 在 Azure 门户中的应用页左侧窗格中，选择“自定义域”。

   ![显示“自定义域”菜单的屏幕截图。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 在“自定义域”页中，复制应用的 IP 地址。

   ![显示到 Azure 应用的门户导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>通过域提供商访问 DNS 记录

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>创建 A 记录

若要将 A 记录映射到应用（通常是根域），请创建两条记录：

| 记录类型 | 主机 | Value | 注释 |
| - | - | - |
| A | `@` | 在[复制应用的 IP 地址](#info)步骤中复制的 IP 地址 | 域映射本身（`@` 通常表示根域）。 |
| TXT | `asuid` | [之前获得的验证 ID](#get-a-domain-verification-id) | 应用服务访问 `asuid.<subdomain>` TXT 记录以验证你对自定义域的所有权。 对于根域，请使用 `asuid`。 |

> [!NOTE]
> 若要使用 A 记录（而不是建议的 [CNAME 记录](#map-a-cname-record)）添加子域（如 `www.contoso.com`），A 记录和 TXT 记录应如下表所示：
>
> | 记录类型 | 主机 | Value |
> | - | - | - |
> | A | `www` | 在[复制应用的 IP 地址](#info)步骤中复制的 IP 地址 |
> | TXT | `asuid.www` | `<app-name>.azurewebsites.net` |
>

添加记录后，DNS 记录页与以下示例相似：

![显示 DNS 记录页的屏幕截图。](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>在应用中启用 A 记录映射

在 Azure 门户中返回到应用的“自定义域”页，将完全限定的自定义 DNS 名称（例如 `contoso.com`）添加到列表。

1. 选择“添加自定义域”。

    ![演示如何添加主机名的屏幕截图。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 键入已配置 A 记录的完全限定的域名，如 `contoso.com`。 

1. 选择“验证”。 此时会显示“添加自定义域”页。

1. 确保“主机名记录类型”设置为“A 记录 (example.com)”。  选择“添加自定义域”。

    ![演示如何将主机名添加到应用的屏幕截图。](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    新的自定义域可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。

    ![演示如何添加 A 记录的屏幕截图。](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > 自定义域带有警告标签意味着该域尚未绑定到 TLS/SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告，具体取决于浏览器。 若要添加 TLS 绑定，请参阅[在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)。
    
    如果之前错过了某个步骤或者在某个位置的输入不正确，则会在页面的底部显示验证错误。
    
    ![显示验证错误的屏幕截图。](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>映射通配符域

在教程示例中，你通过添加 CNAME 记录将[通配符 DNS 名称](https://en.wikipedia.org/wiki/Wildcard_DNS_record)（例如 `*.contoso.com`）映射到应用服务应用。

#### <a name="access-dns-records-with-a-domain-provider"></a>通过域提供商访问 DNS 记录

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>创建 CNAME 记录

将通配符名称 `*` 映射到应用的默认域名（`<app-name>.azurewebsites.net`，其中 `<app-name>` 是应用的名称）。 若要映射通配符名称，请创建两条记录：

| 记录类型 | 主机 | Value | 注释 |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | 域映射本身。 |
| TXT | `asuid` | [之前获得的验证 ID](#get-a-domain-verification-id) | 应用服务访问 `asuid` TXT 记录以验证你对自定义域的所有权。 |

在 `*.contoso.com` 域示例中， CNAME 记录将把名称 `*` 映射到 `<app-name>.azurewebsites.net`。

添加 CNAME 时，DNS 记录页与以下示例相似：

![显示到 Azure 应用的导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>在应用中启用 CNAME 记录映射

现在，可以将任何与通配符名称匹配的子域（例如 `sub1.contoso.com`、`sub2.contoso.com` 和 `*.contoso.com` 都匹配 `*.contoso.com`）添加到应用中。

1. 在 Azure 门户中的应用页左侧窗格中，选择“自定义域”。

    ![显示“自定义域”菜单的屏幕截图。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 选择“添加自定义域”。

    ![演示如何添加主机名的屏幕截图。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 键入与通配符域相匹配的完全限定的域名（例如 `sub1.contoso.com`），然后选择“验证”。

    “添加自定义域”按钮随即激活。

1. 确保“主机名记录类型”设置为“CNAME 记录(www\.example.com 或任何子域)”。 选择“添加自定义域”。

    ![显示将 DNS 名称添加到应用的屏幕截图。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    新的自定义域可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。

1. 再次选择 **+** 图标，添加另一个与通配符域匹配的自定义域。 例如，添加 `sub2.contoso.com`。

    ![演示如何添加 CNAME 记录的屏幕截图。](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > 自定义域带有警告标签意味着该域尚未绑定到 TLS/SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告，具体取决于浏览器。 若要添加 TLS 绑定，请参阅[在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)。
    
## <a name="test-in-a-browser"></a>在浏览器中测试

浏览至你之前配置的 DNS 名称（例如 `contoso.com`、`www.contoso.com`、`sub1.contoso.com` 和 `sub2.contoso.com`）。

![显示到 Azure 应用的导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>解决 404“未找到”错误

如果在浏览到自定义域的 URL 时收到 HTTP 404（未找到）错误，请验证域是否使用 <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> 对应用的 IP 地址进行解析。 如果未使用，则可能是由于以下原因之一：

- 配置的自定义域缺少 A 记录或 CNAME 记录。
- 浏览器客户端已缓存域的旧 IP 地址。 清除缓存并再次测试 DNS 解析。 在 Windows 计算机上，使用 `ipconfig /flushdns` 清除缓存。

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>迁移活动域

若要将实时站点及其 DNS 域名迁移到应用服务而不停机，请参阅[将活动 DNS 名称迁移到 Azure 应用服务](manage-custom-dns-migrate-domain.md)。

## <a name="redirect-to-a-custom-directory"></a>重定向到自定义目录

默认情况下，应用服务将 Web 请求定向到应用代码的根目录下。 但某些 Web 框架不在根目录下启动。 例如，[Laravel](https://laravel.com/) 在 `public` 子目录中启动。 若要继续 `contoso.com` DNS 示例，此类应用可在 `http://contoso.com/public` 中访问，但你想要将 `http://contoso.com` 直接定向到 `public` 目录。 此步骤不涉及 DNS 解析，但会涉及到自定义虚拟目录。

若要自定义虚拟目录，请在 Web 应用页的左侧窗格中选择“应用程序设置”。

在页面底部，根虚拟目录 `/` 默认指向 `site\wwwroot`，这是应用代码的根目录。 将其改为指向例如 `site\wwwroot\public`，并保存所做的更改。

![演示如何自定义虚拟目录的屏幕截图。](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

操作完成后，应用会返回根路径的正确页面（例如 `http://contoso.com`）。

## <a name="automate-with-scripts"></a>使用脚本自动化

可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/) 通过脚本自动管理自定义域。

### <a name="azure-cli"></a>Azure CLI

以下命令将配置的自定义 DNS 名称添加到应用服务应用。

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

有关详细信息，请参阅[将自定义域映射到 Web 应用](scripts/cli-configure-custom-domain.md)。

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下命令将配置的自定义 DNS 名称添加到应用服务应用。

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

有关详细信息，请参阅[将自定义域分配到 Web 应用](scripts/powershell-configure-custom-domain.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 使用 CNAME 记录映射子域。
> * 使用 A 记录映射根域。
> * 使用 CNAME 记录映射通配符域。
> * 将默认 URL 重定向到自定义目录。
> * 使用脚本自动执行域映射。

继续学习下一教程，了解如何将自定义 TLS/SSL 证书绑定到 Web 应用。

> [!div class="nextstepaction"]
> [在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)
