---
title: 教程 - 将现有自定义域映射到 Azure Spring Cloud
description: 如何将现有的自定义分布式名称服务 (DNS) 名称映射到 Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 5892fd732a1e66b2b7dd4c1031cabfcbcc768c6d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326144"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>将现有自定义域映射到 Azure Spring Cloud

本文适用于：✔️ Java ✔️ C#

域名服务 (DNS) 是将网络节点名称存储在整个网络中的一种技术。 本教程使用 CNAME 记录来映射域（例如 www.contoso.com）。 本教程将使用证书保护自定义域，并介绍如何强制实施传输层安全性 (TLS)（也称为安全套接字层 (SSL)）。 

证书用于对 Web 流量进行加密。 可将这些 TLS/SSL 证书存储在 Azure Key Vault 中。 

## <a name="prerequisites"></a>先决条件
* 一个已部署到 Azure Spring Cloud 的应用程序（请参阅[快速入门：使用 Azure 门户启动现有 Azure Spring Cloud 应用程序](spring-cloud-quickstart.md)，或使用现有应用）。
* 一个有权访问域提供商（例如 GoDaddy）的 DNS 注册表的域名。
* 来自第三方提供商的私有证书（即你的自签名证书）。 该证书必须与域匹配。
* 一个已部署的 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 实例

## <a name="import-certificate"></a>导入证书 
导入证书的过程要求 PEM 或 PFX 编码的文件位于磁盘上，并且你必须具有私钥。 

若要将证书上传到密钥保管库，请执行以下操作：
1. 转到你的密钥保管库实例。
1. 在左侧导航窗格中，单击“证书”。
1. 在上部菜单中，单击“生成/导入”。
1. 在“创建证书”对话框中，在“证书创建方法”下，选择 `Import` 。
1. 在“上传证书文件”下，导航到证书所在位置并选择该证书。
1. 在“密码”下，输入证书的私钥。
1. 单击“创建”。

    ![导入证书 1](./media/custom-dns-tutorial/import-certificate-a.png)

若要在导入证书之前，授予 Azure Spring Cloud 对密钥库的访问权限，请执行以下操作：
1. 转到你的密钥保管库实例。
1. 在左侧导航窗格中，单击“访问策略”。
1. 在上方菜单中，单击“添加访问策略”。
1. 填写信息，单击“添加按钮，然后保存访问策略。

| 机密权限 | 证书权限 | 选择主体 |
|--|--|--|
| 获取、列出 | 获取、列出 | Azure Spring Cloud 域管理 |

![导入证书 2](./media/custom-dns-tutorial/import-certificate-b.png)

或者，可以使用 Azure CLI 授予 Azure Spring Cloud 对密钥库的访问权限。

通过以下命令获取对象 ID。
```
az ad sp show --id <service principal id> --query objectId
```

授予 Azure Spring Cloud 对密钥库的读取权限，并替换以下命令中的对象 ID。
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list
``` 

若要将证书导入 Azure Spring Cloud，请执行以下操作：
1. 转到你的服务实例。 
1. 在你的应用的左侧导航窗格中，选择“TLS/SSL 设置”。
1. 然后单击“导入 Key Vault 证书”。

    ![导入证书](./media/custom-dns-tutorial/import-certificate.png)

或者，可以使用 Azure CLI 导入证书：

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> 在执行上一个导入证书命令之前，请确保已授予 Azure Spring Cloud 对密钥库的访问权限。 如果还没有，则可以执行以下命令来授予访问权限。

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

成功导入证书后，会在“私钥证书”列表中看到它。

![私钥证书](./media/custom-dns-tutorial/key-certificates.png)

或者，可以使用 Azure CLI 显示证书列表：

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```

> [!IMPORTANT] 
> 若要使用此证书保护自定义域，仍需将此证书绑定到特定的域。 请执行本文档中标题为“添加 SSL 绑定”的部分中的步骤。

## <a name="add-custom-domain"></a>添加自定义域
可以使用 CNAME 记录将自定义 DNS 名称映射到 Azure Spring Cloud。 

> [!NOTE] 
> 不支持 A 记录。 

### <a name="create-the-cname-record"></a>创建 CNAME 记录
转到你的 DNS 提供商，添加一条 CNAME 记录以将域映射到 <service_name>.azuremicroservices.io。 此处的 <service_name> 是 Azure Spring Cloud 实例的名称。 我们支持通配符域和子域。 添加 CNAME 后，DNS 记录页将类似于以下示例： 

![DNS 记录页](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>将自定义域映射到 Azure Spring Cloud 应用
如果你在 Azure Spring Cloud 中没有应用程序，请按照以下教程操作：[快速入门：使用 Azure 门户启动现有 Azure Spring Cloud 应用程序](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master)。

转到应用程序页。

1. 选择“自定义域”。
2. 然后选择“添加自定义域”。 

    ![自定义域](./media/custom-dns-tutorial/custom-domain.png)

3. 键入你为其添加了 CNAME 记录的完全限定的域名，例如 www.contoso.com。 请确保主机名记录类型设置为 CNAME (<service_name>.azuremicroservices.io)
4. 单击“验证”以启用“添加”按钮 。
5. 单击“添加”。

    ![添加自定义域](./media/custom-dns-tutorial/add-custom-domain.png)

或者，可以使用 Azure CLI 添加自定义域：
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

一个应用可以有多个域，但一个域只能映射到一个应用。 成功将自定义域映射到应用后，会在自定义域表中看到该域。

![自定义域表](./media/custom-dns-tutorial/custom-domain-table.png)

或者，可以使用 Azure CLI 显示自定义域列表：
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```

> [!NOTE]
> 自定义域带有“不安全”标签意味着该域尚未绑定到 SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告。

## <a name="add-ssl-binding"></a>添加 SSL 绑定
在自定义域表中，如上图所示选择“添加 SSL 绑定”。  
1. 选择或导入你的证书。
1. 单击“ **保存**”。

    ![添加 SSL 绑定 1](./media/custom-dns-tutorial/add-ssl-binding.png)

或者，可以使用 Azure CLI 添加 SSL 绑定：
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

成功添加 SSL 绑定后，域将处于安全状态：“正常运行”。 

![添加 SSL 绑定 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>实施 HTTPS
默认情况下，所有人仍可使用 HTTP 访问你的应用，但你可以将所有 HTTP 请求重定向到 HTTPS 端口。

在应用页的左侧导航窗格中，选择“自定义域”。 然后，将“仅限 HTTPS”设置为“True”。

![添加 SSL 绑定 3](./media/custom-dns-tutorial/enforce-http.png)

或者，可以使用 Azure CLI 强制实施 HTTPS：
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```

该操作完成后，导航到指向你的应用的任一 HTTP URL。 请注意，HTTP URL 不起作用。

## <a name="see-also"></a>另请参阅
* [什么是 Azure 密钥保管库？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [导入证书](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [使用 Azure CLI 启动 Spring Cloud 应用](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

