---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097640"
---
创建可用性组侦听器之后，可能需要调整侦听器资源的 RegisterAllProvidersIP 和 HostRecordTTL 群集参数。 这些参数可以减少故障转移后的重新连接时间，这样可以防止连接超时。 有关这些参数以及示例代码的详细信息，请参阅[创建或配置可用性组侦听器](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。

<!-- Update_Description: update meta properties -->