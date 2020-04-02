---
title: 管理 Azure 数据资源管理器群集中的语言扩展。
description: 使用语言扩展将其他语言集成到 Azure 数据资源管理器 KQL 查询中。
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522467"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>管理 Azure 数据资源管理器群集中的语言扩展（预览）

语言扩展功能允许您使用语言扩展插件将其他语言集成到 Azure 数据资源管理器 KQL 查询中。 使用相关脚本运行用户定义函数 （UDF） 时，脚本将获取表格数据作为其输入，并有望生成表格输出。 插件的运行时托管在[沙盒](/azure/kusto/concepts/sandboxes)中，一个隔离和安全的环境，在群集的节点上运行。 在本文中，您将在 Azure 门户中管理 Azure 数据资源管理器群集中的语言扩展插件。

> [!NOTE]
> 当前支持的 Azure 数据资源管理器语言扩展是 Python 和 R。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)。

## <a name="enable-language-extensions-on-your-cluster"></a>在群集上启用语言扩展

> [!WARNING]
> 请在启用语言扩展之前查看[限制](#limitations)。

执行以下步骤以在群集上启用语言扩展：

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集。 
1. 在“设置”中选择“配置”。******** 
1. 在 **"配置"** 窗格中，选择 **"打开**"以启用语言扩展。
1. 选择“保存”。 
 
    ![语言扩展](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> 启用语言扩展过程可能需要几分钟时间。 在此期间，群集将挂起。
 
## <a name="run-language-extension-integrated-queries"></a>运行语言扩展集成查询

* 了解如何运行[Python 集成 KQL 查询](/azure/kusto/query/pythonplugin)。
* 了解如何运行[R 集成 KQL 查询](/azure/kusto/query/rplugin)。 

## <a name="disable-language-extensions-on-your-cluster"></a>禁用群集上的语言扩展

> [!NOTE]
> 禁用语言扩展可能需要几分钟时间。

执行以下步骤来禁用群集上的语言扩展：

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集。 
1. 在“设置”中选择“配置”。******** 
1. 在 **"配置"** 窗格中，选择 **"关闭**"以禁用语言扩展。
1. 选择“保存”。 

    ![关闭语言扩展](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>限制

* 语言扩展功能不支持[磁盘加密](manage-cluster-security.md)。 
* 语言扩展运行时沙盒分配磁盘空间，即使相关语言范围内没有查询运行也是如此。

