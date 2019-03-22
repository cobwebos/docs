---
title: 排查 Azure 应用程序网关会话相关性问题
description: 本文介绍如何排查 Azure 应用程序网关中的会话相关性问题
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 157cbd9b05f7f2af58df732a1ca0329926a200da
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123209"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>排查 Azure 应用程序网关会话相关性问题

了解如何诊断和解决 Azure 应用程序网关的会话相关性问题。

## <a name="overview"></a>概述

需要在同一服务器上保留用户会话时，可以使用基于 Cookie 的会话相关性功能。 借助网关托管的 Cookie，应用程序网关可以将来自用户会话的后续流量定向到同一服务器进行处理。 在用户会话的会话状态在服务器上进行本地保存的情况下，此功能十分重要。

## <a name="possible-problem-causes"></a>问题的可能原因

基于 Cookie 的会话相关性问题的主要可能原因如下：

- 未启用“基于 Cookie 的相关性”设置
- 应用程序无法处理基于 Cookie 的相关性
- 应用程序使用基于 Cookie 的相关性，但请求仍在后端服务器之间弹跳

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>检查是否启用了“基于 Cookie 的相关性”设置

如果你忘记了启用“基于 Cookie 的相关性”设置，则有时会出现会话相关性问题。 若要确定是否在 Azure 门户中的“HTTP 设置”选项卡上启用了“基于 Cookie 的相关性”设置，请遵照以下说明操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 在**左侧导航**窗格中，单击“所有资源”。 在“所有资源”边栏选项卡中单击应用程序网关名称。 如果所选订阅中已包含多个资源，可在“按名称筛选…”中输入应用程序网关名称。 轻松访问应用程序网关。

