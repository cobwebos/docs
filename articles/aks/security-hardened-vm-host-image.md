---
title: AKS 虚拟主机中的安全性强化
description: 了解 AKS VM 主机操作系统中的安全性强化
services: container-service
author: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 5c2ecd5a53bb77193469ba1135c46d9b5fa65b2c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202974"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>AKS 虚拟主机中的安全性强化 

Azure Kubernetes Service （AKS）是一种安全服务，符合 SOC、ISO、PCI DSS 和 HIPAA 标准。 本文介绍适用于 AKS 虚拟主机的安全性强化。 有关 AKS 安全性的详细信息，请参阅[Azure Kubernetes Service （AKS）中的应用程序和群集的安全概念](https://docs.microsoft.com/azure/aks/concepts-security)。

AKS 群集部署在运行安全优化操作系统的主机虚拟机上。 此主机 OS 目前基于 Ubuntu 16.04. LTS 映像，其中应用了一组附加的安全强化步骤（请参阅安全强化详细信息）。   

安全强化主机操作系统的目标是减少攻击面，并允许以安全的方式部署容器。 

> [!Important]
> 安全强化的操作系统不是 CIS 基准。 尽管与 CIS 基准重叠，但目标并不符合 CIS 标准。 主机操作系统强化的目标是在与 Microsoft 自己的内部主机安全标准一致的安全级别上聚合。 

## <a name="security-hardening-features"></a>安全强化功能 

* 默认情况下，AKS 提供安全优化的主机操作系统。 当前没有选择备用操作系统的选项。 

* Azure 会将每日修补程序（包括安全修补程序）应用于 AKS 虚拟主机。 其中一些修补程序需要重新启动，而其他修补程序则不需要重新启动。 你负责根据需要计划 AKS VM 主机重启。 有关如何自动执行 AKS 修补的指导，请参阅[修补 AKS 节点](https://docs.microsoft.com/en-us/azure/aks/node-updates-kured)。

下面是在 AKS 中实现的图像强化工作的摘要，用于生成安全优化的主机操作系统。 该工作已[在此 GitHub 项目中](https://github.com/Azure/aks-engine/projects/7)实现。  

AKS-Engine 目前不会升级或遵守任何特定的安全标准，但在适用的情况下，将提供 CIS （适用于 Internet 安全的中心）审核 Id。 

## <a name="whats-configured"></a>配置内容

| CI  | 审核说明| 
|---|---|
| 1.1.1.1 |确保已禁用 cramfs 文件系统的装载|
| 1.1.1.2 |确保已禁用 freevxfs 文件系统的装载|
| 1.1.1.3 |确保已禁用 jffs2 文件系统的装载|
| 1.1.1.4 |确保已禁用 HFS 文件系统的装载|
| 1.1.1.5 |确保已禁用 HFS 和文件系统的装载|
|1.4.3 |确保单用户模式需要进行身份验证 |
|1.7.1.2 |确保正确配置了本地登录警告横幅 |
|1.7.1.3 |确保正确配置远程登录警告横幅 |
|1.7.1.5 |确保配置对/etc/issue 的权限 |
|1.7.1.6 |确保配置对/etc/issue.net 的权限 |
|2.1.5 |确保--流式连接-空闲超时未设置为0 |
|3.1.2 |确保禁用数据包重定向发送 |
|3.2.1 |确保不接受源路由包 |
|3.2.2 |确保不接受 ICMP 重定向 |
|3.2.3 |确保不接受安全 ICMP 重定向 |
|3.2.4 |确保记录可疑数据包 |
|3.3.1 |确保不接受 IPv6 路由器播发 |
|3.5.1 |确保禁用 DCCP |
|3.5.2 |确保已禁用 SCTP |
|3.5.3 |确保已禁用 RDS |
|3.5.4 |确保禁用 TIPC |
|4.2.1.2 |确保配置日志记录 |
|5.1.2 |确保配置对/etc/crontab 的权限 |
|5.2.4 |确保已禁用 SSH X11 转发 |
|5.2.5 |确保 SSH MaxAuthTries 设置为4或更小 |
|5.2.8 |确保已禁用 SSH 根登录 |
|5.2.10 |确保已禁用 SSH PermitUserEnvironment |
|5.2.11 |确保仅使用已批准的最大算法 |
|5.2.12 |确保配置了 SSH 空闲超时间隔 |
|5.2.13 |确保 SSH LoginGraceTime 设置为一分钟或更短 |
|5.2.15 |确保配置了 SSH 警告横幅 |
|5.3.1 |确保配置了密码创建要求 |
|5.4.1.1 |确保密码过期为90天或更短 |
|5.4.1.4 |确保非活动密码锁定为30天或更短时间 |
|5.4.4 |确保默认用户 umask 是027或更严格的 |
|5.6 |确保对 su 命令的访问受到限制|

## <a name="additional-notes"></a>附加说明
 
* 为了进一步减小攻击面，一些不必要的内核模块驱动程序已在操作系统中禁用。 

* 不支持在 AKS 平台之外进行安全强化的操作系统。 

## <a name="next-steps"></a>后续步骤  

有关 AKS 安全性的详细信息，请参阅以下文章： 

[Azure Kubernetes 服务 (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS 安全注意事项](https://docs.microsoft.com/azure/aks/concepts-security)

[AKS 最佳实践](https://docs.microsoft.com/azure/aks/best-practices)
