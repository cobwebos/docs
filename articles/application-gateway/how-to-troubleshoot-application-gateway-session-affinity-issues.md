---
title: 排查 Azure 应用程序网关会话相关性问题
description: 本文介绍如何排查 Azure 应用程序网关中的会话相关性问题
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 07165a497e75934a65719e48a9af7d8d6906ee7b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65538345"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>排查 Azure 应用程序网关会话相关性问题

了解如何诊断和解决 Azure 应用程序网关的会话相关性问题。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

- 运行[Get AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting)在 PowerShell 中
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

若要解决此问题，应使用 FQDN 访问应用程序网关。 例如，使用 [http://website.com](https://website.com/) 或 [http://appgw.website.com](http://appgw.website.com/)。

## <a name="additional-logs-to-troubleshoot"></a>用于故障排除的其他日志

可以收集其他日志并对其进行分析，以排查基于 Cookie 的会话相关性的相关问题

### <a name="analyze-application-gateway-logs"></a>分析应用程序网关日志

若要收集应用程序网关日志，请遵照以下说明操作：

通过 Azure 门户启用日志记录

1. 在 [Azure 门户](https://portal.azure.com/)中找到你的资源，然后单击“诊断日志”。

   对于应用程序网关，提供 3 种日志：访问日志、性能日志、防火墙日志

2. 若要开始收集数据，请单击“启用诊断”。

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. “诊断设置”边栏选项卡提供用于诊断日志的设置。 本示例使用 Log Analytics 存储日志。 单击“Log Analytics”下的“配置”以设置工作区。 也可使用事件中心和存储帐户保存诊断日志。

   ![troubleshoot-session-affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 确认设置，然后单击“保存”。

   ![troubleshoot-session-affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>查看和分析应用程序网关访问日志

1. 在 Azure 门户中的应用程序网关资源视图下，选择“监视”部分中的“诊断日志”。

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 在右侧“日志类别”下的下拉列表中选择“ApplicationGatewayAccessLog”。  

   ![troubleshoot-session-affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. 在“应用程序网关访问日志”列表中，单击要分析和导出的日志，然后导出 JSON 文件。

4. 将步骤 3 中导出的 JSON 文件转换为 CSV 文件，然后在 Excel、Power BI 或其他任何数据可视化工具中查看。

5. 检查以下数据：

- **ClientIP**– 这是从连接的客户端的客户端 IP 地址。
- **ClientPort** - 这是发出请求的连接方客户端的源端口。
- **RequestQuery** – 这表示目标服务器收到的请求。
- **Server-Routed**：收到请求的后端池实例。
- **X-AzureApplicationGateway-LOG-ID**：用于请求的相关 ID。 它可用于排查后端服务器上的流量问题。 例如：X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**：应用程序网关接收从后端的 HTTP 响应代码。

  ![troubleshoot-session-affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

如果看到有两个项来自同一个 ClientIP 和 ClientPort，并且它们发送到同一个后端服务器，则表示已正确配置应用程序网关。

如果看到有两个项来自同一个 ClientIP 和 ClientPort，但它们发送到不同的后端服务器，则表示请求在后端服务器之间弹跳。选择底部的“应用程序使用基于 Cookie 的相关性，但请求仍在后端服务器之间弹跳”可以排查该问题。

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>使用 Web 调试器捕获和分析 HTTP 或 HTTPS 流量

Fiddler 之类的 Web 调试工具可以捕获 Internet 与测试计算机之间的网络流量，并以此帮助你调试 Web 应用程序。 当浏览器接收/发送传入和传出数据时，可以使用此类工具来检测这些数据。 在此示例中，Fiddler 提供 HTTP 重放选项来帮助你排查 Web 应用程序的客户端问题，尤其是身份验证类型相关的问题。

使用所选的 Web 调试器。 此示例将使用 Fiddler 来捕获和分析 http 或 https 流量，具体说明如下：

1. 在 <https://www.telerik.com/download/fiddler> 中下载 Fiddler 工具。

    > [!NOTE]
    > 如果捕获流量的计算机上已安装 .NET 4，请选择 Fiddler4。 否则请选择 Fiddler2。

2. 右键单击安装程序可执行文件，并以管理员的身份安装该程序。

            ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. 打开 Fiddler 时，它应会自动开始捕获流量（注意左下角的“正在捕获”）。 按 F12 键可以启动或停止流量捕获。

        ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 你很有可能对已解密的 HTTPS 流量感兴趣；选择“工具” > “Fiddler 选项”，然后选中“解密HTTPS 流量”框，即可启用 HTTPS 解密。

        ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 在再现问题之前，可以通过单击“X”（图标）>“全部删除”来删除以前的不相关会话，如以下屏幕截图所示： 

        ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 再现问题后，选择“文件” > “保存” > “所有会话...”以保存该文件供复查。 

        ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 检查并分析会话日志以确定问题所在。

    例如：

- **示例 A：** 在会话日志中，你发现请求是从客户端发出的，该请求发送到了应用程序网关的公共 IP 地址。于是你单击此日志以查看详细信息。  右侧下框中的数据是应用程序网关返回给客户端的数据。 选择“RAW”选项卡，并确定客户端是否正在接收“**Set-Cookie:ARRAffinity=** *ARRAffinityValue*”。 如果未看到任何 Cookie，则表示未设置会话相关性，或者应用程序网关未将 Cookie 应用回到客户端。

   > [!NOTE]
   > 此 ARRAffinity 值是应用程序网关为客户端设置的 Cookie ID，它将发送到特定的后端服务器。

    ![troubleshoot-session-affinity-issues-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **示例 B：** 前一条会话日志后面的会话日志是客户端返回给应用程序网关的响应，其中设置了 ARRAAFFINITY。 如果 ARRAffinity Cookie ID 匹配，则数据包应会发送到前面使用的同一个后端服务器。 检查 http 通信的后面几行，以查看客户端的 ARRAffinity Cookie 是否在发生变化。

    ![troubleshoot-session-affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 对于同一个通信会话，Cookie 不应该变化。 检查右侧的上框，选择“Cookie”选项卡以查看客户端是否正在使用 Cookie 并将其发回到应用程序网关。 如果不是，则表示客户端浏览器未保留 Cookie 并将其用于对话。 有时，客户端可能提供不实的数据。

 

## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。
