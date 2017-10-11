---
title: "Windows 身份验证和 Azure MFA 服务器 |Microsoft 文档"
description: "本 Azure 多重身份验证页面将会帮助你部署 Windows 身份验证和 Azure 多重身份验证服务器。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 19a4043f-c4ce-43c0-80e7-2548ee92cb74
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 7e6384ea8fea686b5cad1a3bc3007252b9cfcd65
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows 身份验证和 Azure 多重身份验证服务器
使用 Azure 多因素身份验证服务器的 Windows 身份验证部分来启用和配置应用程序的 Windows 身份验证。 设置 Windows 身份验证之前，请记住以下列表：

* 设置完成后，重新启动终端服务才会生效 Azure 多因素身份验证。
* 如果选中了“需要 Azure 多重身份验证用户匹配”，而你未在用户列表中，则在重新启动后你将无法登录到计算机。
* 受信任的 Ip 均依赖于是否可以为应用程序提供身份验证的客户端 IP。 支持当前仅终端服务。  

> [!NOTE]
> 为 Windows Server 2012 R2 上的安全终端服务不支持此功能。

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>若要保护具有 Windows 身份验证的应用程序，请使用以下过程。
1. 在 Azure 多因素身份验证服务器中，单击 Windows 身份验证图标。
   ![Windows 身份验证](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. 检查**启用 Windows 身份验证**复选框。 默认情况下，此框未选中。
3. 应用程序选项卡允许管理员配置一个或多个应用程序的 Windows 身份验证。
4. 选择服务器或应用程序-指定是否启用服务器/应用程序。 单击" **确定**"。
5. 单击**添加...**
6. 使用“受信任 IP”选项卡，你可以跳过从特定 IP 发起的 Windows 会话的 Azure 多重身份验证。 例如，如果员工在办公室和家里使用应用程序，你可能会决定不让他们在办公室的电话振铃来进行 Azure 多重身份验证。 为此，将将办公室子网指定为受信任 Ip 条目。
7. 单击**添加...**
8. 选择**单个 IP**如果想要跳过单个 IP 地址。
9. 选择**IP 范围**如果想要跳过整个 IP 范围。 示例 10.63.193.1-10.63.193.100。
10. 选择**子网**如果你想要指定使用子网表示法的 Ip 范围。 输入子网的起始 IP 并选择相应的网络掩码，从下拉列表。
11. 单击" **确定**"。

## <a name="next-steps"></a>后续步骤

- [为 Azure MFA 服务器配置第三方 VPN 设备](multi-factor-authentication-advanced-vpn-configurations.md)

- [适用于 Azure MFA 中增加了 NPS 扩展你现有的身份验证基础结构](multi-factor-authentication-nps-extension.md)
