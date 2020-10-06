---
title: 发行说明：Azure Synapse Analytics（工作区预览版）
description: Azure Synapse Analytics（工作区预览版）的发行说明
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031664"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Azure Synapse Analytics（工作区预览版）发行说明

本文介绍 Azure Synapse Analytics（工作区）的限制和问题。 如需相关信息，请参阅[什么是 Azure Synapse Analytics（工作区）](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- 问题及其对客户的影响：SDK 创建的工作区无法启动 Synapse Studio

- 解决方法：请完成下列步骤： 
  1.    通过运行 `az synapse workspace create` 来创建工作区。
  2.    通过运行 `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` 来提取托管标识 ID。
  3.    通过运行 ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` 将工作区作为角色添加到存储帐户。
  4.    通过运行 ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` 来添加防火墙规则。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure Synapse](overview-what-is.md)
* [入门](get-started.md)
* [常见问题解答](overview-faq.md)
