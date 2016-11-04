---
title: 使用 RADIUS 的远程桌面网关和 Azure Multi-Factor Authentication 服务器
description: 本 Azure Multi-Factor Authentication 页面将会帮助你部署使用 RADIUS 的远程桌面 (RD) 网关和 Azure Multi-Factor Authentication 服务器。
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban

---
# 使用 RADIUS 的远程桌面网关和 Azure Multi-Factor Authentication 服务器
在许多情况下，远程桌面网关使用本地 NPS 对用户进行身份验证。本文档介绍如何将来自远程桌面网关（通过本地 NPS）的 RADIUS 请求路由到 Multi-Factor Authentication 服务器。

Multi-Factor Authentication 服务器应安装在独立的服务器上，它随后会将 RADIUS 请求通过代理路由回远程桌面网关服务器上的 NPS。NPS 验证用户名和密码后，将向Multi-Factor Authentication 服务器返回响应，Multi-Factor Authentication 服务器执行第二重身份验证后，将结果返回到网关。

## 配置 RD 网关
必须将 RD 网关配置为将 RADIUS 身份验证发送到 Azure Multi-Factor Authentication 服务器。在 RD 网关安装、配置完毕并正常工作后，转到 RD 网关属性。转到“RD CAP 存储”选项卡，将其更改为使用运行 NPS 的中央服务器，而不是使用运行 NPS 的本地服务器。添加一个或多个 Azure Multi-Factor Authentication 服务器作为 RADIUS 服务器，并为每个服务器指定共享机密。

## 配置 NPS
RD 网关使用 NPS 将 RADIUS 请求发送到 Azure Multi-Factor Authentication。必须更改超时以防止在完成Multi-Factor Authentication之前 RD 网关超时。使用以下过程可配置 NPS。

1. 在 NPS 中，展开左侧列中的“RADIUS 客户端和服务器”菜单，单击“远程 RADIUS 服务器组”。进入 TS 网关服务器组的属性。编辑显示的 RADIUS 服务器，然后转到“负载平衡”选项卡。将“在这些秒数后没有反应，就认为请求被放弃”和“当服务器被识别为不可用时，请求之间的间隔秒数”更改为 30-60 秒。单击“身份验证/帐户”选项卡，确保指定的 RADIUS 端口与 Multi-Factor Authentication 服务器将侦听的端口匹配。
2. 还必须将 NPS 配置为从 Azure Multi-Factor Authentication 服务器接收返回的 RADIUS 身份验证。单击左侧菜单中的“RADIUS 客户端”。将 Azure Multi-Factor Authentication 服务器添加为 RADIUS 客户端。选择友好名称，并指定共享机密。
3. 展开左侧导航区域中的“策略”部分，然后单击“连接请求策略”。它应包含配置 RD 网关时创建的名为“TS 网关授权策略”的连接请求策略。此策略将 RADIUS 请求转发到Multi-Factor Authentication 服务器。
4. 复制此策略以创建一个新策略。在新策略中添加一个条件，使客户端友好名称与上面的步骤 2 中为 Azure Multi-Factor Authentication 服务器 RADIUS 客户端设置的友好名称匹配。将“身份验证提供程序”更改为“本地计算机”。此策略可确保从 Azure Multi-Factor Authentication 服务器接收 RADIUS 请求时，身份验证在本地进行，而不是将 RADIUS 请求发送回 Azure Multi-Factor Authentication 服务器，后者会导致循环情况。为了防止循环情况，必须将此新策略的顺序排列在转发到 Multi-Factor Authentication 服务器的原始策略之前。

## 配置 Azure Multi-Factor Authentication
- - -
将 Azure Multi-Factor Authentication 服务器配置为 RD 网关与 NPS 之间的 RADIUS 代理。应将 Azure Multi-Factor Authentication 服务器安装在不同于 RD 网关服务器的已加入域的服务器上。使用以下过程可配置 Azure Multi-Factor Authentication 服务器。

1. 打开 Azure Multi-Factor Authentication 服务器，并单击“RADIUS 身份验证”图标。选中“启用 RADIUS 身份验证”复选框。
2. 在“客户端”选项卡上，确保端口与 NPS 中配置的内容匹配，然后单击“添加...”按钮。添加 RD 网关服务器 IP 地址、应用程序名称（可选）和共享机密。Azure Multi-Factor Authentication 服务器和 RD 网关上的共享机密将需要相同。
3. 单击“目标”选项卡，然后选择“RADIUS 服务器”单选按钮。
4. 单击“添加...”按钮。输入 NPS 服务器的 IP 地址、共享机密和端口。除非使用中央 NPS，否则 RADIUS 客户端和 RADIUS 目标将是相同的。共享机密必须与 NPS 服务器的“RADIUS 客户端”部分中设置的共享机密相匹配。

![Radius 身份验证](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

<!---HONumber=AcomDC_0921_2016-->