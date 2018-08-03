---
title: Azure Stack 数据中心集成 - 安全性
description: 了解如何将 Azure Stack 安全性与数据中心安全性集成
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 9f356b814ac1ac6ca8b6d6efe7cb9f5d9ed66270
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442469"
---
# <a name="azure-stack-datacenter-integration---security"></a>Azure Stack 数据中心集成 - 安全性
Azure Stack 在设计和构建时已将安全性考虑进去。 Azure Stack 是锁定的系统，因此不支持安装软件安全代理。

本文帮助你将 Azure Stack 的安全功能与数据中心内已部署的安全解决方案相集成。

## <a name="security-logs"></a>安全日志

Azure Stack 每隔两分钟收集一次基础结构角色和缩放单元节点的操作系统和安全事件。 这些日志存储在存储帐户 Blob 容器中。

每个基础结构角色都有一个存储帐户，所有典型的操作系统事件也有一个常规存储帐户。

运行状况资源提供程序可通过 REST 协议调用，将 URL 检索到 Blob 容器。 第三方安全解决方案可以使用 API 和存储帐户来检索事件以供处理。

### <a name="use-azure-storage-explorer-to-view-events"></a>使用 Azure 存储资源管理器查看事件

可以使用名为“Azure 存储资源管理器”的工具来检索 Azure Stack 收集的事件。 可从 [http://storageexplorer.com](http://storageexplorer.com) 下载 Azure 存储资源管理器。

以下过程示例可用于配置 Azure Stack 的 Azure 存储资源管理器：

1. 以操作员身份登录 Azure Stack 管理员门户。
1. 浏览“存储帐户”，并找到 **frphealthaccount**。 **frphealthaccount** 帐户是用于存储所有操作系统事件的常规存储帐户。

   ![存储帐户](media/azure-stack-integrate-security/storage-accounts.png)

1. 选择“frphealthaccount”，单击“访问密钥”。

   ![访问密钥](media/azure-stack-integrate-security/access-keys.png)

1. 将访问密钥复制到剪贴板。
1. 打开 Azure 存储资源管理器。
1. 在“编辑”菜单中，选择“目标 Azure Stack”。
1. 选择“添加帐户”，然后选择“使用存储帐户名和密钥”。

   ![连接存储](media/azure-stack-integrate-security/connect-storage.png)

1. 单击“下一步”。
1. 在“附加外部存储”页上：

   a. 键入帐户名称 **frphealthaccount**。

   b. 粘贴存储帐户访问密钥。

   c. 在“存储终结点域”下选择“其他”，并指定存储终结点的 **[区域].[域名]**。

   d. 选中“使用 HTTP”复选框。

   ![附加外部存储](media/azure-stack-integrate-security/attach-storage.png)

1. 单击“下一步”、查看摘要，然后**完成**向导。
1. 现在可以浏览各个 Blob 容器并下载事件。

   ![浏览 Blob](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>使用编程语言访问事件

可以使用各种编程语言来访问存储帐户。 使用以下文档选择与语言匹配的示例：

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>设备访问审核

Azure Stack 中的所有物理设备都支持使用 TACACS 或 RADIUS。 这包括访问基板管理控制器 (BMC) 和网络交换机。

Azure Stack 解决方案在推出时并未内置 RADIUS 或 TACACS。 但是，经验证，这些解决方案支持使用市面上现有的 RADIUS 或 TACACS 解决方案。

对于 RADIUS，只有 MSCHAPv2 经过了验证。 它代表使用 RADIUS 的最安全实现。
请咨询 OEM 硬件供应商，在 Azure Stack 解决方案包含的设备中启用 TACAS 或 RADIUS。

## <a name="syslog"></a>Syslog

Azure Stack 中的所有物理设备都可以发送 Syslog 消息。 Azure Stack 解决方案未随附 Syslog 服务器。 但是，经验证，这些解决方案支持将消息发送到市面上现有的 Syslog 解决方案。

Syslog 目标地址是针对部署所收集的可选参数，但也可以在部署后添加此参数。

## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)
