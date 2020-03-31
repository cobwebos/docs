---
title: 适用于 Azure 市场映像的安全建议 | Microsoft Docs
description: 本文将提供适用于市场映像的安全建议
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: b82cf957f4bd74cb2c63bfd5a7fe73899b395df6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795815"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>适用于 Azure 市场映像的安全建议

映像必须满足这些安全配置建议。 这些建议不仅有助于为 Azure 市场中的合作伙伴解决方案映像保持高级别的安全性。

在提交之前，始终在映像上运行安全漏洞检测。 如果在自己的已发布映像中检测到安全漏洞，则必须及时通知客户该漏洞以及如何更正该漏洞。

## <a name="open-source-based-images"></a>打开基于源的映像

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **类别**                                                 | **检查**                                                                                                                                                                                                                                                                              |
| 安全性                                                     | 安装 Linux 发行版的所有最新安全修补程序。                                                                                                                                                                                                              |
| 安全性                                                     | 遵循行业准则，为特定的 Linux 发行版保护 VM 映像。                                                                                                                                                                                     |
| 安全性                                                     | 限制攻击面，仅保留必要的 Windows Server 角色、功能、服务和网络端口来保持最小的占用空间。                                                                                                                                               |
| 安全性                                                     | 扫描源代码和生成的 VM 映像中的恶意软件。                                                                                                                                                                                                                                   |
| 安全性                                                     | VHD 映像仅包括必要的锁定帐户，这些帐户没有允许交互式登录的默认密码;没有后门                                                                                                                                           |
| 安全性                                                     | 禁用防火墙规则，除非应用程序在功能上依赖于这些规则，例如防火墙设备。                                                                                                                                                                             |
| 安全性                                                     | 从 VHD 映像中删除所有敏感信息，例如测试 SSH 密钥、已知主机文件、日志文件和不必要的证书。                                                                                                                                       |
| 安全性                                                     | 避免使用 LVM。                                                                                                                                                                                                                                            |
| 安全性                                                     | 包括所需库的最新版本： </br> - OpenSSL v1.0 或更高版本 </br> - Python 2.5 或更高版本（强烈建议使用 Python 2.6+） </br> - Python pyasn1 包（如果尚未安装） </br> - d.OpenSSL v 1.0 或更高版本                                                                |
| 安全性                                                     | 清除 Bash/Shell 历史记录条目。                                                                                                                                                                                                                                             |
| 网络                                                   | 默认情况下包括 SSH 服务器。 将 SSH 保持活动状态设置为 sshd 配置，并具有以下选项：客户端 AliveInterval 180。                                                                                                                                                        |
| 网络                                                   | 从映像中删除任何自定义网络配置。 删除 resolv.conf： `rm /etc/resolv.conf`.                                                                                                                                                                                |
| 部署                                                   | 安装最新的 Azure Linux 代理。</br> - 使用 RPM 或 Deb 软件包进行安装。  </br> - 也可使用手动安装进程，但建议首选安装包。 </br> - 如果要从 GitHub 存储库手动安装代理，首先请将 `waagent` 文件复制到 `/usr/sbin` 中并（以 root 身份）运行： </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>代理配置文件放置在 `/etc/waagent.conf` 中。 |
| 部署                                                   | 确保 Azure 支持可以在需要时为我们的合作伙伴提供串行控制台输出，并为从云存储安装 OS 磁盘提供足够的超时。 将以下参数添加到映像内核引导行： `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`。 |
| 部署                                                   | OS 磁盘上无需交换分区。 可通过 Linux 代理在本地资源磁盘上请求创建交换。         |
| 部署                                                   | 为 OS 磁盘创建单个根分区。      |
| 部署                                                   | 仅支持 64 位 操作系统。                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>基于 Windows Server 的映像

|||
|-------------| -------------------------|
| **类别**                                                     | **检查**                                                                                                                                                                |
| 安全性                                                         | 使用安全 OS 的基础映像。 用于任何基于 Windows Server 的映像源的 VHD 必须来自 Microsoft Azure 所提供的 Windows Server OS 映像。 |
| 安全性                                                         | 安装所有最新的安全更新。                                                                                                                                     |
| 安全性                                                         | 应用程序不应依赖于受限的用户名，如管理员、根或管理员。                                                                |
| 安全性                                                         | 为操作系统硬盘和数据硬盘启用 BitLocker 驱动器加密。                                                             |
| 安全性                                                         | 限制攻击面，仅保留必要的 Windows Server 角色、功能、服务和网络端口来保持最小的占用空间。                         |
| 安全性                                                         | 扫描源代码和生成的 VM 映像中的恶意软件。                                                                                                                     |
| 安全性                                                         | 将 Windows Server 映像安全更新设置为自动更新。                                                                                                                |
| 安全性                                                         | VHD 映像仅包括必要的锁定帐户，这些帐户没有允许交互式登录的默认密码;没有后门                             |
| 安全性                                                         | 禁用防火墙规则，除非应用程序在功能上依赖于这些规则，例如防火墙设备。                                                               |
| 安全性                                                         | 从 VHD 映像中删除所有敏感信息，包括 HOSTS 文件、日志文件和不必要的证书。                                              |
| 部署                                                       | 仅支持 64 位 操作系统。                            |

即使您的组织在 Azure 市场中没有映像，请考虑根据这些建议检查 Windows 和 Linux 映像配置。

## <a name="contacting-customers"></a>联系客户

要识别客户及其联系人电子邮件：

1.  在"云合作伙伴门户"中，在左侧轨上，选择 **"见解**"。
2.  在"**订单和使用情况**"选项卡上，使用 **"开始日期**"和 **"结束日期"** 字段在所需的日期范围内查询使用情况。 这显示了每天用于产品/服务中的 Azure 订阅。 导出此数据。 
3.  同样，在 **"客户"** 选项卡上，查询和导出客户群。
4.  将步骤 2 中的订阅 ID 与步骤 3 中的订阅 ID 匹配，以查找必要的客户信息。
