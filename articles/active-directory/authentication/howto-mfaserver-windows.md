---
title: Windows 身份验证和 Azure MFA 服务器 | Microsoft 文档
description: 部署 Windows 身份验证和 Azure 多重身份验证服务器。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: b61ecf1019442b29a4cf8120dbdd7ce9c1b4a51a
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160080"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows 身份验证和 Azure 多重身份验证服务器

使用 Azure 多重身份验证服务器的“Windows 身份验证”部分即可为应用程序启用和配置 Windows 身份验证。 设置 Windows 身份验证之前，请注意以下列表：

* 设置以后，重新启动用于终端服务的 Azure 多重身份验证，使之生效。
* 如果选中了“需要 Azure 多重身份验证用户匹配”，而你未在用户列表中，则在重新启动后无法登录到计算机。
* 受信任 IP 依赖于应用程序是否可以为客户端 IP 提供身份验证。 目前只支持终端服务。  

> [!NOTE]
> 不支持使用此功能为 Windows Server 2012 R2 上的终端服务提供安全保障。

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>若要使用 Windows 身份验证保护应用程序，请使用以下过程。
1. 在 Azure 多重身份验证服务器中，单击“Windows 身份验证”图标。
   ![Windows 身份验证](./media/howto-mfaserver-windows/windowsauth.png)
2. 选中“启用 Windows 身份验证”复选框。 默认情况下，此框处于未选中状态。
3. 使用“应用程序”选项卡，管理员可以为 Windows 身份验证配置一个或多个应用程序。
4. 选择服务器或应用程序 - 指定是否启用服务器/应用程序。 单击“确定”。
5. 单击“添加...”
6. 使用“受信任 IP”选项卡，可以跳过从特定 IP 发起的 Windows 会话的 Azure 多重身份验证。 例如，如果员工可以在办公室和家里使用应用程序，你可以决定不让其在办公室进行 Azure 多重身份验证时电话响铃。 为此，可将办公室子网指定为受信任 IP 条目。
7. 单击“添加...”
8. 若要跳过单个 IP 地址，请选择“单个 IP”。
9. 若要跳过整个 IP 范围，请选择“IP 范围”。 示例：10.63.193.1-10.63.193.100。
10. 若要使用子网表示法指定 IP 范围，请选择“子网”。 输入子网的起始 IP，并从下拉列表中选取适当的子网掩码。
11. 单击“确定”。

## <a name="next-steps"></a>后续步骤

- [为 Azure MFA 服务器配置第三方 VPN 设备](howto-mfaserver-nps-vpn.md)

- [通过适用于 Azure MFA 的 NPS 扩展增强现有的身份验证基础结构](howto-mfa-nps-extension.md)
