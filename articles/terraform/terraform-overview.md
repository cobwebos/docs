---
title: 将 Terraform 与 Azure 结合使用
description: 介绍如何使用 Terraform 部署 Azure 基础结构并对其进行版本控制。
ms.service: virtual-machines-linux
keywords: terraform, devops, 概述, plan, apply, 自动
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5d313bda6a1067e6d023f62fb26704f9aee5c7bf
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114466"
---
# <a name="terraform-with-azure"></a>Terraform 与 Azure

[Hashicorp Terraform](https://www.terraform.io/) 是一种开源工具，用于预配和管理云基础结构。 它编写了描述云资源拓扑的配置文件中的基础结构，例如虚拟机、存储帐户和网络接口。 Terraform 的命令行接口 (CLI) 提供一种简单机制，用于将配置文件部署到 Azure 或任何其他支持的云并对其进行版本控制。

本文介绍使用 Terraform 管理 Azure 基础结构的优点。

## <a name="automate-infrastructure-management"></a>自动化基础结构管理。

使用 Terraform 基于模板的配置文件，能够以可重复、可预测的方式定义、预配和配置 Azure 资源。 自动化基础结构有以下优点：

- 降低在部署和管理基础结构时出现人为错误的可能性。
- 多次部署同一模板，以创建相同的开发、测试和生产环境。
- 通过按需创建开发和部署环境来降低成本。

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>应用基础结构更改前先理解这些更改 

随着资源拓扑变得复杂，理解基础结构更改的含义和影响可能会很困难。

Terraform 提供的命令行接口 (CLI) 允许用户在部署基础结构更改之前对其进行验证和预览。 以安全、高效的方式预览基础结构更改有以下优点：
- 通过快速理解建议的更改及其影响，团队成员可提高协作效率。
- 可在开发进程中尽早发现意外更改


## <a name="deploy-infrastructure-to-multiple-clouds"></a>将基础结构部署到多个云

Terraform 工具常用于多云方案，在此类方案中，类似的基础结构会部署到 Azure、其他云提供程序或本地数据中心。 它使开发人员能够使用相同的工具和配置文件管理多个云提供程序上的基础结构。

## <a name="next-steps"></a>后续步骤

现在你已大致了解 Terraform 及其优点，下面是建议的后续步骤：

- 首先，[安装并配置 Terraform 以使用 Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)。
- [使用 Terraform 创建 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- 浏览[适用于 Terraform 的 Azure 资源管理器模块](https://www.terraform.io/docs/providers/azurerm/) 