---
title: "Azure 容器实例区域和资源可用性 | Azure Docs"
description: "了解哪些 Azure 区域支持容器实例的部署，以及这些实例的 CPU 和内存限制。"
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: marsma
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 2b9b1b864bbfd73383759212dd7d91f8e4941544
ms.contentlocale: zh-cn
ms.lasthandoff: 09/01/2017

---

# <a name="region-availability-for-azure-container-instances"></a>Azure 容器实例的区域可用性

预览期间，Azure 容器实例在具有指定 CPU 和内存限制的以下区域中可用。

| 位置 | 操作系统 | CPU | 内存 (GB) |
| -------- | -- | :---: | :-----------: |
| 西欧、美国西部、美国东部 | Linux | 2 | 7 |
| 西欧、美国西部、美国东部 | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>资源可用性

在这些资源限制内创建的容器实例受部署区域内可用性的限制。 某个区域负载较重时，部署实例时可能会失败。

若要减少此类部署失败，请尝试部署具有较低 CPU 和内存设置的实例，或稍后尝试部署。

## <a name="next-steps"></a>后续步骤

有关容器实例部署故障排除的详细信息，请参阅[排查 Azure 容器实例的部署问题](container-instances-troubleshooting.md)。
