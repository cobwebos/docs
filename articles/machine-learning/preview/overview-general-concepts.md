---
title: "Azure 机器学习预览版功能的概念性概述 | Microsoft Docs"
description: "Azure 机器学习的预览版功能的概念性概述，例如订阅、帐户、工作区、项目，等等。"
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 3d4ba2ca6f7adc8b51030c02d9e9eeb2b9995bb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning---concepts"></a>Azure 机器学习 - 概念

本文阐明并介绍了使用 Azure 机器学习时需要知道的概念。 

![概念的层次结构](media/overview-general-concepts/hierarchy.png)

- **订阅：**Azure 订阅向你授予对 Azure 中的资源的访问权限。 因为 Azure 机器学习与计算、存储和许多其他 Azure 资源和服务深度集成，所以，Workbench 要求每个用户都能够访问一个有效的 Azure 订阅。 用户还必须在该订阅中具有足够的权限以便创建资源。


- **试验帐户：**试验帐户是 Azure ML 所需的一种 Azure 资源，是一种计费工具。 它包含工作区，而后者包含项目。 可以向试验帐户添加多个用户（称为_座位_）。 为了使用 Azure ML Workbench 运行试验，还必须对试验帐户具有访问权限。 


- **模型管理帐户** 模型管理帐户也是 Azure ML 所需的用于管理模型的一种 Azure 资源。 可以使用它来注册模型和清单，构建容器化 Web 服务并将其部署在本地或云中。 它还是 Azure ML 的另一种计费工具。


- **工作区：**工作区是 Azure ML 中用于共享和协作的主要组件。 项目在工作区中进行分组。 然后，可以与已添加到试验帐户中的多个用户共享工作区。


- **项目：**在 Azure 机器学习中，项目是解决问题时所做的所有工作的逻辑容器。 它映射到本地磁盘上的单个文件夹，可以向其中添加任何文件或子文件夹。 还可以将项目与 Git 存储库进行关联以便进行源代码管理和协作。  

- **试验：**在 Azure ML 中，试验是可以从单个入口点执行的一个或多个源代码文件。 它可以包含诸如数据引入、特征工程、模型训练或模型评估等任务。 当前，Azure ML 仅支持 Python 或 PySpark 试验。


- **模型：**在 Azure 机器学习中，模型是指机器学习试验的产物。 它们是配方，当正确应用于数据时，将生成预测值。 可以将模型部署到测试或生产环境，并将其用于对新数据进行评分。 部署到生产环境中后，可以对模型进行监视来查明性能和数据偏差，以及根据需要重新训练。 

- **计算目标：**计算目标是你配置的用于执行试验的计算资源。 它可以是本地计算机（Windows 或 macOS）、在本地计算机上或在 Azure 上的 Linux VM 或 HDInsight Spark 群集中运行的 Docker 容器。


- **运行：** 试验服务对运行的定义是计算目标中某个试验执行的生存期。 Azure ML 自动捕获每个运行的信息并以运行历史记录的形式提供特定试验的完整历史记录。

- **环境：**在 Azure 机器学习中，环境是指用于部署和管理模型的特定计算资源。 它可以是本地计算机、Azure 上的 Linux VM 或者是在 Azure 容器服务中运行的 Kubernetes 群集，具体取决于上下文和配置。 模型托管在这些环境中运行的 Docker 容器中并且公开为 REST API 终结点。


- **托管模型：**借助模型管理，可以作为 Web 服务部署模型、管理模型的各个版本以及监视模型的性能和指标。 托管模型注册到 Azure 机器学习模型管理帐户中。

- **清单：**当模型管理系统将模型部署到生产环境中时，它包括一个清单，该清单中可以包括模型、依赖项、评分脚本、示例数据和架构。 清单是用来创建 Docker 容器映像的配方。 使用模型管理，可以自动生成清单、创建不同的版本以及管理这些清单。 


- **映像：**可以使用清单生成（和重新生成）Docker 映像。 容器化 Docker 映像可以灵活地在云中、在本地计算机上或者在 IoT 设备上运行。 映像是自包含的，并且包含使用模型对新数据进行评分所需的所有依赖项。 

- **服务：**借助模型管理，可以将模型部署为 Web 服务。 Web 服务逻辑和依赖项封装到映像中。 每个 Web 服务都是一组基于映像的容器，可以为发往给定 URL 的服务请求提供服务。 一个 Web 服务计为单个部署。