3. 选择“设置”下的“HTTP 设置”选项卡。

   ![troubleshoot-session-affinity-issues-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 单击右侧的“appGatewayBackendHttpSettings”，检查是否为“基于 Cookie 的相关性”选择了“已启用”。

   ![troubleshoot-session-affinity-issues-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



也可以使用以下方法之一，检查“backendHttpSettingsCollection”下的“CookieBasedAffinity”值是否设置为“Enabled”：

- 在 PowerShell 中运行 [Get-AzureRmApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermapplicationgatewaybackendhttpsettings?view=azurermps-4.1.0)
- 使用 Azure 资源管理器模板通查 JSON 文件

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>应用程序无法处理基于 Cookie 的相关性

#### <a name="cause"></a>原因

应用程序网关只能使用 Cookie 执行基于会话的相关性。

#### <a name="workaround"></a>解决方法

如果应用程序无法处理基于 Cookie 的相关性，则必须使用外部或内部 Azure 负载均衡器或其他第三方解决方案。

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>应用程序使用基于 Cookie 的相关性，但请求仍在后端服务器之间弹跳

#### <a name="symptom"></a>症状

你已启用“基于 Cookie 的相关性”设置，但在 Internet Explorer 中使用短名 URL（例如 [http://website](http://website/)）访问应用程序网关时，请求仍在后端服务器之间弹跳。

若要识别此问题，请遵照以下说明操作：

1. 在连接到应用程序网关后面的应用程序的“客户端”上提取 Web 调试器跟踪（本示例使用 Fiddler）。
    **提示**如果你不知道如何使用 Fiddler，请选中底部的“我想要收集网络流量并使用 Web 调试器分析它”选项。

2. 检查并分析会话日志，确定客户端提供的 Cookie 是否包含 ARRAffinity 详细信息。 如果在 Cookie 集中找不到类似于 "**ARRAffinity=** *ARRAffinityValue*" 的 ARRAffinity 详细信息，则表示客户端未使用应用程序网关提供的 ARRA Cookie 做出回复。
    例如：

    ![troubleshoot-session-affinity-issues-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

        ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

应用程序会持续尝试针对每个请求设置 Cookie，直到收到回复。

#### <a name="cause"></a>原因

此问题的可能原因是 Internet Explorer 和其他浏览器无法存储或使用包含短名 URL 的 Cookie。

#### <a name="resolution"></a>解决方法

若要解决此问题，应使用 FQDN 访问应用程序网关。 例如，使用 [http://website.com](http://website.com/) 或 [http://appgw.website.com](http://appgw.website.com/)。

## <a name="additional-logs-to-troubleshoot"></a>其他日志进行故障排除

可以收集其他日志并对其进行故障排除问题相关的基于 cookie 的会话相关性进行分析

### <a name="analyze-application-gateway-logs"></a>分析应用程序网关日志

若要收集应用程序网关日志，请按照说明操作：

通过 Azure 门户启用日志记录

1. 在中[Azure 门户](https://portal.azure.com/)，找到你的资源，然后单击**诊断日志**。

   对于应用程序网关，提供 3 种日志：访问日志、 性能日志、 防火墙日志

2. 若要开始收集数据，请单击**启用诊断**。

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. “诊断设置”边栏选项卡提供用于诊断日志的设置。 本示例使用 Log Analytics 存储日志。 单击**配置**下**Log Analytics**设置你的工作区。 也可使用事件中心和存储帐户保存诊断日志。

   ![troubleshoot-session-affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 确认设置，然后单击**保存**。

   ![troubleshoot-session-affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>查看和分析应用程序网关访问日志

1. 在 Azure 门户中应用程序网关资源视图下，选择**诊断日志**中**监视**部分。

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 在右侧窗格中，选择"**ApplicationGatewayAccessLog**"下的下拉列表中**日志类别。**  

   ![troubleshoot-session-affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. 在应用程序网关访问日志列表中，单击你想要分析并导出，请在的日志，然后导出 JSON 文件。

4. 转换到 CSV 文件的步骤 3 中导出的 JSON 文件，并在 Excel、 Power BI 或任何其他数据可视化工具中查看它们。

5. 检查以下数据：

- **ClientIP**– 这是从连接的客户端的客户端 IP 地址。
- **ClientPort** -这是从连接的客户端请求的源端口。
- **RequestQuery** – 这表示目标服务器收到的请求。
- **Server-Routed**：收到的请求的后端池实例。
- **X-AzureApplicationGateway-LOG-ID**：用于请求的相关 ID。 它可用于排查后端服务器上的流量问题。 例如：X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**：应用程序网关接收从后端的 HTTP 响应代码。

  ![troubleshoot-session-affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

如果您看到的两个项即将从相同的 ClientIP 和客户端端口，并向其发送到相同的后端服务器，这意味着应用程序网关配置正确。

如果您看到的两个项即将从相同的 ClientIP 和客户端端口，并向其发送到不同的后端服务器，这意味着请求弹跳之间后端服务器，选择"**应用程序使用基于 cookie 的相关性请求但后端服务器之间仍弹跳**"在故障排除方法的底部。

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>使用 web 调试器来捕获和分析 HTTP 或 HTTPS 流量

Web Fiddler 之类的调试工具，可帮助你调试 web 应用程序通过捕获在 Internet 和测试计算机之间的网络流量。 这些工具使您能够检查传入和传出数据，如在浏览器接收/发送它们。 Fiddler，在此示例中，具有 HTTP 重播选项，可以帮助您解决与 web 应用程序，尤其是对于身份验证类型的问题的客户端的问题。

使用所选的 web 调试程序。 在此示例中我们将使用 Fiddler 捕获和分析 http 或 https 流量，请按照说明进行操作：

1. 下载 Fiddler 工具在<https://www.telerik.com/download/fiddler>。

    > [!NOTE]
    > 如果捕获计算机已安装.NET 4，请选择 Fiddler4。 否则，请选择 Fiddler2。

2. 右键单击安装程序可执行文件，并以管理员身份安装运行。

            ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. 当您打开 Fiddler 时，它应自动启动捕获流量 （注意，在较低左下角捕获）。 按 f12 键以启动或停止捕获流量。

        ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 很可能您会对感兴趣解密 HTTPS 流量，并可以通过选择启用 HTTPS 解密**工具** > **Fiddler 选项**，并选中复选框"**解密HTTPS 流量**"。

        ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 您可以删除之前通过单击重现该问题以前不相关的会话**X** （图标） >**全部删除**如遵循屏幕快照： 

        ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 一旦重现此问题，通过选择保存文件以进行检查**文件** > **保存** > **所有会话...**. 

        ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 检查和分析会话日志来确定此问题。

    例如：

- **示例 a:** 找到从客户端，发送请求，它将转到应用程序网关的公共 IP 地址的会话日志，请单击此日志以查看详细信息。  在右侧，在底部中的数据是应用程序网关返回到客户端。 选择"原始"选项卡，并确定客户端正在接收"**设置 Cookie:ARRAffinity=** *ARRAffinityValue*." 如果没有任何 cookie，未设置会话相关性，或应用程序网关不将 cookie 应用到客户端。

   > [!NOTE]
   > 此 ARRAffinity 值是应用程序网关设置客户端发送到特定的后端服务器的 cookie 的 id。

    ![troubleshoot-session-affinity-issues-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **示例 b:** 下一步的会话日志后, 跟上一个之一是客户端响应返回到应用程序网关，后者已设置 ARRAAFFINITY。 如果 ARRAffinity cookie id 与匹配，应将数据包发送到以前使用的同一后端服务器。 检查以查看是否更改客户端的 ARRAffinity cookie 使用 http 通信的下一步多个行。

    ![troubleshoot-session-affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 相同的通信会话，cookie 应不能更改。 检查在右侧顶部的框中，选择"Cookie"选项卡以查看是否在客户端是使用 cookie，并将其发送回应用程序网关。 如果没有，客户端浏览器不是保留和使用 cookie 的会话。 有时，可能会位于客户端。

 

## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。