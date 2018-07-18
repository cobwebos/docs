---
title: Azure DevOps 项目概述 | Microsoft Docs
description: 了解 Azure DevOps 项目的价值
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: 992b469803b26a11e7ca26142beba7d238e25452
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952205"
---
# <a name="overview-of-azure-devops-project"></a>Azure DevOps 项目的概述

通过 Azure DevOps 项目可以轻松开始使用 Azure。 通过 DevOps 项目资源，仅需几个简单的步骤即可从 Azure 门户在所选的 Azure 服务上启动最喜爱的应用类型。 DevOps 项目提供开发、部署和监视应用程序所需的一切设置。
通过 DevOps 项目仪表板，从 Azure 门户的单一视图即可监视代码提交、生成和部署。

## <a name="why-should-i-use-the-azure-devops-project"></a>为何应该使用 Azure DevOps 项目？

Azure DevOps 项目将整个持续集成 (CI) 和持续交付 (CD) 管道的设置自动执行到 Azure。  可以使用现有代码或使用所提供的示例应用程序之一，然后快速将该应用程序部署到不同的 Azure 服务，例如虚拟机、应用服务、Azure 容器服务、Azure SQL 数据库和 Azure Service Fabric。  

Azure DevOps 项目可完成初始配置 DevOps 管道的所有工作，包括设置初始 Git 存储库、配置 CI/CD 管道、创建用于监视的 Application Insights 资源，以及通过在 Azure 门户上创建 Azure DevOps 项目仪表板提供整个解决方案的单一视图。

使用 Azure DevOps 项目，可以：

* 快速将应用程序部署到 Azure
* 自动设置 VSTS CI/CD 管道
* 将 DevOps 项目用作模板，查看并了解如何通过 VSTS 正确地将 CI/CD 设置到 Azure
* 开始使用指向 Azure 的 CI/CD 管道，然后进一步地根据特定方案自定义发布管道

## <a name="how-do-i-use-the-azure-devops-project"></a>如何使用 Azure DevOps 项目？

可从 Azure 门户获取 Azure DevOps 项目。  创建 Azure DevOps 项目资源的方式与在门户中创建任何其他 Azure 资源相同。  DevOps 项目为各种配置选项提供分步的向导式体验。  

在初始设置过程中，选择多个配置选项。  这些选项包括：

* 使用所提供的示例应用或使用自己的代码
* 选择应用语言
* 根据基于语言的应用框架
* 选择 Azure 服务（部署目标）
* VSTS 账户（新建或现有）
* 选择 Azure 订阅
* 选择 Azure 服务的位置
* 从 Azure 服务的各种定价层中进行选择

在使用 Azure DevOps 项目后，还可以从 Azure 门户上 Azure DevOps 项目仪表板的单个位置删除所有资源。

## <a name="azure-devops-project-and-vsts-integration"></a>Azure DevOps 项目和 VSTS 集成

DevOps 项目由 VSTS 提供支持。  DevOps 项目自动执行 VSTS 将 CI/CD 设置到 Azure 所需的所有工作。  在新建或现有的 VSTS 帐户中创建 Git 存储库。  DevOps 项目将示例应用程序或现有代码提交到新的 Git 存储库。  自动执行还为生成建立 CI 触发器，使每个新的代码提交都启动生成。  DevOps 项目还创建了 CD 触发器并将每个新的成功的生成部署到所选的 Azure 服务。  可以为其他方案自定义生成和发布定义。  还可以克隆生成和发布定义，用于其他项目。

创建 DevOps 项目后，可以：

* 自定义生成和发布管道
* 使用拉取请求管理代码流并保持高质量
* 在合并代码之前，测试和生成每个提交以提升质量要求
* 跟踪项目积压工作和应用程序的问题

## <a name="how-do-i-start-using-the-azure-devops-project"></a>如何开始使用 Azure DevOps 项目？

* [Azure DevOps 项目入门](https://docs.microsoft.com/azure/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Azure DevOps 项目视频

* [通过 Azure DevOps 项目创建 CI/CD](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
