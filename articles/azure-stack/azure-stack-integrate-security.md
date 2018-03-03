---
title: "Azure 堆栈数据中心集成的安全"
description: "了解如何将 Azure 堆栈安全集成数据中心安全"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 8ce9045a3e4fd12d61e9b1600ee98880762bc544
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---security"></a>Azure 堆栈数据中心集成的安全
Azure 堆栈的设计和构建考虑到了安全性。 Azure 堆栈是锁定的系统，因此不支持软件安全代理安装。

本文可帮助你将与已在你的数据中心中部署的安全解决方案集成 Azure 堆栈的安全功能。

## <a name="security-logs"></a>安全日志

Azure 堆栈收集操作系统和安全事件的基础结构角色和缩放单元节点每隔两分钟。 日志存储在存储帐户 blob 容器。

有了每个基础结构角色的一个存储帐户和典型的操作系统的所有事件的一个常规的存储帐户。

运行状况资源提供程序可以通过 REST 协议检索到 blob 容器 URL 调用。 第三方安全解决方案可以使用的 API 和存储帐户检索处理的事件。

### <a name="use-azure-storage-explorer-to-view-events"></a>使用 Azure 存储资源管理器来查看事件

你可以检索通过使用一个名为 Azure 存储资源管理器工具的 Azure 堆栈收集的事件。 你可以下载 Azure 存储资源管理器从[http://storageexplorer.com](http://storageexplorer.com)。

以下过程演示了可用于为 Azure 堆栈配置 Azure 存储资源管理器：

1. 作为一个操作员登录到 Azure 堆栈管理员门户。
2. 浏览**存储帐户**并查找**frphealthaccount**。 **Frphealthaccount**帐户是用于存储所有的操作系统事件的常规存储帐户。

   ![存储帐户](media/azure-stack-integrate-security/storage-accounts.png)

3. 选择**frphealthaccount**，然后单击**访问密钥**。

   ![访问密钥](media/azure-stack-integrate-security/access-keys.png)

4. 将访问密钥复制到剪贴板。
5. 打开 Azure 存储资源管理器。
6. 上**编辑**菜单上，选择**目标 Azure 堆栈**。
7. 选择**添加帐户**，然后选择**使用存储帐户名称和密钥**。

   ![连接存储](media/azure-stack-integrate-security/connect-storage.png)

8. 单击“下一步”。
9. 上**附加外部存储**页：

   a. 键入帐户名称**frphealthaccount**。

   b. 粘贴的存储帐户访问密钥。

   c. 下**存储终结点域**，选择**其他**，并指定存储终结点**[Region]。 [DomainName]**。

   d.单击“验证存储凭据”以验证存储帐户。 选择**使用 HTTP**复选框。

   ![附加外部存储](media/azure-stack-integrate-security/attach-storage.png)

10. 单击**下一步**，查看摘要，和**完成**向导。
11. 你现在可以浏览各个 blob 容器和下载事件。

   ![浏览 blob](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>使用访问事件的编程语言

各种编程语言可用于访问存储帐户。 使用以下文档选取与你的语言匹配的一个示例：

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>设备访问审核

Azure 堆栈中的所有物理设备都支持使用 TACACS 或 RADIUS。 这包括访问的基板管理控制器 (BMC) 和网络交换机。

Azure 堆栈解决方案未附带 RADIUS 或 TACACS 生成。 但是，已验证解决方案为在市场中支持使用现有的 RADIUS 或 TACACS 解决方案可用。

仅 radius，MSCHAPv2 进行了验证。 这表示使用 RADIUS 的最安全的实现。
咨询你的 OEM 硬件供应商，以启用 TACAS 或 RADIUS 中包括 Azure 堆栈解决方案的设备。

## <a name="syslog"></a>Syslog

Azure 堆栈中的所有物理设备可以发送 Syslog 消息。 Azure 堆栈解决方案未附带 Syslog 服务器。 但是，已验证解决方案为在市场中支持向现有 Syslog 解决方案可用的发送消息。

Syslog 目标地址是收集有关部署，一个可选参数，但它还可添加后期部署。

## <a name="next-steps"></a>接下来的步骤

[维护策略](azure-stack-servicing-policy.md)
