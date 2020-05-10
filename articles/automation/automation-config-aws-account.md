---
title: 使用 Amazon Web Services 对 Azure 自动化 runbook 进行身份验证
description: 本文介绍如何创建和验证 Azure 自动化管理的 AWS 资源中的 Runbook 的 AWS 凭据。
keywords: AWS 身份验证, 配置 AWS
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 92919d2e0cc7ca685d2b60a8e7a8cf20433bbefc
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994714"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>使用 Amazon Web Services 对 Azure 自动化 runbook 进行身份验证

使用 Amazon Web Services (AWS) 中的资源自动处理常见任务可以通过 Azure 中的自动化 Runbook 完成。 可以使用自动化 Runbook 自动处理 AWS 中的许多任务，就如使用 Azure 中的资源可以执行的任务一样。 只需要两个事项：

* AWS 订阅和一组凭据。 特别是 AWS 访问键和密钥。 有关详细信息，请查看[使用 AWS 凭据](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)这一文章。
* Azure 订阅和自动化帐户。

若要使用 AWS 进行身份验证，必须指定一组 AWS 凭据，对从 Azure 自动化运行的 Runbook 进行身份验证。 如果已创建了一个自动化帐户，并且想要使用它来通过 AWS 进行身份验证，可以按照以下部分中的步骤进行操作。 如果要将帐户专用于面向 AWS 资源的 runbook，则应首先创建一个新的[自动化帐户](automation-create-standalone-account.md)，并跳过创建运行方式帐户所需的步骤。 创建帐户后，请按照以下步骤完成配置。

## <a name="configure-automation-account"></a>配置自动化帐户

要让 Azure 自动化与 AWS 通信，首先需要检索 AWS 凭据并在 Azure 自动化中将其存储为资产。 执行 AWS 文档 [Managing Access Keys for your AWS Account](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)（管理 AWS 帐户的访问密钥）中所述的以下步骤来创建访问密钥，并复制**访问密钥 ID** 和**密码访问密钥**（选择性地下载密钥文件以将其保存到某个安全位置）。

创建并复制 AWS 安全密钥后，需要使用 Azure 自动化帐户创建一个凭据资产，以安全地将其保存并使用 Runbook 对其进行引用。 按照 [Azure 自动化中的凭据资产](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal)一文中“创建新的凭据”**** 部分的步骤进行操作，并输入以下信息：

1. 在“名称”框中，输入“AWScred”或符合命名标准的适当值********。
2. 在“用户名”框中，键入**访问 ID**，并在“密码”和“确认密码”框中键入**机密访问密钥**************。

## <a name="next-steps"></a>后续步骤

* 查看[Amazon Web Services 中的 VM 自动部署](automation-scenario-aws-deployment.md)，了解如何创建 runbook 来自动执行 AWS 中的任务。
