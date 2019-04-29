---
title: 在 Azure 数据工厂中复制或克隆数据工厂 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中复制或克隆数据工厂
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 96ea8142e2f7794d3c15c6efb436eafa585bc8fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780917"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>在 Azure 数据工厂中复制或克隆数据工厂

本文介绍如何在 Azure 数据工厂中复制或克隆数据工厂。

## <a name="use-cases-for-cloning-a-data-factory"></a>克隆数据工厂的用例

以下可能是有助于复制或克隆数据工厂的一些情况：

-   **重命名资源**。 Azure 不支持重命名资源。 若要重命名数据工厂，可以使用其他名称克隆该数据工厂，然后删除现有的数据工厂。

-   **调试更改**，在调试功能不足时进行。 有时为了测试更改，可能需要在将更改应用到主工厂之前在其他工厂中测试更改。 在大多数情况下，可以使用“调试”。 但是，触发器中的更改（例如，当自动调用触发器或在某个时间窗口中调用触发器时更改的行为）在没有签入的情况下可能无法轻松测试。 在这些情况下，克隆工厂以及在此应用更改很有意义。 由于 Azure 数据工厂主要按运行次数收费，因此第二个工厂不会产生任何额外费用。

## <a name="how-to-clone-a-data-factory"></a>如何克隆数据工厂

1. Azure 门户中的数据工厂 UI 允许将数据工厂的整个有效负载导出到资源管理器模板，随附一份参数文件，可允许在克隆工厂时更改任何要更改的值。

1. 作为先决条件，你需要从 Azure 门户创建目标数据工厂。

1. 如果必须在源工厂 SelfHosted IntegrationRuntime，需要预先它创建具有相同目标工厂中的名称。 如果你想要共享 SelfHosted IRs 之间不同的工厂，可以使用已发布的模式[此处](author-visually.md#best-practices-for-git-integration)。

1. 如果你处于 GIT 模式下，则每次从门户发布时，工厂的资源管理器模板都会保存到存储库的 adf_publish 分支中的 GIT 中。

1. 对于其他场景，可以通过单击门户中的“导出资源管理器模板”按钮下载资源管理器模板。

1. 下载资源管理器模板之后，可以通过标准的资源管理器模板部署方法将其部署。

1. 出于安全原因，生成的资源管理器模板不包含任何机密信息，比如链接服务的密码。 因此，必须将这些密码作为部署参数提供。 如果不希望提供参数，则必须从 Azure Key Vault 获取链接服务的连接字符串和密码。

## <a name="next-steps"></a>后续步骤

在[使用 Azure 数据工厂 UI 创建数据工厂](quickstart-create-data-factory-portal.md)中查看 Azure 门户中创建数据工厂的指南。
