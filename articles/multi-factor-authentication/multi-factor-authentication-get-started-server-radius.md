---
title: "RADIUS 身份验证和 Azure MFA 服务器 | Microsoft Docs"
description: "本 Azure 多重身份验证页面会帮助你部署 RADIUS 身份验证和 Azure 多重身份验证服务器。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: kgremban
ms.reviewer: 
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 4dfa56ba6f80193e643965b97b6439c62f7873e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>将 RADIUS 身份验证与 Azure 多重身份验证服务器集成

RADIUS 是一种标准协议，用于接受身份验证请求并处理这些请求。 Azure 多重身份验证服务器可充当 RADIUS 服务器。 将其插入 RADIUS 客户端（VPN 设备）和身份验证目标之间，以便添加双重验证。 身份验证目标可以是 Active Directory、LDAP 目录或其他 RADIUS 服务器。 要使 Azure 多重身份验证 (MFA) 起作用，必须配置 Azure MFA 服务器，使其可以同时与户端服务器和身份验证目标进行通信。 Azure MFA 服务器将接受来自 RADIUS 客户端的请求，针对身份验证目标验证凭据，添加 Azure 多重身份验证，并将响应发送回 RADIUS 客户端。 只有主要身份验证和 Azure 多重身份验证都成功，身份验证才成功。

> [!NOTE]
> MFA 服务器仅支持 PAP（密码身份验证协议），充当 RADIUS 服务器时，还支持 MSCHAPv2（Microsoft 的质询握手身份验证协议）RADIUS 协议。  当 MFA 服务器充当其他 RADIUS 服务器的 RADIUS 代理时，可使用 EAP（可扩展身份验证协议）等协议，但前提是该 RADIUS 服务器支持该协议。
>
> 在此配置中，单向 SMS 和 OATH 令牌将不起作用，因为 MFA 服务器无法使用替代协议发起成功的 RADIUS 质询响应。

![Radius 身份验证](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="add-a-radius-client"></a>添加 RADIUS 客户端
若要配置 RADIUS 身份验证，请在 Windows Server 上安装 Azure 多重身份验证服务器。 如果有 Active Directory 环境，应将服务器加入到网络内部的域中。 使用以下过程可配置 Azure 多重身份验证服务器：

1. 在 Azure 多重身份验证服务器内，单击左侧菜单中的“RADIUS 身份验证”图标。
2. 选中“启用 RADIUS 身份验证”复选框。
3. 在客户端选项卡上，如果 Azure MFA RADIUS 服务需要侦听非标准端口上的 RADIUS 请求，则更改身份验证端口和计帐端口。
4. 单击“添加”。
5. 输入将向 Azure 多重身份验证服务器进行身份验证的设备/服务器的 IP 地址、应用程序名称（可选）和共享机密。

  应用程序名称将出现在报告中，并可能会显示在短信或移动应用身份验证消息中。

  Azure 多重身份验证服务器和设备/服务器上的共享机密必须相同。

6. 如果所有用户均已导入到该服务器并接受多重身份验证，请选中“需要多重身份验证用户匹配”框。 如果大量用户尚未导入到该服务器或者将免除进行双重验证，请使该框处于未选中状态。
7. 如果想使用移动身份验证应用中的 OATH 密码作为备份方法，请选中“启用回退 OATH 令牌”框。
8. 单击 **“确定”**。

重复执行步骤 4 到步骤 8，添加所需的其他 RADIUS 客户端。

## <a name="configure-your-radius-client"></a>配置 RADIUS 客户端

1. 单击“目标”选项卡。
2. 如果 Azure MFA 服务器已安装在 Active Directory 环境中已加入域的服务器上，请选择 Windows 域。
3. 如果应针对 LDAP 目录对用户进行身份验证，请选择“LDAP 绑定”。

  请在“设置”选项卡上选择“目录集成”图标并编辑 LDAP 配置，以便将服务器绑定到目录。 可在 [LDAP 代理配置指南](multi-factor-authentication-get-started-server-ldap.md)中找到配置 LDAP 的说明。

4. 如果用户应向其他 RADIUS 服务器进行身份验证，请选择“RADIUS 服务器”。
5. 单击“添加”，配置 Azure MFA 服务器将为其代理 RADIUS 请求的服务器。
6. 在“添加 RADIUS 服务器”对话框中，输入 RADIUS 服务器的 IP 地址和共享机密。

  Azure多重身份验证服务器和 RADIUS 服务器上的共享机密必须相同。 如果 RADIUS 服务器使用不同端口，请更改身份验证端口和记帐端口。

7. 单击 **“确定”**。
8. 在其他 RADIUS 服务器中将 Azure MFA 服务器添加为 RADIUS 客户端，以便该 RADIUS 服务器处理 Azure MFA 服务器发送给它的访问请求。 使用 Azure 多重身份验证服务器中配置的共享机密。

重复上述步骤，添加更多 RADIUS 服务器。 使用“上移”和“下移”按钮配置 Azure MFA 服务器调用这些 RADIUS 服务器时应使用的顺序。

已成功配置 Azure 多重身份验证服务器。 该服务器正在已配置端口上侦听来自已配置客户端的 RADIUS 访问请求。   

## <a name="radius-client-configuration"></a>RADIUS 客户端配置
若要配置 RADIUS 客户端，请遵循以下准则：

* 将设备/服务器配置为通过 RADIUS 向 Azure 多重身份验证服务器的 IP 地址进行身份验证，后者充当 RADIUS 服务器。
* 使用之前配置的共享机密。
* 将 RADIUS 超时配置为 30-60 秒，以便有时间验证用户的凭据、执行双重验证、接收其响应，然后对 RADIUS 访问请求做出响应。

## <a name="next-steps"></a>后续步骤

了解在云中有 Azure 多重身份验证的情况下，如何[集成 RADIUS 身份验证](multi-factor-authentication-nps-extension.md)。 