---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: a3db5535e65e036ab10c35b7bc066a6286a9cad7
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545191"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>配置传感器指纹提供程序

首先，我们将创建和配置传感器指纹提供程序。 传感器指纹提供程序将负责读取设备上特定于平台的传感器，并将其读数转换为云空间定位点会话使用的通用表示形式。