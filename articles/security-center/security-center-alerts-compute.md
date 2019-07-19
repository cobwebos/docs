---
title: Azure 安全中心内云本机计算的威胁检测 |Microsoft Docs
description: 本主题介绍 Azure 安全中心提供的云本机计算警报。 在 Azure 安全中心内。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 14433806a28e31cef1a278e16cb69e7c9b1a2458
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295844"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Azure 安全中心内云本机计算的威胁检测

作为云提供商, 安全中心利用了它对内部日志进行分析并确定多个目标的攻击方法的独特可见性。 本主题介绍可用于以下 Azure 服务的警报:

* [Azure 应用服务](#app-services)
* [容器](#azure-containers) 

## Azure App Service<a name="app-services"></a>

安全中心利用云的规模来识别针对 Azure App Service 上运行的客户应用程序的攻击。 利用几乎在任何现代网络中的 web 应用程序, 攻击者都可以探测并利用这些漏洞。 在将路由到特定环境之前, 对在 Azure 中运行的应用程序的请求将通过多个网关进行检查和记录。 然后, 将使用此数据来识别攻击者和攻击者, 并了解稍后将使用的新模式。

通过利用 Azure 作为云提供商的可见性, 安全中心将分析应用服务内部日志, 以确定多个目标的攻击方法。 例如, 广泛的扫描和分布式攻击。 这种类型的攻击通常来自一小部分 Ip, 并展示了对多个主机上类似终结点的爬网模式, 搜索有漏洞的页面或插件。 这可以使用云检测到, 但不能通过单一主机的观点来识别。

安全中心还可以访问基础沙箱和 Vm。 与内存取证一起, 基础结构可以从一种新的攻击传播到客户计算机中的威胁, 从而讲述这一案例。 因此, 安全中心可以检测到攻击者对 web 应用程序的攻击。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**检测到可疑的 WordPress 主题调用**|Azure App Service 活动日志指出了应用服务资源上可能的代码注入活动。<br/> 此可疑活动类似于活动, 它操作 WordPress 主题以支持服务器端代码的执行, 然后是直接 web 请求来调用操作的主题文件。 过去, 这种类型的活动已在 WordPress 上被视为攻击活动的一部分。|
|**检测到从异常 IP 地址连接到网页**|"Azure App Service 活动日志" 表示从以前从未连接到它的源地址到敏感网页的连接。 这可能表示有人正在尝试对你的 Web 应用管理页面进行暴力攻击。 它也可能是合法用户使用的新 IP 地址的结果。|
|**在威胁智能中找到连接到 Azure App Service FTP 接口的 IP**|Azure App Service FTP 日志分析检测到来自威胁情报源中找到的源地址的连接。 在此连接期间, 用户访问了下面列出的页面。|
|**检测到 Web 指纹**|Azure App Service 活动日志指出了应用服务资源上可能存在的 web 指纹活动。 <br/>检测到此可疑活动与名为盲大象的工具相关联。 工具指纹 web 服务器并尝试检测已安装的应用程序及其版本。 攻击者通常使用此工具来探测 web 应用程序以查找漏洞。|
|**检测到可能易受到攻击的网页的可疑访问**|Azure App Service 活动日志指示已访问看似敏感的网页。 <br/>此可疑活动源自其访问模式与 web 扫描程序类似的源地址。 这种活动通常与攻击者尝试扫描网络以尝试访问敏感或易受攻击的网页有关。|
|**上传文件夹中的 PHP 文件**|Azure App Service 活动日志指示某些内容已被访问上传文件夹中的可疑 PHP 页。 <br/>这种类型的文件夹通常不包含 PHP 文件。 存在这种类型的文件可能表示利用了任意文件上传漏洞的利用。|
|**尝试在 Windows 应用服务上运行 Linux 命令**|分析应用服务进程检测到尝试在 Windows 应用服务上运行 Linux 命令。 此操作已被 web 应用程序运行。 此行为在市场活动中经常出现, 利用公共 web 应用程序中的漏洞。|
|**检测到可疑 PHP 执行**|计算机日志指示可疑 PHP 进程正在运行。 操作包含尝试使用 PHP 进程从命令行运行 OS 命令或 PHP 代码。 虽然这种行为是合法的, 但在 web 应用程序中, 这种行为也会在恶意活动中被发现, 如尝试利用 web shell 感染网站。|
|**从临时文件夹执行进程**|应用服务流程分析检测到从应用的临时文件夹执行进程。 尽管此行为很合理, 但在 web 应用程序中, 这种行为也会在恶意活动中被发现。|
|**检测到尝试运行高特权命令**|分析应用服务进程检测到尝试运行需要高权限的命令。 命令在 web 应用程序上下文中运行。 尽管此行为很合理, 但在 web 应用程序中, 这种行为也会在恶意活动中被发现。|

> [!NOTE]
> 应用服务的安全中心威胁检测当前在 Azure 政府版和主权云区域中不可用。

有关应用服务威胁检测警报的详细信息, 请访问使用 Azure 安全中心保护应用服务, 并查看如何对应用服务工作负荷启用监视和保护。

## 存放<a name="azure-containers"></a>

安全中心基于审核框架为 Linux 计算机上的容器提供实时威胁检测。 这些警报标识了几个可疑的 Docker 活动, 例如在主机上创建特权容器、在 Docker 容器中运行安全外壳 (SSH) 服务器的指示, 或者使用了加密挖掘者采用。 可以使用此信息快速补救安全问题，并提高容器的安全性。 除了 Linux 检测外, 安全中心还提供更特定于容器部署的分析。
