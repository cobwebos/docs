---
title: VPN 网关：排查 VPN 客户端问题 - Azure AD 身份验证
description: 排查 VPN 网关 P2S Azure AD 身份验证客户端的问题
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 56a8514fc2531ba0b18925427814e5bfef7d64bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84988103"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>排查 Azure AD 身份验证 VPN 客户端问题

本文帮助你排查 VPN 客户端在使用点到站点 VPN 和 Azure Active Directory 身份验证连接到虚拟网络时出现的问题。

## <a name="view-status-log"></a><a name="status"></a>查看状态日志

查看状态日志中的错误消息。

![日志](./media/troubleshoot-ad-vpn-client/1.png)

1. 单击客户端窗口右下角的箭头图标以显示“状态日志”。 
2. 在日志中检查可能指示出现了问题的错误。
3. 错误消息以红色字体显示。

## <a name="clear-sign-in-information"></a><a name="clear"></a>清除登录信息

清除登录信息。

![登录](./media/troubleshoot-ad-vpn-client/2.png)

1. 选择 要排查问题的配置文件旁边的“...”。 选择“配置”->“清除保存的帐户”。 
2. 选择“保存”  。
3. 尝试连接。
4. 如果连接仍然失败，请转到下一部分。

## <a name="run-diagnostics"></a><a name="diagnostics"></a>运行诊断

在 VPN 客户端上运行诊断。

![诊断](./media/troubleshoot-ad-vpn-client/3.png)

1. 在“架构”属性中  要对其运行诊断的配置文件旁边的“...”。 选择“诊断”->“运行诊断”。 
2. 客户端将运行一系列测试并显示测试结果

   * Internet 访问 – 检查客户端是否已建立 Internet 连接
   * 客户端凭据 – 检查是否可访问 Azure Active Directory 身份验证终结点
   * 服务器可解析 – 连接 DNS 服务器，解析配置的 VPN 服务器的 IP 地址
   * 服务器可访问 – 检查 VPN 服务器是否做出响应
3. 如果上述任何测试失败，请联系网络管理员来解决问题。
4. 下一部分介绍如何按需收集日志。

## <a name="collect-client-log-files"></a><a name="logfiles"></a>收集客户端日志文件

收集 VPN 客户端日志文件。 可以通过所选的方法将日志文件发送给支持人员/管理员。 例如，通过电子邮件发送。

1. 单击 要对其运行诊断的配置文件旁边的“...”。 选择“诊断”->“显示日志目录”。 

   ![显示日志](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows 资源管理器中会打开包含日志文件的文件夹。

   ![查看文件](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[为使用 Azure AD 身份验证的 P2S 开放 VPN 连接创建 Azure Active Directory 租户](openvpn-azure-ad-tenant.md)。