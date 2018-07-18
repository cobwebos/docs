---
title: 适用于 Azure Marketplace 映像的安全建议 | Microsoft Docs
description: 本文将提供适用于市场映像的安全建议
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2017
ms.locfileid: "23465402"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>适用于 Azure Marketplace 映像的安全建议

我们建议每个解决方案都遵守以下安全配置建议。 这些建议不仅有助于为 Azure Marketplace 中的合作伙伴解决方案映像保持高级别的安全性。

而且对于在 Azure Marketplace 中没有映像的组织也很有帮助。 你可能希望根据下表中的准则检查公司的 Windows 和 Linux 映像配置。

## <a name="open-source-based-images"></a>打开基于源的映像

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **类别**                                                 | **检查**                                                                                                                                                                                                                                                                              |
| 安全                                                     | 所有 Linux 分发版的最新安全修补程序均已安装。                                                                                                                                                                                                              |
| 安全                                                     | 确保特定的 Linux 发行版 VM 映像已遵循行业准则。                                                                                                                                                                                     |
| 安全                                                     | 限制攻击面，仅保留必要的 Windows Server 角色、功能、服务和网络端口来保持最小的占用空间。                                                                                                                                               |
| 安全                                                     | 扫描源代码和生成的 VM 映像中的恶意软件。                                                                                                                                                                                                                                   |
| 安全                                                     | VHD 映像仅包括必要的锁定帐户，不含允许交互式登录的默认密码以及“后门”。                                                                                                                                           |
| 安全                                                     | 禁用防火墙规则，除非受应用程序的功能依赖于这些规则，例如防火墙设备。                                                                                                                                                                             |
| 安全                                                     | VHD 映像中的所有敏感信息均已删除，例如测试 SSH 密钥、已知主机文件、日志文件和不必要的证书。                                                                                                                                       |
| 安全                                                     | 不推荐使用 LVM。                                                                                                                                                                                                                                            |
| 安全                                                     | 所需库的最新版本应包括： </br> - OpenSSL v1.0 或更高版本 </br> - Python 2.5 或更高版本（强烈建议使用 Python 2.6+） </br> - Python pyasn1 包（如果尚未安装） </br> - d.OpenSSL v 1.0 或更高版本                                                                |
| 安全                                                     | 必须清除 Bash/Shell 历史记录                                                                                                                                                                                                                                             |
| 联网                                                   | 默认情况下，应包括 SSH 服务器。 请通过以下选项为 sshd 配置设置 SSH 保持连接时间：ClientAliveInterval 180                                                                                                                                                        |
| 联网                                                   | 映像不应包含任何自定义网络配置。 删除 resolv.conf：`rm /etc/resolv.conf`                                                                                                                                                                                |
| 部署                                                   | 应安装最新的 Azure Linux 代理 </br> - 应使用 RPM 或 Deb 包安装代理。  </br> - 也可使用手动安装进程，但建议首选安装包。 </br> - 如果从 github 存储库手动安装代理，首先请将 `waagent` 文件复制到 `/usr/sbin` 中并（作为根）运行： </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>代理配置文件将放置在 `/etc/waagent.conf` 中。    |
| 部署                                                   | 确保 Azure 支持可在需要时为合作伙伴提供串行控制台输出，并为从云存储装载的 OS 磁盘提供足够的超时时间。 映像必须将以下参数添加到内核引导行：`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| 部署                                                   | OS 磁盘上无需交换分区。 可通过 Linux 代理在本地资源磁盘上请求创建交换。         |
| 部署                                                   | 建议为 OS 磁盘创建单个根分区。      |
| 部署                                                   | 仅支持 64 位 操作系统。                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>基于 Windows Server 的映像

|||
|-------------| -------------------------|
| **类别**                                                     | **检查**                                                                                                                                                                |
| 安全                                                         | 使用安全 OS 的基础映像。 用于任何基于 Windows Server 的映像源的 VHD 必须来自 Microsoft Azure 所提供的 Windows Server OS 映像。 |
| 安全                                                         | 安装所有最新的安全更新。                                                                                                                                     |
| 安全                                                         | 应用程序不应依赖于受限的用户名，例如 Administrator、根和管理员。                                                                |
| 安全                                                         | 操作系统硬盘上不支持 BitLocker 驱动器加密。 BitLocker 可在数据磁盘上使用。                                                            |
| 安全                                                         | 限制攻击面，仅保留必要的 Windows Server 角色、功能、服务和网络端口来保持最小的占用空间。                         |
| 安全                                                         | 扫描源代码和生成的 VM 映像中的恶意软件。                                                                                                                     |
| 安全                                                         | 将 Windows Server 映像安全更新设置为自动更新。                                                                                                                |
| 安全                                                         | VHD 映像仅包括必要的锁定帐户，不含允许交互式登录的默认密码以及“后门”。                             |
| 安全                                                         | 禁用防火墙规则，除非受应用程序的功能依赖于这些规则，例如防火墙设备。                                                               |
| 安全                                                         | 所有敏感信息已从 VHD 映像中删除。 例如，应删除主机文件、日志文件和不必要的证书。                                              |
| 部署                                                       | 仅支持 64 位 操作系统。                            |
