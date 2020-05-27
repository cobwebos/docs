---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649058"
---
应用服务的各个平台组件（包括 Azure VM、存储、网络连接、Web 框架、管理和集成功能）都得到了积极保护和加强。 应用服务持续进行严格的符合性检查，以确保：

- 你的应用资源与其他客户的 Azure 资源隔离开来，从而[受到保护](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)。
- [定期更新 VM 实例和运行时软件](../articles/app-service/overview-patch-os-runtime.md)，以解决新发现的漏洞。 
- 你的应用与其他 Azure 资源（例如 [SQL 数据库](https://azure.microsoft.com/services/sql-database/)）之间的密钥（例如连接字符串）通信一直在 Azure 中进行，不跨越任何网络边界。 存储密钥时始终对其加密。
- 通过应用服务连接功能（例如[混合连接](../articles/app-service/app-service-hybrid-connections.md)）进行的所有通信均已加密。 
- 与 Azure PowerShell、Azure CLI、Azure SDK、REST API 等远程管理工具的连接均已加密。
- 24 小时威胁管理可保护基础结构和平台免受恶意软件、分布式拒绝服务 (DDoS)、中间人 (MITM) 和其他威胁的危害。

有关 Azure 中的基础结构和平台安全性的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/overview/trusted-cloud/)。