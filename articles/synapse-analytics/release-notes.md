---
title: 发行说明：Azure Synapse Analytics（工作区）
description: Azure Synapse Analytics（工作区）的发行说明
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: add5c89e83f33980803bf571239023859653c4f1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059616"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Azure Synapse Analytics（预览版）发行说明

本文介绍 Azure Synapse Analytics（工作区）的限制和问题。 如需相关信息，请参阅[什么是 Azure Synapse Analytics（工作区）](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse（工作区） 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- 问题及其对客户的影响：SDK 创建的工作区无法启动 Synapse Studio

- 解决方法：请完成下列步骤： 
  1.    通过运行 `az synapse workspace create` 来创建工作区。
  2.    通过运行 `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` 来提取托管标识 ID。
  3.    通过运行 ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` 将工作区作为角色添加到存储帐户。
  4.    通过运行 ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` 来添加防火墙规则。

## <a name="next-steps"></a>后续步骤

* [创建工作区](quickstart-create-workspace.md)
* [使用 Synapse Studio](quickstart-synapse-studio.md)
* [创建 SQL 池](quickstart-create-sql-pool-portal.md)
* [使用 SQL 按需版本](quickstart-sql-on-demand.md)
* [创建 Apache Spark 池](quickstart-create-apache-spark-pool-portal.md)