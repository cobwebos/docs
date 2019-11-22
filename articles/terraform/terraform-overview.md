---
title: 将 Terraform 与 Azure 结合使用
description: 介绍了如何使用 Terraform 部署 Azure 基础结构并对其进行版本控制。
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 1c6ac9b67f556b039b9ffd5ed725ea1f24aeeb3a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969244"
---
# <a name="terraform-with-azure"></a>Terraform 与 Azure

[Hashicorp Terraform](https://www.terraform.io/) 是一种开源工具，用于预配和管理云基础结构。 它将基础结构编入描述云资源拓扑的配置文件中。 这些资源包括虚拟机、存储帐户和网络接口。 Terraform CLI 提供一种简单机制，用于将配置文件部署到 Azure 并对其进行版本控制。

本文介绍使用 Terraform 管理 Azure 基础结构的优点。

## <a name="automate-infrastructure-management"></a>自动化基础结构管理。

使用 Terraform 基于模板的配置文件，能够以可重复、可预测的方式定义、预配和配置 Azure 资源。 自动化基础结构有以下优点：

- 降低在部署和管理基础结构时出现人为错误的可能性。
- 多次部署同一模板，以创建相同的开发、测试和生产环境。
- 通过按需创建开发和部署环境来降低成本。

## <a name="understand-infrastructure-changes-before-being-applied"></a>应用基础结构更改前先理解这些更改

随着资源拓扑变得复杂，理解基础结构更改的含义和影响可能会很困难。

通过 Terraform CLI，用户可在应用基础结构更改之前验证和预览这些更改。 以安全方式预览基础结构更改有以下优点：
- 通过快速理解建议的更改及其影响，团队成员可提高协作效率。
- 可在开发进程中尽早发现意外更改

## <a name="deploy-infrastructure-to-multiple-clouds"></a>将基础结构部署到多个云

Terraform 擅长跨多个云提供商部署基础结构。 开发者借助它可使用一致的工具来管理每个基础结构定义。

## <a name="next-steps"></a>后续步骤

现在你已大致了解 Terraform 及其优点，下面是建议的后续步骤：

- 首先，[安装并配置 Terraform 以使用 Azure](/azure/virtual-machines/linux/terraform-install-configure)。
- [使用 Terraform 创建 Azure 虚拟机](/azure/virtual-machines/linux/terraform-create-complete-vm)
- 浏览[适用于 Terraform 的 Azure 资源管理器模块](https://www.terraform.io/docs/providers/azurerm/) 