---
title: Windows 虚拟桌面安全 URL 列表-Azure
description: 你应取消阻止的 Url 的列表，以确保你的 Windows 虚拟桌面部署按预期工作。
author: Heidilohr
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 37fb5ccf121fed6e772dc1cd3dcba2345d62c66f
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067199"
---
# <a name="safe-url-list"></a>安全 URL 列表

需要取消阻止某些 Url，以便 Windows 虚拟桌面部署正常工作。 本文列出了这些 Url，以便您知道哪些 Url 是安全的。

## <a name="virtual-machines"></a>虚拟机

为 Windows 虚拟桌面创建的 Azure 虚拟机必须能够访问以下 URL：

|地址|出站 TCP 端口|目的|服务标记|
|---|---|---|---|
|*.wvd.microsoft.com|443|服务流量|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|代理和 SXS 堆栈更新|AzureCloud|
|*.core.windows.net|443|代理流量|AzureCloud|
|*.servicebus.windows.net|443|代理流量|AzureCloud|
|prod.warmpath.msftcloudes.com|443|代理流量|AzureCloud|
|catalogartifact.azureedge.net|443|Azure 市场|AzureCloud|
|kms.core.windows.net|1688|Windows 激活|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Azure 门户支持|AzureCloud|
| 169.254.169.254 | 80 | [Azure 实例元数据服务终结点](../virtual-machines/windows/instance-metadata-service.md) | 不适用 |

>[!IMPORTANT]
>Windows 虚拟桌面现在支持 FQDN 标记。 有关详细信息，请参阅[使用 Azure 防火墙保护 Windows 虚拟桌面部署](../firewall/protect-windows-virtual-desktop.md)。
>
>建议使用 FQDN 标记或服务标记（而不是 URL）来防止服务问题。 列出的 URL 和标记只对应于 Windows 虚拟桌面站点和资源。 这些 URL 不包含其他服务（如 Azure Active Directory）的 URL。

下表列出了 Azure 虚拟机可以访问的可选 URL：

|地址|出站 TCP 端口|目的|服务标记|
|---|---|---|---|
|*.microsoftonline.com|443|向 Microsoft Online Services 进行身份验证|无|
|*.events.data.microsoft.com|443|遥测服务|无|
|www.msftconnecttest.com|443|检测 OS 是否已连接到 Internet|无|
|*.prod.do.dsp.mp.microsoft.com|443|Windows 更新|无|
|login.windows.net|443|登录到 Microsoft 365 等 Microsoft Online Services|无|
|*.sfx.ms|443|OneDrive 客户端软件更新|无|
|*.digicert.com|443|证书吊销检查|无|

>[!NOTE]
>Windows 虚拟桌面当前没有 IP 地址范围的列表，你可以取消阻止这些 IP 地址范围以允许网络流量。 目前仅支持取消阻止特定的 Url。
>
>有关与安全 Office 相关的 Url （包括所需 Azure Active Directory 相关 Url）的列表，请参阅[Office 365 url 和 IP 地址范围](/office365/enterprise/urls-and-ip-address-ranges)。
>
>对于涉及服务流量的 URL，必须使用通配符 (*)。 如果不希望为代理相关的流量使用 *，下面提供了有关如何在不使用通配符的情况下查找 URL 的信息：
>
>1. 向 Windows 虚拟桌面主机池注册你的虚拟机。
>2. 打开**事件查看器**，然后切换到**Windows 日志**  >  **应用程序**  >  **WVD** ，并查找事件 ID 3701。
>3. 将在事件 ID 3701 下找到的 Url 列入白名单。 事件 ID 3701 下的 Url 是特定于区域的。 需要为要在其中部署虚拟机的每个区域的相关 Url 重复取消阻止进程。

## <a name="remote-desktop-clients"></a>远程桌面客户端

你使用的任何远程桌面客户端都必须具有以下 Url 的访问权限：

|地址|出站 TCP 端口|目的|客户端|
|---|---|---|---|
|*.wvd.microsoft.com|443|服务流量|All|
|*.servicebus.windows.net|443|排查数据问题|All|
|go.microsoft.com|443|Microsoft FWLink|All|
|aka.ms|443|Microsoft URL 缩短符|All|
|docs.microsoft.com|443|文档|All|
|privacy.microsoft.com|443|隐私声明|All|
|query.prod.cms.rt.microsoft.com|443|客户端更新|Windows 桌面|

>[!IMPORTANT]
>若要获取可靠的客户端体验，必须打开这些 URL。 不支持阻止访问这些 URL，否则会影响服务功能。
>
>这些 Url 仅对应于客户端站点和资源。 此列表不包含其他服务（如 Azure Active Directory）的 Url。 在[Office 365 url 和 IP 地址范围](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)的 ID 56 下可以找到 Azure Active Directory url。
