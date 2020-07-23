---
title: 在 Azure 数据工厂中复制或克隆数据工厂
description: 了解如何在 Azure 数据工厂中复制或克隆数据工厂
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 304c39f4b6f7852068d4e72adfad2d41eeefc26c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85552969"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>在 Azure 数据工厂中复制或克隆数据工厂

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何在 Azure 数据工厂中复制或克隆数据工厂。

## <a name="use-cases-for-cloning-a-data-factory"></a>克隆数据工厂的用例

以下可能是有助于复制或克隆数据工厂的一些情况：

- **将数据工厂移动**到新区域。 如果要将数据工厂移动到其他区域，最佳方法是在目标区域中创建副本，并删除现有的副本。

- **重命名数据工厂**。 Azure 不支持重命名资源。 如果要重命名数据工厂，可以使用其他名称克隆数据工厂，并删除现有数据工厂。

- **调试更改**，在调试功能不足时进行。 在大多数情况下，可以使用[调试](iterative-development-debugging.md)。 在其他情况下，在克隆的沙箱环境中测试更改会更有意义。 例如，如果在文件到达时触发触发器而不是在翻转时间范围内触发触发器，则参数化 ETL 管道的行为方式可能无法通过单独调试轻松进行测试。 在这些情况下，你可能想要克隆沙盒环境以试验。 由于 Azure 数据工厂主要按运行数量收费，因此第二个工厂不会产生任何额外的费用。

## <a name="how-to-clone-a-data-factory"></a>如何克隆数据工厂

1. 首先，需要从 Azure 门户创建目标数据工厂。

1. 如果处于 GIT 模式下：
    1. 每次从门户发布时，出厂的资源管理器模板都会保存到 adf 发布分支中的 GIT \_
    1. 将新工厂连接到_相同_的存储库，并从 adf \_ 发布分支进行构建。 管道、数据集和触发器等资源将通过

1. 如果处于实时模式：
    1. 利用数据工厂 UI，你可以将数据工厂的整个负载导出到资源管理器模板文件和参数文件中。 可以通过门户中的 " **ARM 模板 \ 导出资源管理器模板**" 按钮访问它们。
    1. 你可以对参数文件进行适当的更改，并将新的工厂替换为新值
    1. 接下来，可以通过标准资源管理器模板部署方法进行部署。

1. 如果源工厂中有 SelfHosted IntegrationRuntime，则需要在目标工厂中使用相同的名称预先创建它。 如果要在不同工厂之间共享 SelfHosted Integration Runtime，可以使用[此处](create-shared-self-hosted-integration-runtime-powershell.md)发布的模式来共享 SelfHosted IR。

1. 出于安全原因，生成的资源管理器模板不包含任何机密信息，例如链接服务的密码。 因此，你需要提供凭据作为部署参数。 如果手动输入凭据对您的设置不是理想的，请考虑从 Azure Key Vault 检索连接字符串和密码。 [查看更多](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>后续步骤

在[使用 Azure 数据工厂 UI 创建数据工厂](quickstart-create-data-factory-portal.md)中查看 Azure 门户中创建数据工厂的指南。
