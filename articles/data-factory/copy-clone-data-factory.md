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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85552969"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>在 Azure 数据工厂中复制或克隆数据工厂

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何在 Azure 数据工厂中复制或克隆数据工厂。

## <a name="use-cases-for-cloning-a-data-factory"></a>克隆数据工厂的用例

以下可能是有助于复制或克隆数据工厂的一些情况：

- 将数据工厂移到新区域。 若要将数据工厂移到其他区域，最佳方法是在目标区域中创建副本，并删除现有数据工厂。

- **重命名数据工厂**。 Azure 不支持重命名资源。 若要重命名数据工厂，可以使用其他名称克隆该数据工厂，然后删除现有数据工厂。

- **调试更改**，在调试功能不足时进行。 在大多数情况下，可以使用[调试](iterative-development-debugging.md)。 在其他情况下，在克隆的沙盒环境中测试更改会更有意义。 例如，当触发器在文件到达时触发而不是在翻转时间窗口中触发时，参数化 ETL 管道的行为可能不容易只通过调试进行测试。 在这些情况下，可能需要克隆沙盒环境进行试验。 由于 Azure 数据工厂主要按运行次数收费，因此第二个工厂不会产生任何额外费用。

## <a name="how-to-clone-a-data-factory"></a>如何克隆数据工厂

1. 首先需要从 Azure 门户创建目标数据工厂，这是先决条件。

1. 如果你处于 GIT 模式下：
    1. 每次从门户发布时，工厂的资源管理器模板都会保存到 adf\_publish 分支的 GIT 中
    1. 将新工厂连接到_同一_存储库，并从 adf\_publish 分支进行构建。 资源（如管道、数据集和触发器）将一起克隆

1. 如果你处于实时模式：
    1. 可以利用数据工厂 UI，将数据工厂的整个有效负载导出到资源管理器模板文件和参数文件中。 可以从门户中的“ARM 模板\导出资源管理器模板”按钮访问它们。
    1. 你可以对参数文件进行适当的更改，并为新工厂换入新值
    1. 接下来，可以通过标准的资源管理器模板部署方法将其部署。

1. 如果源工厂中有 SelfHosted IntegrationRuntime，则需要在目标工厂中使用相同的名称预先创建它。 若要在不同工厂中共享自承载 Integration Runtime 时，可以在共享自承载 IR 时使用[此处](create-shared-self-hosted-integration-runtime-powershell.md)发布的模式。

1. 出于安全原因，生成的资源管理器模板不会包含任何机密信息，例如链接服务的密码。 因此，你需要提供凭据作为部署参数。 如果手动输入凭据不适合你的设置，请考虑改为从 Azure Key Vault 检索连接字符串和密码。 [查看详细信息](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>后续步骤

在[使用 Azure 数据工厂 UI 创建数据工厂](quickstart-create-data-factory-portal.md)中查看 Azure 门户中创建数据工厂的指南。
