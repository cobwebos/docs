---
title: 使用 Habitat 将应用程序部署到 Azure
description: 了解如何以一致的方式将应用程序部署到 Azure 虚拟机和容器
keywords: azure, chef, devops, 虚拟机, 概述, 自动化, habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60388805"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>使用 Habitat 将应用程序部署到 Azure
[Habitat](https://www.habitat.sh/) 是一种应用程序打包和运行时系统，用于将应用程序及其自动化功能捆绑在一起，作为部署单元。 这将实现应用程序的终极可移植性，使其在不执行重写或重新打包操作的情况下，即可部署到容器、虚拟机、祼机或 PaaS。

本文将介绍使用 Habitat 的主要好处。

## <a name="modernize-and-move-legacy-applications"></a>现代化和移动旧版应用程序
使用 Habitat 重新打包旧版应用程序，以将其从较旧操作系统和中间件中释放。 生成的项目具有可移植性，可轻松地移植到较新的基础结构上，例如在云中运行的虚拟机或容器。

## <a name="accelerate-container-adoption"></a>加速采用容器
Habitat 解决了连续部署面向微服务的复杂应用程序问题，因为该系统可以准确地表示运行时依赖项。 无需生成复杂的业务流程，即可超越对单个组件执行简单的蓝色/绿色部署，构建复杂的部署行为。

## <a name="run-any-application-anywhere"></a>在任何位置运行任意应用程序
有了 Habitat，应用程序就可以不做修改在任意运行时环境中运行。 这包括从裸机和虚拟机到容器（例如 Docker）、群集管理系统（例如 Mesosphere 或 Kubernetes）、PaaS 系统（例如 Pivotal Cloud Foundry）的一切环境。

## <a name="integrate-into-the-chef-devops-workflow"></a>集成到 Chef DevOps 工作流中
Habitat 项目是 Chef Software 的开源项目之一，拥有强大的社区支持。 Habitat 充分利用了 Chef 在基础结构自动化方面的大量经验，为应用程序提供前所未有的自动化功能。 Chef 为 Habitat 提供商业支持，并实现 Habitat 和 Chef Automate 之间的无缝集成，从而可以将从开发到部署的应用程序发布周期完全自动化。

## <a name="next-steps"></a>后续步骤

* [试用 Habitat](https://www.habitat.sh/learn/)
