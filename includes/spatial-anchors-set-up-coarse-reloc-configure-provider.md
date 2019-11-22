---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: c18e40b8950d5e75398382a41cf0acc3505ea5a6
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092983"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>配置传感器指纹提供程序

首先，我们将创建和配置传感器指纹提供程序。 这将有助于读取设备上特定于平台的传感器，并将其抽象为云定位点会话使用的常见表示形式。