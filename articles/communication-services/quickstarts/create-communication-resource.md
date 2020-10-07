---
title: 快速入门 - 在 Azure 通信服务中创建和管理资源
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入门中，你将了解如何创建和管理你的第一个 Azure 通信服务资源。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: 8b606108d0e33ba1dd49e37c0f407b0caa350f6b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91667497"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>快速入门：创建和管理通信服务资源

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

通过预配第一个通信服务资源来开始使用 Azure 通信服务。 可以通过 Azure 门户或使用 .NET 管理客户端库预配通信服务资源。 管理客户端库使你可以通过 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)（Azure 的部署和管理服务）创建、配置、更新和删除资源和接口。 客户端库中提供的所有功能都可在 Azure 门户中使用。 

> [!WARNING]
> 请注意，在公共预览版期间，通信服务仅限美国地区使用。 另请注意，在公共预览版期间，通信资源无法转移到其他订阅。

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>访问连接字符串和服务终结点

连接字符串使通信服务客户端库可以连接到 Azure 并向其进行身份验证。 你可以从 Azure 门户或使用 Azure 资源管理器 API 以编程方式访问通信服务连接字符串和服务终结点。 

导航到通信服务资源之后，从导航菜单中选择“密钥”并复制“连接字符串”或“终结点”值以供通信服务客户端库使用  。 请注意，你可以访问主密钥和辅助密钥。 在你希望向第三方或过渡环境提供对通信服务资源的临时访问权限的方案中，这可能会十分有用。

:::image type="content" source="./media/key.png" alt-text="通信服务密钥页的屏幕截图。":::

## <a name="store-your-connection-string"></a>存储连接字符串

通信服务客户端库使用连接字符串对向通信服务发出的请求进行授权。 可使用多个选项来存储连接字符串：

* 在桌面或设备上运行的应用程序可在 **app.config** 或 **web.config** 文件中存储连接字符串。 将连接字符串添加到这些文件中的 **AppSettings** 节。
* 在 Azure 应用服务中运行的应用程序可以将连接字符串存储在[应用服务应用程序设置](https://docs.microsoft.com/azure/app-service/configure-common)中。 在门户中将连接字符串添加到“应用程序设置”选项卡的“连接字符串”部分。
* 可以将连接字符串存储在 [Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 中。
* 如果在本地运行应用程序，则可能要将连接字符串存储在环境变量中。

### <a name="store-your-connection-string-in-an-environment-variable"></a>将连接字符串存储在环境变量中

若要配置环境变量，请打开控制台窗口，然后从以下选项卡中选择操作系统。 将 `<yourconnectionstring>` 替换为实际的连接字符串。

#### <a name="windows"></a>[Windows](#tab/windows)

打开控制台窗口并输入以下命令：

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

添加环境变量后，可能需要重启任何正在运行的、需要读取环境变量的程序（包括控制台窗口）。 例如，如果使用 Visual Studio 作为编辑器，请在运行示例之前重启 Visual Studio。

#### <a name="macos"></a>[macOS](#tab/unix)

编辑 .zshrc，然后添加环境变量：

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

添加环境变量后，请从控制台窗口运行 `source ~/.zshrc`，使更改生效。 如果在 IDE 打开的情况下创建了环境变量，则可能需要关闭编辑器、IDE 或 shell，然后再重新打开才能访问该变量。

#### <a name="linux"></a>[Linux](#tab/linux)

编辑 .bash_profile，然后添加环境变量：

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

添加环境变量后，请从控制台窗口运行 `source ~/.bash_profile`，使更改生效。 如果在 IDE 打开的情况下创建了环境变量，则可能需要关闭编辑器、IDE 或 shell，然后再重新打开才能访问该变量。

---

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

如果在删除资源时已为资源分配了任何电话号码，则会同时自动从资源释放电话号码。 

## <a name="next-steps"></a>后续步骤

在此快速入门中，你学习了如何：

> [!div class="checklist"]
> * 创建通信服务资源
> * 配置资源地理位置和标记
> * 访问该资源的密钥
> * 删除资源

> [!div class="nextstepaction"]
> [创建第一个用户访问令牌](access-tokens.md)
