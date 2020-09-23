---
title: 启用了 Azure Arc 的数据服务-发行说明
description: 最新发行说明
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 5908083be4e6ed389b606754ffef41a4a371c3e3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934165"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>发行说明-启用了 Azure Arc 的数据服务 (预览) 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>2020 年 9 月

已为公共预览版发布启用了 Azure Arc 的数据服务。 启用 Arc 的数据服务允许你在任何位置管理数据服务。

- SQL 托管实例
- PostgreSQL 超大规模

有关说明，请参阅 [什么是启用了 Azure Arc 的数据服务？](overview.md)

## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？  
> 在 Azure Kubernetes Service 上快速开始使用 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) (AKS) 、AWS 弹性 Kubernetes SERVICE (EKS) 、Google Cloud Kubernetes ENGINE (GKE) 或 Azure VM。

[安装客户端工具](install-client-tools.md)

[创建 Azure Arc 数据控制器](create-data-controller.md) (需要先安装客户端工具) 

[在 Azure arc 上创建 AZURE SQL 托管实例](create-sql-managed-instance.md) (要求首先创建 azure arc 数据控制器) 

[在 Azure arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md) ， (需要首先创建 azure arc 数据控制器) 

## <a name="known-limitations-and-issues"></a>已知限制和问题

- SQL 托管实例名称不能超过13个字符
- Azure Arc 数据控制器或数据库实例没有就地升级。
- 启用 Arc 的数据服务容器映像未签名。  可能需要将 Kubernetes 节点配置为允许拉取未签名的容器映像。  例如，如果使用 Docker 作为容器运行时，则可以设置 DOCKER_CONTENT_TRUST = 0 环境变量并重新启动。  其他容器运行时具有类似的选项，如 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration)中所示。
- 无法从 Azure 门户创建启用了 Azure Arc 的 SQL 托管实例或 PostgreSQL 超大规模服务器组。
- 现在，如果你使用的是 NFS，则需要在创建 Azure Arc 数据控制器之前在部署配置文件中将 allowRunAsRoot 设置为 true。
- 仅限 SQL 和 PostgreSQL 登录身份验证。  不支持 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上创建数据控制器需要严格的安全约束。  有关详细信息，请参阅文档。
- 不支持缩放 Postgres 超大规模辅助 _角色节点的_ 数量。
- 如果使用 azure Kubernetes 服务引擎 (使用 Azure Arc 数据控制器和数据库实例的 Azure Stack 集线器上的 AKS Engine) ，则不支持升级到较新的 Kubernetes 版本。 请先卸载 Azure Arc 数据控制器和所有数据库实例，然后再升级 Kubernetes 群集。
- 预览不支持 Postgres 版本11引擎的备份/还原。 它仅支持 Postgres 版本12的备份/还原。
