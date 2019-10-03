---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: dce9b4ffa05b48fc1789859328b221ac1a0f3a27
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594439"
---
获得使用表单识别器所需的访问权限时，你会收到一封欢迎电子邮件，其中包含多个链接和资源。 使用该邮件中的“Azure 门户”链接打开 Azure 门户并创建表单识别器资源。 在“创建”窗格中提供以下信息： 

|    |    |
|--|--|
| **名称** | 资源的描述性名称。 建议使用描述性名称，例如“MyNameFormRecognizer”  。 |
| **订阅** | 选择已被授予访问权限的 Azure 订阅。 |
| **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
| **定价层** | 资源的费用取决于所选的定价层和使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。
| **资源组** | 将包含资源的 [Azure 资源组](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)。 可以创建新组或将其添加到预先存在的组。 |

> [!IMPORTANT]
> 通常情况下，在 Azure 门户中创建认知服务资源时，可以选择创建多服务订阅密钥（跨多个认知服务使用）或单服务订阅密钥（仅与单个具体的认知服务配合使用）。 但是，由于表单识别器为预览版，因此未将其包括在多服务订阅中。除非使用欢迎电子邮件中提供的链接，否则你不能创建单一服务订阅。

表单识别器资源完成部署以后，请在门户的“所有资源”列表中找到并选中它。  然后，选择“密钥”选项卡以查看订阅密钥。  任一密钥都会为应用提供资源访问权限。 复制**密钥 1** 的值。