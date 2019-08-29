---
title: 高可用性和灾难恢复 - Azure Batch | Microsoft Docs
description: 了解如何针对区域性中断问题设计 Batch 应用程序
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: 11446f5f23d470234b5dba641dc16e0bf71d9b72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094034"
---
# <a name="design-your-application-for-high-availability"></a>针对高可用性设计应用程序

Azure Batch 是一项区域性服务。 Batch 在所有 Azure 区域提供，但在创建 Batch 帐户时，必须将其与区域相关联。 然后，Batch 帐户的所有操作会应用到该区域。 例如，池和关联的虚拟机 (VM) 在 Batch 帐户所在的区域中创建。

设计使用 Batch 的应用程序时，必须考虑到 Batch 在某个区域不可用的可能性。 可能会遇到罕见的情况，即：整个区域出现问题、区域中的整个 Batch 服务出现问题，或者特定 Batch 帐户出现问题。

如果使用 Batch 的应用程序或解决方案必须始终可用，则应将其设计为可以故障转移到另一区域，或者始终在两个或更多个区域之间拆分工作负荷。 两种方法都需要至少两个 Batch 帐户，每个帐户位于不同的区域中。

## <a name="multiple-batch-accounts-in-multiple-regions"></a>多个 Batch 帐户位于多个区域中

在不同的区域中使用多个 Batch 帐户时，应用程序可以在另一区域的某个 Batch 帐户变得不可用时继续运行。 如果应用程序必须高度可用，则使用多个帐户尤其重要。

在某些情况下，可能会将应用程序设计成始终使用两个或更多个区域。 例如，在需要的容量相当大时，则可能需要使用多个区域，以应对出现大型应用程序或者将来出现增长的情况。

## <a name="design-considerations-for-providing-failover"></a>有关如何提供故障转移的设计注意事项

在提供故障转移到备用区域的功能时，需要考虑的要点是：解决方案中的所有组件都必须考虑；不能直接设置另一个 Batch 帐户。 例如，在大多数 Batch 应用程序中，需要一个 Azure 存储帐户，而存储帐户和 Batch 帐户需要位于同一区域中，这样才能获得可以接受的性能。

设计可以故障转移的解决方案时，请注意以下几点：

- 在每个区域预先创建所有必需的帐户，例如 Batch 帐户和存储帐户。 创建帐户通常不收费，只有在存储数据或使用帐户的时候才收费。
- 确保提前设置帐户的配额，这样就能够使用 Batch 帐户分配必需的核心数。
- 使用模板和/或脚本在区域中自动部署应用程序。
- 始终更新所有区域中的应用程序二进制文件和引用数据。 保持更新可确保将区域快速联机，不需等待文件的上传和部署。 例如，如果使用 Batch 应用程序包来存储和引用需安装在池节点中的自定义应用程序，则在生成新版应用程序后，应将其上传到每个 Batch 帐户并通过池配置对其进行引用（或者使新版本成为默认版本）。
- 在调用 Batch、存储以及任何其他服务的应用程序中，轻松地将客户端或负载切换到另一区域。
- 若要确保故障转移成功，最佳做法是在进行正常操作时频繁地切换到备用区域。 例如，如果两个部署位于不同的区域，则每月切换到备用区域一次。

## <a name="next-steps"></a>后续步骤

- 详细了解如何通过 [Azure 门户](batch-account-create-portal.md)、[Azure CLI](cli-samples.md)、[Powershell](batch-powershell-cmdlets-get-started.md) 或 [Batch 管理 API](batch-management-dotnet.md) 创建 Batch 帐户。
- 默认配额与某个 Batch 帐户相关联；[此文](batch-quota-limit.md)详述了默认配额值并介绍了如何才能提高配额。
