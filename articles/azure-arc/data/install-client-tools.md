---
title: 安装客户端工具
description: 安装 azdata、kubectl、Azure CLI、psql、Azure Data Studio (预览体验) ，并为 Azure Data Studio 提供 Arc 扩展
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d61bc99e851b28712262dba8512c06b6e8872c0e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108212"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>安装用于部署和管理已启用 Azure Arc 的数据服务的客户端工具

> [!IMPORTANT]
> 如果要更新到新的每月发布版本，请务必更新到最新版本的 Azure Data Studio、Azure 数据 CLI (azdata) 工具，以及 Azure 数据 CLI 和 Azure Arc 扩展以获得 Azure Data Studio。

本文档将指导你完成在客户端计算机上安装 Azure 数据 CLI (azdata) 、Azure Data Studio、Azure CLI (az) 和 Kubernetes CLI 工具 (kubectl) 的步骤。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>用于创建和管理启用了 Azure Arc 的数据服务的工具 

下表列出了创建和管理启用了 Azure Arc 的数据服务所需的常用工具，以及如何安装这些工具：

| 工具 | 必选 | 说明 | 安装 |
|---|---|---|---|
| Azure 数据 CLI (azdata)  | 是 | 用于安装和管理大数据群集的命令行工具。 Azure 数据 CLI 还包括一个命令行实用程序，用于连接到 Azure SQL，并使用命令 SQL Server 实例和 Postgres 服务器 `azdata sql query` ， (从命令行运行单个查询) ， `azdata sql shell` (交互式 shell) `azdata postgres query` 和 `azdata postgres shell` 。 | [安装](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | 是 | 丰富的体验工具，用于连接和查询各种数据库，包括 Azure SQL、SQL Server、PostrgreSQL 和 MySQL。 Azure Data Studio 的扩展为启用了 Azure Arc 的数据服务提供管理体验。 | [安装](/sql/azure-data-studio/download-azure-data-studio) |
| 适用于 Azure Data Studio 的 Azure 数据 CLI 扩展 | 是 | 将安装 Azure 数据 CLI 的 Azure Data Studio 的扩展（如果尚未安装）。| 从 Azure Data Studio 中的扩展库安装。|
| 适用于 Azure Data Studio 的 Azure Arc 扩展 | 是 | 为启用了 Azure Arc 的数据服务提供管理体验的 Azure Data Studio 扩展。 Azure Data Studio 的 Azure 数据 CLI 扩展存在依赖关系。 | 从 Azure Data Studio 中的扩展库安装。|
| Azure Data Studio 中的 PostgreSQL 扩展 | 否 | 为 PostgreSQL 提供管理功能的 Azure Data Studio 的 PostgreSQL 扩展。 | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> 从 Azure Data Studio 中的扩展库安装。|
| Azure CLI (az) <sup>1</sup> | 是 | 用于管理 Azure 服务的新式命令行接口。 与 AKS 部署一起使用，并将启用了 Azure Arc 的数据服务清单和计费数据上传到 Azure。  ([详细信息](/cli/azure/?view=azure-cli-latest&preserve-view=true)) 。 | [安装](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Kubernetes CLI (kubectl) <sup>2</sup> | 是 | 用于管理 Kubernetes 群集的命令行工具 ([详细信息](https://kubernetes.io/docs/tasks/tools/install-kubectl/)) 。 | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| 卷 <sup>3</sup> | 对于某些示例脚本是必需的。 | 使用 URL 传输数据的命令行工具。 | [Windows](https://curl.haxx.se/windows/) \| Linux：安装 curl 包 |
| oc | 是 Red Hat OpenShift 和 Azure Redhat OpenShift 部署所必需的。 |`oc` 是 Open Shift 命令行接口 (CLI)。 | [安装 CLI](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> 必须使用 Azure CLI 版本2.0.4 或更高版本。 如果需要，可运行 `az --version` 来查找版本。

<sup>2</sup> 必须使用 `kubectl` 1.13 版或更高版本。 此外，`kubectl` 的版本应该加或减 Kubernetes 群集的一个次要版本。 要在 `kubectl` 客户端上安装特定版本，请参阅[通过 curl 安装 `kubectl` 二进制文件](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl)（在 Windows 10 上，使用 cmd.exe 而不是 Windows PowerShell 来运行 curl）。

<sup>3</sup> 如果使用的是 PowerShell，则卷曲是 Invoke-WebRequest cmdlet 的别名。

## <a name="next-steps"></a>后续步骤

[创建 Azure Arc 数据控制器](create-data-controller.md)