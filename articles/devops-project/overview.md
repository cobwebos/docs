---
title: Azure DevOps Starter 概述 |Microsoft Docs
description: 了解 Azure DevOps Starter 的价值
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 99a1fdb8caff9953041c996d0f5581318ce11c66
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233680"
---
# <a name="overview-of-azure-devops-starter"></a>Azure DevOps Starter 概述

 Azure DevOps Starter 可让你轻松地开始在 Azure 上入门。 有了它，仅需几个简单的步骤即可从 Azure 门户在所选的 Azure 服务上启动最喜爱的应用。 

 DevOps Starter 设置开发、部署和监视应用程序所需的一切。 你可以使用 DevOps 入门仪表板来监视代码提交、生成和部署，所有这些都是从 Azure 门户中的单个视图进行的。

## <a name="advantages-of-using-devops-starter"></a>使用 DevOps Starter 的优点

  DevOps Starter 自动将整个持续集成（CI）和持续交付（CD）管道设置为 Azure。  可以从现有的代码着手，也可以使用提供的示例应用程序之一。 然后，可以快速将该应用程序部署到不同的 Azure 服务，例如虚拟机、应用服务、Azure Kubernetes 服务 (AKS)、Azure SQL 数据库和 Azure Service Fabric。  

  DevOps Starter 完成 DevOps 管道初始配置的所有工作，包括设置初始 Git 存储库、配置 CI/CD 管道、创建用于监视的 Application Insights 资源，以及在 Azure 门户中创建 DevOps Projects 仪表板的整个解决方案的单一视图。

可以使用 DevOps Starter 执行以下操作：

* 快速将应用程序部署到 Azure
* 自动设置 CI/CD 管道
* 查看并了解如何正确设置 CI/CD 管道
* 进一步根据特定方案自定义发布管道

## <a name="how-to-use-devops-starter"></a>如何使用 DevOps Starter？

  Azure 门户提供了 DevOps Starter。 你可以创建 DevOps Starter 资源，就像通过门户创建任何其他 Azure 资源一样。 DevOps Projects 为各种配置选项提供了分步的向导式体验。  

在初始设置过程中，选择多个配置选项。 这些选项包括：

* 使用所提供的示例应用或使用自己的代码
* 选择应用语言
* 选择基于语言的应用框架
* 选择 Azure 服务（部署目标）
* 创建新的 Azure DevOps 组织，或使用现有的组织 
* 选择 Azure 订阅
* 选取 Azure 服务的位置
* 从 Azure 服务的各种定价层中进行选择

使用 DevOps Starter 后，还可以从 Azure 门户上的 DevOps 入门仪表板中删除单个位置的所有资源。

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter 和 Azure DevOps 集成

DevOps Starter 由 Azure DevOps 提供支持。 DevOps Starter 自动完成 Azure Pipelines 中所需的所有工作，以设置 CI/CD 管道。 它在新的或现有的 Azure DevOps 组织中创建一个 Git 存储库，然后将示例应用程序或现有代码提交到新的 Git 存储库。  

自动执行还为生成建立 CI 触发器，使每个新的代码提交都启动生成。 DevOps Starter 创建 CD 触发器，并将每个新的成功生成部署到所选的 Azure 服务。  

可以为其他方案自定义生成和发布管道。 另外，还可以克隆生成和发布管道，用于其他项目。

创建 DevOps Starter 后，可以：

* 自定义生成和发布管道
* 使用拉取请求管理代码流并保持高质量
* 在合并代码之前，测试和生成每个提交以提升质量要求
* 跟踪积压工作和应用程序的问题

## <a name="getting-started-with-devops-starter"></a>开始 DevOps 入门

* [开始 DevOps 入门](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>DevOps Starter 视频

* [通过 Azure DevOps Starter 创建 CI/CD](https://www.youtube.com/watch?v=NuYDAs3kNV8)
