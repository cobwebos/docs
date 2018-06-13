---
title: Azure Blockchain Workbench 概述
description: Azure Blockchain Workbench 及其功能概述的。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/21/2018
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 9cd8ef3977d12364759838b46632ba32e0de6e70
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075663"
---
# <a name="what-is-azure-blockchain-workbench"></a>什么是 Azure Blockchain Workbench？

Azure Blockchain Workbench 是 Azure 服务和功能的集合，旨在帮助创建和部署区块链应用程序，以便与其他组织共享业务流程和数据。 Azure Blockchain Workbench 可提供用于创建区块链应用程序的基础结构基架，使开发者能够专注于创建业务逻辑和智能协定。 此外，借助 Blockchain Workbench，通过集成多种 Azure 服务和功能，帮助自动执行常见开发任务，可更轻松地创建区块链应用程序。

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

## <a name="use-active-directory-login"></a>使用 Active Directory 登录

通过现有区块链协议，可以网络上的地址代表区块链标识。 Azure Blockchain Workbench 将区块链标识与 Active Directory 标识相关联，以此提取区块链标识，让生成具有 Active Directory 标识的企业应用程序变得更加简单。

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>将链上数据与链下存储同步

Azure Blockchain Workbench 可自动将区块链上的数据与链下存储同步，让区块链事件和数据分析变得更加容易。 可查询 SQL Server 等链下数据库系统，而不是直接从区块链提取数据。 对于正在进行数据分析任务的最终用户而言，无需具备特定的区块链专业知识。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 体系结构](blockchain-workbench-architecture.md)
