---
title: include 文件
description: include 文件
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 6d2940712f8a76173de47badd45d7c7f0f0be05c
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825470"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>创建为 Azure Dev Spaces 启用的 Kubernetes 群集

1. 通过 http://portal.azure.com 登录到 Azure 门户。
1. 选择“创建资源”，搜索“Kubernetes”，选择“Kubernetes 服务” > “创建”。

   在创建 AKS 群集窗体的每个标题下完成以下步骤。

    - **项目详细信息**：选择 Azure 订阅和一个新的或现有的 Azure 资源组。
    - **群集详细信息**：输入 AKS 群集的名称、区域（当前必须选择 EastUS、WestEurope 或 CanadaEast）、版本和 DNS 名称前缀。
    - **规模**：选择 AKS 代理节点的 VM 大小和节点数。 如果是刚开始使用 Azure Dev Spaces，则采用一个节点来公开所有功能就足够了。 在部署群集后，随时可以轻松调整节点数。 请注意，在部署 AKS 群集后无法更改 VM 大小。 但是，在部署 AKS 群集后，如果需要纵向扩展，可以轻松创建具有更大 VM 的新 AKS 群集并使用 Dev Spaces 重新部署到该更大的群集。

   请确保选择 Kubernetes 版本 1.9.6 或更高版本。

   ![Kubernetes 配置设置](../media/common/Kubernetes-Create-Cluster-2.PNG)

   完成时选择“下一步: 网络”。

1. 请确保启用 Http 应用程序路由。

   ![启用 Http 应用程序路由](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > 创建 AKS 群集时必须确保启用 Http 应用程序路由。 以后无法更改此设置。

1. 完成时依次选择“评审 + 创建”、“创建”。
