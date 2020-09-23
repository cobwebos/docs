---
title: Azure 流分析的持续集成和部署
description: 本文概述了适用于 Azure 流分析 (CI/CD) 管道的持续集成和部署。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: ec8f27d0376f7187fd36b3feba556dbced0946e9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933437"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>用于 Azure 流分析的持续集成和部署 (CI/CD) 

你可以使用源代码管理集成来持续部署 Azure 流分析作业。 源代码管理集成启用了一个工作流，其中的代码更新将触发到 Azure 的资源部署。 本文概述了 (CI/CD) 管道创建持续集成和部署的基本步骤。

如果你不熟悉 Azure 流分析，请开始了解 [Azure 流分析快速入门](stream-analytics-quick-create-portal.md)。

## <a name="create-a-cicd-pipeline"></a>创建 CI/CD 管道

遵循本指南中的步骤，为流分析创建 CI/CD 管道。

1. 开发 Azure 流分析查询。

   使用用于 [Visual Studio Code](quick-create-vs-code.md) 或 [Visual Studio](stream-analytics-quick-create-vs.md) 的 Azure 流分析工具在 [本地开发和测试查询](develop-locally.md)。 你还可以将 [现有作业导出](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) 到本地项目中。

2. 将 Azure 流分析项目提交到源代码管理系统，如 Git 存储库。

3. 使用 [Azure 流分析 CI/CD 工具](cicd-tools.md) 生成项目并生成用于部署的 Azure 资源管理模板。

4. 为质量回归运行 [自动脚本测试](cicd-tools.md#automated-test) 。

5. 将[作业自动部署](cicd-tools.md#deploy-to-azure)到 Azure。

## <a name="auto-build-test-and-deploy"></a>自动生成、测试和部署

你可以使用命令行和 [Azure 流分析 CI/CD 工具](cicd-tools.md) 自动生成、测试和部署。 你还可以在 [Azure Pipelines](set-up-cicd-pipeline.md)中设置 CI/CD 管道。 Azure Pipelines 以实现更高级的功能，如管道管理、可视化和触发器。

## <a name="next-steps"></a>后续步骤

* [使用 CI/CD 工具自动生成、测试和部署 Azure 流分析作业](cicd-tools.md)
* [使用 Azure Pipelines 为流分析作业设置 CI/CD 管道](set-up-cicd-pipeline.md)
