---
title: 使用 Amazon Web Services 对 Azure 自动化 runbook 进行身份验证
description: 本文介绍如何使用 Amazon Web Services 对 Runbook 进行身份验证。
keywords: AWS 身份验证, 配置 AWS
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83837174"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>使用 Amazon Web Services 对 Runbook 进行身份验证

使用 Amazon Web Services (AWS) 中的资源自动处理常见任务可以通过 Azure 中的自动化 Runbook 完成。 可以使用自动化 Runbook 自动处理 AWS 中的许多任务，就如使用 Azure 中的资源可以执行的任务一样。 若要进行身份验证，必须拥有 Azure 订阅。

## <a name="obtain-aws-subscription-and-credentials"></a>获取 AWS 订阅和凭据

若要使用 AWS 进行身份验证，必须获取 AWS 订阅并指定一组 AWS 凭据，对从 Azure 自动化运行的 Runbook 进行身份验证。 所需的特定凭据为 AWS 访问密钥和密钥。 请参阅[使用 AWS 凭据](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)。

## <a name="configure-automation-account"></a>配置自动化帐户

可以通过现有自动化帐户，使用 AWS 进行身份验证。 或者，可以为 Runbook 指定一个面向 AWS 资源的专用帐户。 在这种情况下，请新建一个[自动化帐户](automation-create-standalone-account.md)。  

## <a name="store-aws-credentials"></a>存储 AWS 凭据

必须将 AWS 凭据作为资产存储在 Azure 自动化中。 有关创建访问密钥和密钥的说明，请参阅[管理 AWS 帐户的访问密钥](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)。 当密钥可用时，将访问密钥 ID 和密钥 ID 复制到一个安全的位置。 可下载密钥文件，并将其存储在一个安全的位置。

## <a name="create-credential-asset"></a>创建凭据资产

创建并复制 AWS 安全密钥后，必须使用自动化帐户创建一个凭据资产。 该资产可用于安全地存储 AWS 密钥，并在 Runbook 中引用它们。 请参阅[使用 Azure 门户新建凭据资产](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal)。 在提供的字段中输入以下 AWS 信息：
    
* “名称” - “AWScred”，或符合命名标准的适当值
* 用户名 - 访问 ID
* 密码 - 密钥名称 

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建 Runbook 以自动执行 AWS 中的任务，请参阅[使用 Runbook 部署 Amazon Web Services VM](automation-scenario-aws-deployment.md)。