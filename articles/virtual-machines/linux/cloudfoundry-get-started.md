---
title: Microsoft Azure 上的 Cloud Foundry 入门
description: 在 Microsoft Azure 上运行 OSS 或 Pivotal Cloud Foundry
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 8645afac5734cdbffb8df6f341529e688ca526d7
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891640"
---
# <a name="cloud-foundry-on-azure"></a>Azure 上的 Cloud Foundry

Cloud Foundry 是一种开源平台即服务 (PaaS)，用于生成、部署和操作以各种语言和框架开发的十二因素应用程序。 本文档介绍了在 Azure 上运行 Cloud Foundry 的选项及其入门知识。

## <a name="cloud-foundry-offerings"></a>Cloud Foundry 产品

有两种形式的 Cloud Foundry 可在 Azure 上运行：开源 Cloud Foundry (OSS CF) 和 Pivotal Cloud Foundry (PCF)。 OSS CF 是完全[开源](https://github.com/cloudfoundry)版的 Cloud Foundrydation，由 Cloud Foundry Foundation 管理。 Pivotal Cloud Foundry 是来自 Pivotal Software Inc. 的 Cloud Foundry 的企业分发。我们来看看两个产品间的一些差异。

### <a name="open-source-cloud-foundry"></a>开源 Cloud Foundry

你可在 Azure 上部署 OSS Cloud Foundry：首先部署 BOSH 控制器，然后使用 [GitHub 上提供的说明](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)部署 Cloud Foundry。 若要了解有关使用 OSS CF 的详细信息，请参阅由 Cloud Foundry Foundation 提供的[文档](https://docs.cloudfoundry.org/)。

Microsoft 通过以下社区渠道为 OSS CF 提供最大支持：

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>[Cloud Foundry Slack](https://slack.cloudfoundry.org/) 上的 bosh-azure-cpi 渠道
- [cf-bosh 邮件列表](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) 和[服务中转站](https://github.com/Azure/meta-azure-service-broker/issues)的 GitHub 问题

>[!NOTE]
> 对 Azure 资源（例如运行 Cloud Foundry 的虚拟机）的支持级别以 Azure 支持协议为基础。 社区最大支持仅适用于 Cloud Foundry 特定的组件。

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry 包括与 OSS 分发相同的核心平台，以及一套专有管理工具和企业支持。 若要在 Azure 上运行 PCF，必须从 Pivotal 获取许可证。 Azure 市场的 PCF 产品/服务包括 90 天的试用版许可证。

这些工具包括一个简化部署和管理 Cloud Foundry 基础的 Web 应用程序 [Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/) 和一个用于管理用户和应用程序的 Web 应用程序 [Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/)。

除了上面列出的 OSS CF 支持通道外，PCF 许可证允许联系 Pivotal 以获取支持。 Microsoft 和 Pivotal 还启用了支持工作流，允许用户联系任意一方以获取帮助，并根据问题的具体所在正确地路由查询。

## <a name="azure-service-broker"></a>Azure 服务中转站

Cloud Foundry 鼓励采用[“十二因素应用”](https://12factor.net/)方法论，因为它可以促进无状态应用程序进程和有状态支持服务的清晰分离。 [服务中转站](https://docs.cloudfoundry.org/services/api.html) 提供向应用程序预配和绑定后台服务一致方法。 [Azure 服务中转站](https://github.com/Azure/meta-azure-service-broker)通过此渠道提供了部分关键 Azure 服务，包括 Azure 存储和 Azure SQL。

如果使用的是 Pivotal Cloud Foundry，也可作从 Pivotal 网络[以磁贴的形式提供](https://docs.pivotal.io/azure-sb/installing.html)服务中转站。

## <a name="related-resources"></a>相关资源

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services 插件

Cloud Foundry 非常适合用于开发敏捷软件，其中包括使用持续集成 (CI) 和持续交付 (CD)。 如果使用 Azure DevOps Services 来管理项目，并且希望设置一个面向 Cloud Foundry 的 CI/CD 管道，则可使用 [Azure DevOps Services Cloud Foundry 生成扩展](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension)。 无论是在 Azure 还是在另一环境中运行，都可以通过该插件轻松配置和自动化 Cloud Foundry 的部署。

## <a name="next-steps"></a>后续步骤

- [部署来自 Azure 市场的 Pivotal Cloud Foundry](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [将应用部署到 Azure 中的 Cloud Foundry](./cloudfoundry-deploy-your-first-app.md)
