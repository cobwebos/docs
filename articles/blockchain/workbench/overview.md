---
title: Azure Blockchain Workbench 预览版概述
description: Azure Blockchain Workbench 预览版及其功能的概述。
ms.date: 09/05/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: 88c162539e5d0480d7f938c674b96c2f396774e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75387065"
---
# <a name="what-is-azure-blockchain-workbench"></a>什么是 Azure Blockchain Workbench？

Azure Blockchain Workbench 预览版是 Azure 服务和功能的集合，旨在帮助创建和部署区块链应用程序，以便与其他组织共享业务流程和数据。 Azure Blockchain Workbench 可提供用于创建区块链应用程序的基础结构基架，使开发者能够专注于创建业务逻辑和智能协定。 此外，借助 Blockchain Workbench，通过集成多种 Azure 服务和功能，帮助自动执行常见开发任务，可更轻松地创建区块链应用程序。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>创建区块链应用程序

有了 Blockchain Workbench，即可通过配置和编写智能协定代码来定义区块链应用程序。 可快速启动区块链应用程序开发工作，专注于定义协定和编写业务逻辑，无需生成基架，设置支持服务。

## <a name="manage-applications-and-users"></a>管理应用程序和用户

Azure Blockchain Workbench 提供了用于管理区块链应用程序和用户的 Web 应用程序和 REST API。 Blockchain Workbench 管理员可管理应用程序的访问权限，并为用户分配应用程序角色。 Azure AD 用户会自动映射到应用程序中的成员。

## <a name="integrate-blockchain-with-applications"></a>将区块链与应用程序集成

可使用 Blockchain Workbench REST API 和基于消息的 API 与现有系统集成。 通过这些 API 提供的接口，可替换或使用多个分布式账本技术、存储和数据库产品/服务。

Blockchain Workbench 可转换发送到其基于消息的 API 的消息，以便按照该区块链本机 API 预期的格式生成事务。  Workbench 可以对事务签名并将其路由到相应区块链。 

Workbench 自动将事件发送到服务总线和事件网格，从而向下游使用者发送消息。 开发者可与这些消息传递系统的任意一种集成，以驱动事务并查看结果。

## <a name="deploy-a-blockchain-network"></a>部署区块链网络

Azure Blockchain Workbench 使用 Azure 资源管理器解决方案模板，将联盟区块链网络设置简化为预配置的解决方案。 该模板提供了简化的部署，可部署运行联盟所需的所有组件。 Blockchain Workbench 当前支持 Ethereum。

## <a name="use-active-directory"></a>使用 Active Directory

通过现有区块链协议，可以网络上的地址代表区块链标识。 Azure Blockchain Workbench 将区块链标识与 Active Directory 标识相关联，以此提取区块链标识，让生成具有 Active Directory 标识的企业应用程序变得更加简单。

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>将链上数据与链下存储同步

Azure Blockchain Workbench 可自动将区块链上的数据与链下存储同步，让区块链事件和数据分析变得更加容易。 可查询 SQL Server 等链下数据库系统，而不是直接从区块链提取数据。 对于正在进行数据分析任务的最终用户而言，无需具备区块链专业知识。

## <a name="support-and-feedback"></a>支持和反馈

对于 Azure 区块链新闻，请访问 [Azure 区块链博客](https://azure.microsoft.com/blog/topics/blockchain/)，以随时了解 Azure 区块链工程团队提供的区块链服务产品和信息。

若要提供产品反馈或请求新功能，请通过[区块链的 Azure 反馈论坛](https://aka.ms/blockchainuservoice)来为意见发布投票。

### <a name="community-support"></a>社区支持

与 Microsoft 工程师和 Azure 区块链社区专家交流。

* [Azure 区块链 MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 体系结构](architecture.md)
