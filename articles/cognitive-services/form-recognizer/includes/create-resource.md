---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: cedd302453e75c037f17cc47d8e00a06d3f3a8cd
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996783"
---
访问 Azure 门户并<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="创建新的表单识别器资源" target="_blank">创建新的表单识别器资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 在“创建”窗格中提供以下信息：

|    |    |
|--|--|
| **名称** | 资源的描述性名称。 建议使用描述性名称，例如“MyNameFormRecognizer”。 |
| **订阅** | 选择已被授予访问权限的 Azure 订阅。 |
| **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
| **定价层** | 资源的费用取决于所选的定价层和使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。
| **资源组** | 将包含资源的 [Azure 资源组](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 可以创建新组或将其添加到预先存在的组。 |

> [!NOTE]
> 通常情况下，在 Azure 门户中创建认知服务资源时，可以选择创建多服务订阅密钥（跨多个认知服务使用）或单服务订阅密钥（仅与单个具体的认知服务配合使用）。 但是，由于表单识别器为预览版，因此未将其包括在多服务订阅中。除非使用欢迎电子邮件中提供的链接，否则你不能创建单一服务订阅。

表单识别器资源完成部署以后，请在门户的“所有资源”列表中找到并选中它。 你的密钥和终结点将位于资源的“密钥和终结点”页的“资源管理”下。 请先将它们保存到临时位置，然后继续。
