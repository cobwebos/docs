---
title: VPN 网关：对 VPN 客户端 Azure AD 身份验证进行故障排除
description: 排查 VPN 网关 P2S Azure AD authentication 客户端问题
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151970"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>排查 Azure AD 身份验证 VPN 客户端问题

本文介绍如何使用点到站点 VPN 和 Azure Active Directory 身份验证对 VPN 客户端进行故障排除以连接到虚拟网络。

## <a name="status"></a>查看状态日志

查看错误消息的状态日志。

![日志](./media/troubleshoot-ad-vpn-client/1.png)

1. 单击客户端窗口右下角的 "箭头" 图标以显示**状态日志**。
2. 请检查日志中是否有可能指出问题的错误。
3. 错误消息以红色显示。

## <a name="clear"></a>清除登录信息

清除登录信息。

![登录](./media/troubleshoot-ad-vpn-client/2.png)

1. 选择 。 要进行故障排除的配置文件旁边。 选择 "**配置-> 清除保存的帐户**"。
2. 选择“保存”。
3. 尝试连接。
4. 如果连接仍然失败，请转到下一节。

## <a name="diagnostics"></a>运行诊断

在 VPN 客户端上运行诊断。

![诊断](./media/troubleshoot-ad-vpn-client/3.png)

1. 在“架构”属性中 要在其上运行诊断的配置文件旁边。 选择 "**诊断"-> "运行诊断**"。
2. 客户端将运行一系列测试并显示测试结果

   * Internet 访问–检查客户端是否有 Internet 连接
   * 客户端凭据–查看 Azure Active Directory 身份验证终结点是否可访问
   * 服务器可解析–联系 DNS 服务器，解析已配置的 VPN 服务器的 IP 地址
   * 服务器可访问–检查 VPN 服务器是否响应
3. 如果有任何测试失败，请与网络管理员联系以解决此问题。
4. 下一节将演示如何在需要时收集日志。

## <a name="logfiles"></a>收集客户端日志文件

收集 VPN 客户端日志文件。 可以通过所选的方法将日志文件发送给支持/管理员。 例如，电子邮件。

1. 单击 "..." 要在其上运行诊断的配置文件旁边。 选择 "**诊断-> 显示日志目录**"。

   ![显示日志](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows 资源管理器将打开包含日志文件的文件夹。

   ![查看文件](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[创建用于 P2S 的 Azure Active Directory 租户打开使用 Azure AD 身份验证的 VPN 连接](openvpn-azure-ad-tenant.md)。