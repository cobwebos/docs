---
title: Azure Red Hat OpenShift 简介
description: 了解 Microsoft Azure Red Hat OpenShift 的功能和优势，以部署和管理基于容器的应用程序。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: a3bdc3673474b778aa7c1003e48e215bac6d05bf
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628514"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

借助 Microsoft Azure Red Hat OpenShift  服务，可以部署完全托管的 [OpenShift](https://www.openshift.com/) 群集。

Azure Red Hat OpenShift 扩展 [Kubernetes](https://kubernetes.io/)。 在生产环境中使用 Kubernetes 来运行容器需要其他工具和资源。 这通常包括需要同时处理映像注册表、存储管理、网络解决方案以及日志记录和监视工具 - 所有这些都必须一起进行版本控制和测试。 构建基于容器的应用程序需要与中间件、框架、数据库和 CI/CD 工具的更多集成工作。 Azure Red Hat OpenShift 将这一切合并到单个平台，简化 IT 团队操作，并为应用程序团队提供了执行所需的内容。

Azure Red Hat OpenShift 由 Red Hat 和 Microsoft 共同设计、运营和支持，可以提供集成式支持体验。 无需操作虚拟机，无需修补。 Red Hat 和 Microsoft 代表你修补、更新和监视主数据库、基础结构和应用程序节点。 Azure Red Hat OpenShift 群集部署到 Azure 订阅，并包含在你的 Azure 帐单中。

可以选择自己的注册表、网络、存储和 CI/CD 解决方案，或将内置解决方案用于自动源代码管理、容器和应用程序生成、部署、缩放、运行状况管理等。 Azure Red Hat OpenShift 通过 Azure Active Directory 提供集成式登录体验。

要开始使用，请完成[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程。

## <a name="access-security-and-monitoring"></a>访问权限、安全性和监视

为了改进安全性和管理，Azure Red Hat OpenShift 让你与 Azure Active Directory (Azure AD) 集成，并使用基于 Kubernetes 角色的访问控制 (RBAC)。 也可监视群集和资源的运行状况。

## <a name="cluster-and-node"></a>群集和节点

Azure Red Hat OpenShift 节点在 Azure 虚拟机上运行。 可以将存储连接到节点和 Pod，并升级群集组件。

## <a name="next-steps"></a>后续步骤

了解 Azure Red Hat OpenShift 的先决条件：

> [!div class="nextstepaction"]
> [设置开发环境](tutorial-create-cluster.md)
