---
title: Azure Stack SQL 资源提供程序 1.1.30.0 发行说明 |Microsoft Docs
description: 了解什么是最新 Azure Stack SQL 资源提供程序更新的包括任何已知的问题以及在何处下载它。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: 7bcec2d17f61345986c1676d13011946a0036666
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164701"
---
# <a name="sql-resource-provider-11300-release-notes"></a>SQL 资源提供程序 1.1.30.0 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍了改进和 SQL 资源提供程序版本 1.1.30.0 中的已知的问题。

## <a name="build-reference"></a>内部版本参考
下载 SQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序有一个相应的 Azure Stack 最低内部版本。 下面列出了安装此版本的 SQL 资源提供程序所需的最低 Azure Stack 发行版本：

> |最低 Azure Stack 版本|SQL 资源提供程序版本|
> |-----|-----|
> |版本 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> 适用于 Azure Stack 集成系统的最低支持的 Azure Stack 更新或部署最新版本的 SQL 资源提供程序之前，先部署最新 Azure Stack 开发工具包 (ASDK)。

## <a name="new-features-and-fixes"></a>新功能和修复
此版本的 Azure Stack SQL 资源提供程序包括以下改进和修补程序：

- **对于 SQL 资源提供程序部署启用遥测**。 对于 SQL 资源提供程序部署已启用收集遥测数据。 收集遥测数据包括资源提供程序部署、 启动和停止时间、 退出状态、 退出消息和错误详细信息 （如果适用）。

- **TLS 1.2 加密更新**。 已启用的 TLS 1.2 仅支持与内部 Azure Stack 组件的资源提供程序通信。 

### <a name="fixes"></a>修复项

- **Azure Stack PowerShell SQL 资源提供程序的兼容性**。 SQL 资源提供程序已更新为与 Azure Stack 2018-03-01-混合 PowerShell 配置文件，并提供与 AzureRM 1.3.0 的兼容性工作及更高版本。

- **SQL 登录名更改密码边栏选项卡**。 修复了问题，不能更改密码边栏选项卡上更改密码。 已移除的链接，从密码更改通知。

- **SQL 宿主服务器设置边栏选项卡中更新**。 修复了在其中设置边栏选项卡已错误地标题为"Password"。

## <a name="known-issues"></a>已知问题 

- **SQL Sku 可能需要长达一小时才能在门户中看到**。 可能要花费一小时，新创建的 Sku，以便创建新的 SQL 数据库时要使用可见。 

    **解决方法**：无。

- **重复使用 SQL 登录名**。 尝试创建新的 SQL 与同一订阅下现有登录名作为相同的用户名的登录名会重复使用相同的登录名和现有的密码。 

    **解决方法**：创建新的登录名在同一订阅下时使用不同的用户名或创建具有相同的用户名下不同的订阅的登录名。

- **共享的 SQL 登录名会导致数据不一致**。 如果在同一订阅下的多个 SQL 数据库共享 SQL 登录名，则更改登录密码将导致数据不一致。

    **解决方法**：始终在同一订阅下的不同数据库的使用不同的登录名。

- **TLS 1.2 支持要求**。 如果你尝试部署或更新 SQL 资源提供程序在未启用 TLS 1.2 的计算机，则操作可能失败。 用于部署或更新资源提供程序以验证 TLS 1.2 返回所支持的计算机上运行以下 PowerShell 命令：

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  如果**Tls12**是命令的输出中不包括，未在计算机上启用 TLS 1.2。

    **解决方法**：运行以下 PowerShell 命令来启用 TLS 1.2，然后启动资源提供程序部署或更新相同的 PowerShell 会话中的脚本：

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>云管理员在操作 Azure Stack 的已知的问题
中的文档，请参阅[Azure Stack 发行说明](azure-stack-servicing-policy.md)。

## <a name="next-steps"></a>后续步骤
[了解有关 SQL 资源提供程序的详细信息](azure-stack-sql-resource-provider.md)。

[准备部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md#prerequisites)。

[从以前的版本升级 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。 