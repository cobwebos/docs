---
title: include 文件
description: include 文件
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4c50ca2449787d681d8b9571083a92077d95dcd8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986598"
---
这些示例说明了可在跨租户管理方案中执行的各种任务。

| **模板** | **说明** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | 将存储帐户部署到两个不同的资源组中。|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | 创建 Azure 管理服务，将它们链接在一起并部署其他解决方案。 对于端到端部署，请使用 **rgWithAzureMgmt.json** 模板。 |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | 在目标 Azure 订阅内启用并配置 Azure 安全中心。 |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | 在委托订阅中的现有 Log Analytics 工作区上部署并启用 Azure Sentinel。 |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | 使用这些模板可以将 Log Analytics VM 扩展部署到 Windows VM 和 Linux VM，并将其连接到指定的 Log Analytics 工作区 |
