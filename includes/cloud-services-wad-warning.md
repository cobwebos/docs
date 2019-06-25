---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173292"
---
> [!WARNING]
> 为现有角色启用诊断时，将禁用部署包时已设置的所有扩展。 其中包括：
>
> * Microsoft 监视代理诊断
> * Microsoft Azure 安全监视
> * Microsoft 反恶意软件                 
> * Microsoft Monitoring Agent
> * Microsoft 服务探查器代理      
> * Microsoft Azure 域扩展        
> * Microsoft Azure 诊断扩展   
> * Microsoft Azure 远程桌面扩展
> * Microsoft Azure 日志收集器
>
> 部署更新的角色后，可以通过 Azure 门户或 PowerShell 重置扩展。
>
