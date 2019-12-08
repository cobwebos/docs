---
title: 使用 SSL 绑定保护自定义 DNS
description: 通过证书创建 TLS/SSL 绑定，以便保护对自定义域进行的 HTTPS 访问。 通过强制实施 HTTPS 或 TLS 1.2 提高网站的安全性。
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b967e4cfad2444e39c7df8cfddcc2154bd48367d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670678"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>在 Azure 应用服务中使用 SSL 绑定保护自定义 DNS 名称

本文介绍如何通过创建证书绑定来确保[应用服务应用](https://docs.microsoft.com/azure/app-service/)或[函数应用](https://docs.microsoft.com/azure/azure-functions/)中[自定义域](app-service-web-tutorial-custom-domain.md)的安全。 完成后，可访问自定义 DNS 名称（例如，`https://www.contoso.com`）的 `https://` 终结点处的应用服务应用。 

![包含自定义 SSL 证书的 Web 应用](./media/configure-ssl-bindings/app-with-custom-ssl.png)

使用证书来确保[自定义域](app-service-web-tutorial-custom-domain.md)的安全涉及两个步骤：

- [将专用证书添加到应用服务](configure-ssl-certificate.md)，以满足所有 [SSL 绑定要求](configure-ssl-certificate.md#private-certificate-requirements)。
-  创建相应自定义域的 SSL 绑定。 本文介绍第二步。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 升级应用的定价层
> * 使用证书确保自定义域的安全
> * 实施 HTTPS
> * 强制实施 TLS 1.1/1.2
> * 使用脚本自动完成 TLS 管理

## <a name="prerequisites"></a>先决条件

按照本操作方法指南操作：

- [创建应用服务应用](/azure/app-service/)
- [将域名映射到应用](app-service-web-tutorial-custom-domain.md)或[在 Azure 中购买并配置](manage-custom-dns-buy-domain.md)
- [将专用证书添加到应用](configure-ssl-certificate.md)

> [!NOTE]
> 添加专用证书最简单的方法是[创建免费的应用服务托管证书](configure-ssl-certificate.md#create-a-free-certificate-preview)（预览版）。

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>确保自定义域的安全

请执行以下步骤：

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的左侧菜单中，选择“应用服务” > “\<app-name>”   。

在应用的左侧导航窗格中，通过以下方式启动“TLS/SSL 绑定”对话框  ：

- 选择“自定义域” > “添加绑定”  
- 选择“TLS/SSL 设置” > “添加 TLS/SSL 绑定”  

![为域添加绑定](./media/configure-ssl-bindings/secure-domain-launch.png)

在“自定义域”中，选择要添加绑定的自定义域  。

如果应用已具有所选自定义域的证书，请直接转到[创建绑定](#create-binding)。 反之，请继续操作。

### <a name="add-a-certificate-for-custom-domain"></a>为自定义域添加证书

如果应用不具有所选自定义域的证书，则有以下两种选择：

- **上传 PFX 证书** - 遵循[上传专用证书](configure-ssl-certificate.md#upload-a-private-certificate)中的工作流，然后在此处选择此选项。
- **导入应用服务证书** - 遵循[导入应用服务证书](configure-ssl-certificate.md#import-an-app-service-certificate)中的工作流，然后在此处选择此选项。

> [!NOTE]
> 还可以[创建免费证书](configure-ssl-certificate.md#create-a-free-certificate-preview)（预览版）或[导入 Key Vault 证书](configure-ssl-certificate.md#import-a-certificate-from-key-vault)，但必须单独执行，然后返回到“TLS/SSL 绑定”对话框  。

### <a name="create-binding"></a>创建绑定

使用下表帮助在“TLS/SSL 绑定”对话框中配置 SSL 绑定，然后单击“添加绑定”   。

| 设置 | 说明 |
|-|-|
| 自定义域 | 要添加 SSL 绑定的域名。 |
| 私有证书指纹 | 要绑定的证书。 |
| TLS/SSL 类型 | <ul><li>[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication) - 可添加多个 SNI SSL 绑定  。 选择此选项可以使用多个 SSL 证书来保护同一 IP 地址上的多个域。 大多数新式浏览器（包括 Internet Explorer、Chrome、Firefox 和 Opera）都支持 SNI（有关详细信息，请参阅[服务器名称指示](https://wikipedia.org/wiki/Server_Name_Indication)）。</li><li>**IP SSL** - 只能添加一个 IP SSL 绑定。 选择此选项只能使用一个 SSL 证书来保护专用公共 IP 地址。 配置绑定后，请按照[重新映射 IP SSL 的 A 记录](#remap-a-record-for-ip-ssl)中的步骤进行操作。<br/>仅生产或隔离层中支持 IP SSL。 </li></ul> |

操作完成之后，自定义域的 SSL 状态将更改为“安全”  。

![SSL 绑定成功](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> “自定义域”中的状态为“安全”意味着已使用证书保护该域，但应用服务并未检查该证书是自签名证书还是已过期证书，这可能也会导致浏览器异常，例如显示错误或警告。  

## <a name="remap-a-record-for-ip-ssl"></a>重新映射 IP SSL 的 A 记录

如果不在应用中使用 IP SSL，请跳到[针对自定义域测试 HTTPS](#test-https)。

默认情况下，应用使用共享的公共 IP 地址。 将证书与 IP SSL 绑定时，应用服务会为应用创建新的专用 IP 地址。

如果已将 A 记录映射到应用，请使用这个新的专用 IP 地址更新域注册表。

将使用新的专用 IP 地址更新应用的“自定义域”页。  [复制此 IP 地址](app-service-web-tutorial-custom-domain.md#info)，然后[将 A 记录重新映射](app-service-web-tutorial-custom-domain.md#map-an-a-record)到此新 IP 地址。

## <a name="test-https"></a>测试 HTTPS

在不同的浏览器中，导航到 `https://<your.custom.domain>` 以核实其是否适合应用。

![在门户中导航到 Azure 应用](./media/configure-ssl-bindings/app-with-custom-ssl.png)

> [!NOTE]
> 如果应用显示证书验证错误，可能是因为使用自签名证书。
>
> 如果不是这样，可能是在将证书导出为 PFX 文件时遗漏了中间证书。

## <a name="prevent-ip-changes"></a>防止 IP 更改

在删除某个绑定时，即使该绑定是 IP SSL，入站 IP 地址也可能会更改。 在续订已进行 IP SSL 绑定的证书时，了解这一点尤为重要。 若要避免应用的 IP 地址更改，请按顺序执行以下步骤：

1. 上传新证书。
2. 将新证书绑定到所需的自定义域，不要删除旧证书。 此操作替换而不是删除旧的绑定。
3. 删除旧证书。 

## <a name="enforce-https"></a>实施 HTTPS

默认情况下，任何人都仍可使用 HTTP 访问应用。 可以将所有 HTTP 请求都重定向到 HTTPS 端口。

在应用页的左侧导航窗格中，选择“SSL 设置”  。 然后，在“仅 HTTPS”  中，选择“启用”  。

![实施 HTTPS](./media/configure-ssl-bindings/enforce-https.png)

该操作完成后，将导航到指向应用的任一 HTTP URL。 例如：

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>强制实施 TLS 版本

应用默认情况下允许 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2，这是行业标准（例如 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)）建议的 TLS 级别。 若要强制实施不同的 TLS 版本，请按照下列步骤操作：

在应用页的左侧导航窗格中，选择“SSL 设置”  。 然后，在“TLS 版本”  中，选择所需的最低 TLS 版本。 此设置仅控制入站调用。 

![强制实施 TLS 1.1 或 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

该操作完成后，你的应用将拒绝使用更低 TLS 版本的所有连接。

## <a name="automate-with-scripts"></a>使用脚本自动化

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>更多资源

* [在应用程序代码中使用 SSL 证书](configure-ssl-certificate-in-code.md)
* [常见问题解答：应用服务证书](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
