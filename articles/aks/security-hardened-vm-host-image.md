---
title: AKS 虚拟主机中的安全强化
description: 了解 AKS VM 主机 OS 中的安全强化
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 84b826ce33b5395db5bd38e883b3a0fb3425725b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86244032"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>AKS 代理节点主机 OS 的安全强化

Azure Kubernetes 服务 (AKS) 是符合 SOC、ISO、PCI DSS 和 HIPAA 标准的安全服务。 本文介绍适用于 AKS 虚拟主机的安全强化。 有关 AKS 安全性的详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 中应用程序和群集的安全性概念](./concepts-security.md)。

> [!Note]
> 本文档仅适用于 AKS 中的 Linux 代理。

AKS 群集部署在运行安全优化 OS 的虚拟主机上，该 OS 用于在 AKS 上运行的容器。 此主机 OS 基于 Ubuntu 16.04.LTS 映像，应用了其他安全强化和优化（请参阅“安全强化详细信息”）。

经过安全强化的主机 OS 的目标是减少受攻击面，并以安全的方式优化容器部署。

> [!Important]
> 安全强化 OS 未进行 CIS 基准测试。 虽然存在与 CIS 基准重叠的情况，但此目标不需符合 CIS 标准。 主机操作系统强化的目标是在与 Microsoft 自己的内部主机安全标准一致的安全级别上聚合。

## <a name="security-hardening-features"></a>安全强化功能

* 默认情况下，AKS 提供安全优化主机 OS。 没有用于选择备用操作系统的选项。

* Azure 会将每日修补程序（包括安全修补程序）应用到 AKS 虚拟主机。 这些修补程序中，部分需要重启，部分不需要。 你负责根据需要计划 AKS VM 主机重启。 有关如何自动进行 AKS 修补的指南，请参阅[修补 AKS 节点](./node-updates-kured.md)。

## <a name="what-is-configured"></a>配置内容

| CIS  | 审核说明|
|---|---|
| 1.1.1.1 |确保禁用装载 cramfs 文件系统的功能|
| 1.1.1.2 |确保禁用装载 freevxfs 文件系统的功能|
| 1.1.1.3 |确保禁用装载 jffs2 文件系统的功能|
| 1.1.1.4 |确保禁用装载 HFS 文件系统的功能|
| 1.1.1.5 |确保禁用装载 HFS Plus 文件系统的功能|
|1.4.3 |确保进行单用户模式所需的身份验证 |
|1.7.1.2 |确保正确配置本地登录警告横幅 |
|1.7.1.3 |确保正确配置远程登录警告横幅 |
|1.7.1.5 |确保配置 /etc/issue 上的权限 |
|1.7.1.6 |确保配置 /etc/issue.net 上的权限 |
|2.1.5 |确保 --streaming-connection-idle-timeout 未设置为 0 |
|3.1.2 |确保禁用数据包重定向发送 |
|3.2.1 |确保不接受源路由包 |
|3.2.2 |确保不接受 ICMP 重定向 |
|3.2.3 |确保不接受安全 ICMP 重定向 |
|3.2.4 |确保记录可疑的数据包 |
|3.3.1 |确保不接受 IPv6 路由器播发 |
|3.5.1 |确保禁用 DCCP。 |
|3.5.2 |确保禁用 SCTP。 |
|3.5.3 |确保禁用 RDS。 |
|3.5.4 |确保禁用 TIPC。 |
|4.2.1.2 |确保配置日志记录。 |
|5.1.2 |确保配置 /etc/crontab 上的权限 |
|5.2.4 |确保禁用 SSH X11 转发 |
|5.2.5 |确保将 SSH MaxAuthTries 设置为 4 或更小的数字 |
|5.2.8 |确保禁用 SSH 根登录 |
|5.2.10 |确保禁用 SSH PermitUserEnvironment |
|5.2.11 |确保只使用批准的 MAX 算法 |
|5.2.12 |确保配置 SSH 空闲超时间隔 |
|5.2.13 |确保将 SSH LoginGraceTime 设置为一分钟或更短的时间 |
|5.2.15 |确保配置 SSH 警告横幅 |
|5.3.1 |确保配置密码创建要求 |
|5.4.1.1 |确保密码在 90 天或更短的时间内过期 |
|5.4.1.4 |确保非活动密码在 30 天或更短的时间内锁定 |
|5.4.4 |确保默认的用户 umask 为 027（或更严格的标准） |
|5.6 |确保限制对 su 命令的访问|

## <a name="additional-notes"></a>附加说明
 
* 为了进一步减少受攻击面，我们在 OS 中禁用了一些不必要的内核模块驱动程序。

* 经过安全强化的 OS 是专门为 AKS 构建和维护的，因此，仅在 AKS 平台受支持。

## <a name="next-steps"></a>后续步骤  

有关 AKS 安全性的详细信息，请参阅以下文章： 

[Azure Kubernetes 服务 (AKS)](./intro-kubernetes.md)

[AKS 安全注意事项](./concepts-security.md)

[AKS 最佳做法](./best-practices.md)
