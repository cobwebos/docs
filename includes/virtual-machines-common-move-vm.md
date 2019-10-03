---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 61990e785741799fcbcd4e6df965953bd9944f4d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172922"
---
## <a name="use-the-azure-portal-to-move-a-vm-to-a-different-subscription"></a>使用 Azure 门户将 VM 移动到其他订阅
可以使用 Azure 门户将 VM 及其关联的资源移动到其他订阅。

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 单击“浏览” > “资源组”，并选择包含要移动的 VM 的资源组。  
3. 在资源组页的顶部，选择“移动”，然后选择“移动到另一个订阅”   。 此时将打开“移动资源”  页。
4. 选择要移动的每个资源。 大多数情况下，应移动列出的所有相关资源。
5. 选择要将 VM 移到其中的**订阅**。
6. 选择一个现有“资源组”，或者输入名称以创建新的资源组  。
7. 完成后，选择你了解将创建新的资源 ID 并且移动后需要将新 ID 用于 VM，然后单击“确定”  。

## <a name="use-the-azure-portal-to-move-a-vm-to-another-resource-group"></a>使用 Azure 门户将 VM 移动到其他资源组
可以使用 Azure 门户将 VM 及其关联的资源移动到其他资源组。

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 单击“浏览” > “虚拟机”，并从列表中选择要移动的 VM。  
3. 在 VM 页中，选择资源组标签旁边的“更改”  。 此时将打开“移动资源”  页。
4. 选择要移动的每个资源。 大多数情况下，应移动列出的所有相关资源。
5. 选择一个现有“资源组”，或者输入名称以创建新的资源组  。
6. 完成后，选择你了解将创建新的资源 ID 并且移动后需要将新 ID 用于 VM，然后单击“确定”  。

