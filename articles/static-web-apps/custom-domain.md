---
title: 在 Azure 静态 Web 应用中设置自定义域
description: 了解如何将自定义域映射到 Azure 静态 Web 应用
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 10d3e3674aef901ad35dfb6d0b9fd13715aace55
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594936"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>在 Azure 静态 Web 应用预览中设置自定义域

Azure 静态 Web 应用默认提供自动生成的域名。 本文介绍如何将自定义域名映射到 Azure 静态 Web 应用应用程序。

## <a name="prerequisites"></a>先决条件

- 一个购买的域名
- 访问域的 DNS 配置属性

配置域名时，“A”记录用于将根域（例如 `example.com`）映射到 IP 地址。 必须将根域直接映射到 IP 地址，因为 DNS 规范不允许将一个域映射到另一个域。

## <a name="dns-configuration-options"></a>DNS 配置选项

有几种不同类型的 DNS 配置可用于应用程序。

| 如果你想要                            | 则                                                |
| ----------------------------------------- | --------------------------------------------------- |
| 支持 `www.example.com`                 | [映射 CNAME 记录](#map-a-cname-record)           |
| 支持 `example.com`                     | [配置根域](#configure-a-root-domain) |
| 将所有子域指向 `www.example.com` | [映射通配符](#map-a-wildcard-domain)                   |

## <a name="map-a-cname-record"></a>映射 CNAME 记录

CNAME 记录将一个域映射到另一个域。 可以使用 CNAME 记录将 `www.example.com` 映射到由 Azure 静态 Web 应用提供的自动生成的域。

1. 打开 [Azure 门户](https://portal.azure.com)，然后使用 Azure 帐户登录。

1. 搜索并选择“静态 Web 应用”

1. 在“静态 Web 应用”页上，选择应用的名称。

1. 在菜单中单击“自定义域”。

1. 在“自定义域”窗口中，将 URL 复制到“值”字段。

### <a name="configure-dns-provider"></a>配置 DNS 提供程序

1. 请登录到域提供商的网站。

2. 查找管理 DNS 记录的页面。 每个域提供商都有自己的 DNS 记录界面，因此请查阅提供商的文档。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。  

3. 通常通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到 DNS 记录页面。 转到该页面，然后查找名称类似于“区域文件”、“DNS 记录”或“高级配置”的链接  。

    以下屏幕截图是 DNS 记录页的一个示例：

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="DNS 提供程序配置示例":::

4. 使用以下值创建新的 CNAME 记录...

    | 设置             | 值                     |
    | ------------------- | ------------------------- |
    | 类型                | CNAME                     |
    | 主机                | www                       |
    | 值               | 从剪贴板粘贴 |
    | TTL（如果适用） | 保留为默认值    |

5. 保存 DNS 提供程序所做的更改。

### <a name="validate-cname"></a>验证 CNAME

1. 返回到 Azure 门户中的“自定义域”窗口。

1. 输入域，包括“验证自定义域”部分中的 `www` 部分。

1. 单击“验证”按钮。

现在，自定义域已完成配置，DNS 提供程序可能需要几个小时才能在全球范围内传播更改。 可以通过转到 [dnspropagation.net](https://dnspropagation.net) 检查传播的状态。 输入包含 `www` 的域自定义域，从下拉菜单中选择“CNAME”，然后选择“开始”。

如果 DNS 更改已填充，则网站将返回静态 Web 应用的自动生成的 URL（例如 _random-name-123456789c.azurestaticapps.net_）。

## <a name="configure-a-root-domain"></a>配置根域

根域是域删去任何子域，包括 `www`。 例如，`www.example.com` 的根域 `example.com`。 这也称为“APEX”域。

尽管根域支持在预览期间不可用，但你可以查看博客文章[在 Azure 静态 Web Apps 中配置根域](https://burkeholland.github.io/posts/static-app-root-domain)详细了解如何使用静态 Web 应用配置根域支持。

## <a name="map-a-wildcard-domain"></a>映射通配符域

有时，你希望发送到子域的所有流量路由到另一个域。 常见的示例是将所有子域流量映射到 `www.example.com`。 这样一来，即使有人键入 `ww.example.com`（而不是 `www.example.com`），请求也将发送给 `www.example.com`。

### <a name="configure-dns-provider"></a>配置 DNS 提供程序

1. 请登录到域提供商的网站。

2. 查找管理 DNS 记录的页面。 每个域提供商都有自己的 DNS 记录界面，因此请查阅提供商的文档。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。  

3. 通常通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到 DNS 记录页面。 转到该页面，然后查找名称类似于“区域文件”、“DNS 记录”或“高级配置”的链接  。

    以下屏幕截图是 DNS 记录页的一个示例：

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="DNS 提供程序配置示例":::

4. 使用以下值创建新的 CNAME 记录，并将 `www.example.com` 替换为自定义域名。

    | 设置 | 值                  |
    | ------- | ---------------------- |
    | 类型    | CNAME                  |
    | 主机    | \*                     |
    | 值   | www.example.com        |
    | TTL     | 保留为默认值 |

5. 保存 DNS 提供程序所做的更改。

现在，通配符域已完成配置，更改可能需要几个小时才能在全球范围内传播。 可以通过转到 [dnspropagation.net](https://dnspropagation.net) 检查传播的状态。 输入包含带有任何子域（`www` 除外）的域自定义域，从下拉菜单中选择“CNAME”，然后选择“开始”。

如果已填充了 DNS 更改，则网站将返回为静态 Web 应用配置的自定义域（例如 `www.example.com`）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置应用设置](application-settings.md)
