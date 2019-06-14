---
title: 在门户-Azure 应用服务中配置应用
description: 了解如何在 Azure 门户中配置应用服务应用的常见设置。
keywords: azure 应用服务、 web 应用、 应用设置、 环境变量
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957905"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>在 Azure 门户中配置应用服务应用

本主题介绍如何配置 web 应用、 移动后端或 API 应用使用的通用设置[Azure 门户]。

## <a name="configure-app-settings"></a>配置应用设置

在应用服务中，您可以使用应用设置，例如环境变量。 在中[Azure 门户]，导航到应用程序管理页面。 在应用程序的左侧菜单中，单击**配置** > **应用程序设置**。

![应用程序设置](./media/configure-common/open-ui.png)

对于 ASP.NET 和 ASP.NET Core 开发人员，在应用服务中的设置应用设置如下所示在中进行设置`<appSettings>`中*Web.config*，但在应用服务中的值重写中的*Web.config*。您可以将开发设置 （例如本地 MySQL 密码） 在*Web.config*，但生产机密 （例如 Azure MySQL 数据库密码） 在应用服务中的安全。 相同的代码时，在本地调试和使用生产机密部署到 Azure 时使用您的开发设置。

其他语言的堆栈，同样，在运行时获取的应用程序设置作为环境变量。 有关语言堆栈的具体步骤，请参阅：

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [自定义容器](containers/configure-custom-container.md#configure-environment-variables)

应用程序设置在存储时始终进行加密（静态加密）。

> [!NOTE]
> 应用程序设置还可以从解析[Key Vault](/azure/key-vault/)使用[Key Vault 引用](app-service-key-vault-references.md)。

### <a name="show-hidden-values"></a>显示隐藏的值

默认情况下，应用设置的值是隐藏的门户中的安全。 若要查看的应用设置的隐藏的值，请单击**值**该设置的字段。 若要查看所有应用设置的值，请单击**显示值**按钮。

### <a name="add-or-edit"></a>添加或编辑

若要添加的新应用设置，请单击**新的应用程序设置**。 在对话框中，你可以[停在当前槽设置](deploy-staging-slots.md#which-settings-are-swapped)。

若要编辑设置，请单击**编辑**右侧的按钮。

完成后，单击**更新**。 别忘了单击**保存**回到**配置**页。

> [!NOTE]
> 默认 Linux 容器或自定义 Linux 容器，在任何嵌套的 JSON 键结构中应用设置名称，如`ApplicationInsights:InstrumentationKey`需要与应用服务中配置`ApplicationInsights__InstrumentationKey`密钥名称。 换而言之，任何`:`应该替换为`__`（双下划线）。
>

### <a name="edit-in-bulk"></a>批量编辑

若要添加或编辑中大容量的应用设置，请单击**高级编辑**按钮。 完成后，单击**更新**。 别忘了单击**保存**回到**配置**页。

应用程序设置具有以下 JSON 格式设置：

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>配置连接字符串

在中[Azure 门户]，导航到应用程序的管理页。 在应用程序的左侧菜单中，单击**配置** > **应用程序设置**。

![应用程序设置](./media/configure-common/open-ui.png)

对于 ASP.NET 和 ASP.NET Core 开发人员，在应用服务中的设置连接字符串如下所示在中进行设置`<connectionStrings>`中*Web.config*，但在应用服务中设置的值重写中的*Web.config*.可以将开发设置 （如数据库文件） 保存在*Web.config*和生产机密 （例如 SQL 数据库凭据），在应用服务中的安全。 相同的代码时，在本地调试和使用生产机密部署到 Azure 时使用您的开发设置。

对于其他语言的堆栈，最好是使用[应用设置](#configure-app-settings)相反，因为连接字符串要求中的变量键中的特殊格式设置才能访问的值。 下面是一个例外，但是： 某些 Azure 数据库备份类型以及应用如果应用程序中配置其连接字符串。 有关详细信息，请参阅[备份的内容](manage-backup.md#what-gets-backed-up)。 如果不需要此自动的备份，然后使用应用设置。

在运行时，连接字符串可作为环境变量，使用以下连接类型作为前缀：

* SQL Server：`SQLCONNSTR_`
* MySQL：`MYSQLCONNSTR_`
* SQL 数据库：`SQLAZURECONNSTR_`
* 自定义：`CUSTOMCONNSTR_`

例如，名为 MySql 连接字符串*connectionstring1*可作为环境变量访问`MYSQLCONNSTR_connectionString1`。 有关语言堆栈的具体步骤，请参阅：

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [自定义容器](containers/configure-custom-container.md#configure-environment-variables)

连接字符串在存储时始终进行加密（静态加密）。

> [!NOTE]
> 连接字符串还可以从解析[Key Vault](/azure/key-vault/)使用[Key Vault 引用](app-service-key-vault-references.md)。

### <a name="show-hidden-values"></a>显示隐藏的值

默认情况下，连接字符串的值的隐藏的门户中的安全。 若要查看连接字符串的隐藏的值，只需单击**值**该字符串的字段。 若要查看所有连接字符串值，请单击**显示值**按钮。

### <a name="add-or-edit"></a>添加或编辑

若要添加新的连接字符串，请单击**新的连接字符串**。 在对话框中，你可以[停在当前槽的连接字符串](deploy-staging-slots.md#which-settings-are-swapped)。

若要编辑设置，请单击**编辑**右侧的按钮。

完成后，单击**更新**。 别忘了单击**保存**回到**配置**页。

### <a name="edit-in-bulk"></a>批量编辑

若要添加或编辑连接字符串中大容量，请单击**高级编辑**按钮。 完成后，单击**更新**。 别忘了单击**保存**回到**配置**页。

连接字符串有以下 JSON 格式设置：

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>配置常规设置

在中[Azure 门户]，导航到应用程序的管理页。 在应用程序的左侧菜单中，单击**配置** > **应用程序设置**。

![常规设置](./media/configure-common/open-general.png)

在这里，可以配置应用程序的某些常见设置。 某些设置要求你[纵向扩展到更高的定价层](web-sites-scale.md)。

- **堆栈设置**:要运行应用程序，包括语言和 SDK 版本的软件堆栈。 对于 Linux 应用和自定义容器应用程序，还可以设置的可选启动命令或文件。
- **平台设置**:允许您配置设置的托管平台，包括：
    - **位数**:32 位或 64 位。
    - **WebSocket 协议**:有关[ASP.NET SignalR]或[socket.io](https://socket.io/)，例如。
    - **Alwayson**:保持加载没有流量，即使该应用程序。 您需要为连续 web 作业或使用 CRON 表达式触发的 web 作业启用它。
    - **托管的管道版本**:IIS[管道模式]。 将其设置为**经典**如果旧版应用需要旧版 IIS。
    - **HTTP 版本**:设置为 **2.0**，以启用对 [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 协议的支持。
    > [!NOTE]
    > 大多数新型浏览器仅支持通过 TLS 的 HTTP/2 协议，而非加密流量继续使用 HTTP/1.1。 若要确保客户端浏览器连接到使用 HTTP/2，对应用程序要么[购买应用服务证书](web-sites-purchase-ssl-web-site.md)的应用的自定义域或[将第三方证书绑定](app-service-web-tutorial-custom-ssl.md)。
    - **ARR 相关性**:在多实例部署中，确保将客户端路由到该会话的生存期内的同一实例。 此选项设置为**关闭**的无状态应用程序。
- **调试**:为启用远程调试[ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)， [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)，或[Node.js](containers/configure-language-nodejs.md#debug-remotely)应用。 此选项将在 48 小时后自动关闭。
- **传入的客户端证书**： 要求中的客户端证书[相互身份验证](app-service-web-configure-tls-mutual-auth.md)。

## <a name="configure-default-documents"></a>配置默认文档

此设置仅适用于 Windows 应用。

在中[Azure 门户]，导航到应用程序的管理页。 在应用程序的左侧菜单中，单击**配置** > **默认文档**。

![常规设置](./media/configure-common/open-documents.png)

默认文档是在网站的根 URL 显示的网页。 使用的是列表中的第一个匹配文件。 若要添加新的默认文档，请单击**新的文档**。 别忘了单击**保存**。

如果应用使用基于 URL 而不是提供静态内容的路由模块，则不需要默认文档。

## <a name="configure-path-mappings"></a>配置路径映射

在中[Azure 门户]，导航到应用程序的管理页。 在应用程序的左侧菜单中，单击**配置** > **路径映射**。

![常规设置](./media/configure-common/open-path.png)

**路径映射**页显示不同的事物的 OS 类型。

### <a name="windows-apps-uncontainerized"></a>Windows 应用 (uncontainerized)

对于 Windows 应用程序，可以自定义 IIS 处理程序映射和虚拟应用程序和目录。

处理程序映射，可以添加自定义脚本处理器以处理针对特定文件扩展名的请求。 若要添加自定义处理程序，请单击**新的处理程序**。 配置的处理程序，如下所示：

- **扩展名**。 你想要处理，如文件扩展名 *\*.php*或*handler.fcgi*。
- **脚本处理器**。 向你的脚本处理器的绝对路径。 由脚本处理器处理的文件匹配的文件扩展名的请求。 使用路径 `D:\home\site\wwwroot` 表示应用的根目录。
- **参数**。 脚本处理器的可选命令行参数。

每个应用都具有默认的根路径 (`/`) 映射到`D:\home\site\wwwroot`，默认情况下部署代码的位置。 如果您应用的根位于另一个文件夹，或者如果你的存储库具有多个应用程序，可以编辑或添加虚拟应用程序和目录下面。 单击**新虚拟应用程序或目录**。

若要配置虚拟应用程序和目录，请指定每个虚拟目录和其对应于网站根目录的物理路径 (`D:\home`)。 还可选中“应用程序”复选框，将虚拟目录标记为应用程序。 

### <a name="containerized-apps"></a>容器化的应用

你可以[添加为容器化应用的自定义存储](containers/how-to-serve-content-from-azure-storage.md)。 容器化的应用包括所有 Linux 应用程序以及 Windows 和 Linux 的自定义容器，应用服务上运行。 单击**新的 Azure 存储装载**并配置自定义存储，如下所示：

- **名称**：显示名称中。
- **配置选项**:**基本**或**高级**。
- **存储帐户**：与此容器所需存储帐户。
- **存储类型**：**Azure Blob**或**Azure 文件**。
  > [!NOTE]
  > Windows 容器应用仅支持 Azure 文件。
- **存储容器**：对于基本配置，所需的容器。
- **共享名**:对于高级配置，该文件共享的名称。
- **访问密钥**:对于高级配置，请访问密钥。
- **装载路径**:若要将自定义存储装载到容器中的绝对路径。

有关详细信息，请参阅[通过在 Linux 上的应用服务中的 Azure 存储提供内容](containers/how-to-serve-content-from-azure-storage.md)。

## <a name="configure-language-stack-settings"></a>配置语言堆栈设置

Linux 应用程序，请参阅：

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>配置自定义容器

请参阅[为 Azure 应用服务配置自定义 Linux 容器](containers/configure-custom-container.md)

## <a name="next-steps"></a>后续步骤

- [在 Azure 应用服务中配置自定义域名]
- [设置过渡环境，在 Azure 应用服务]
- [为 Azure 应用服务中的应用启用 HTTPS]
- [启用诊断日志](troubleshoot-diagnostic-logs.md)
- [在 Azure 应用服务中缩放应用]
- [在 Azure 应用服务中监视基础知识]
- [更改与 applicationHost.xdt applicationHost.config 设置](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure 门户]: https://portal.azure.com/
[在 Azure 应用服务中配置自定义域名]: ./app-service-web-tutorial-custom-domain.md
[设置过渡环境，在 Azure 应用服务]: ./deploy-staging-slots.md
[为 Azure 应用服务中的应用启用 HTTPS]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[在 Azure 应用服务中监视基础知识]: ./web-sites-monitor.md
[管道模式]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[在 Azure 应用服务中缩放应用]: ./web-sites-scale.md
