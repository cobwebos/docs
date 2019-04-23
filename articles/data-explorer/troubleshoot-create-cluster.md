---
title: Azure 数据资源管理器创建群集的故障排除
description: 本文介绍在 Azure 数据资源管理器中创建群集的故障排除步骤。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9e6b3f53f07ac86d6b648a8562be4ef45879c37e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791657"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>故障排除：Azure 数据资源管理器创建失败的群集

在 Azure 数据资源管理器中，群集创建失败的可能性不大，如出现此情况，请遵循以下步骤。

1. 请确保具有足够的权限。 若要创建群集，你必须是 Azure 订阅的“参与者”或“所有者”角色。 如有必要，请与订阅管理员联系，请他们将你添加到相应的角色。

1. 确保没有任何与在 Azure 门户的“创建群集”下输入的群集名称相关的验证错误。

1. 查看 [Azure 服务运行状况仪表板](https://azure.microsoft.com/status/)。 在你尝试创建群集的区域查找 Azure 数据资源管理器的状态。

    如果状态不佳（绿色复选标记），请在状态改善后尝试创建群集。

1. 解决问题时如仍需帮助，请打开 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中的支持请求。
