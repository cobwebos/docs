---
title: 在 IoT 中心预览版中启用适用于 IoT 的 Azure 安全中心服务 | Microsoft Docs
description: 了解如何在 IoT 中心启用适用于 IoT 的 Azure 安全中心服务。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 59021d09f2af9d430b118acdeb8aa977094e683e
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862380"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>快速入门：在 IoT 中心启用服务

> [!IMPORTANT]
> 适用于 IoT 的 Azure 安全中心目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何在 IoT 中心启用适用于 IoT 的 Azure 安全中心 (ASC) 预览版服务。  

> [!NOTE]
> 适用于 IoT 的 Azure 安全中心目前仅支持标准层和更高层的 IoT 中心。
> 适用于 IoT 的 Azure 安全中心是一种单中心解决方案。 如果需要多个中心，则需要多个解决方案。 

## <a name="prerequisites-for-enabling-the-service"></a>启用服务的先决条件

- Log Analytics 工作区
  - 默认情况下，适用于 IoT 的 ASC 会在 Log Analytics 工作区中存储两类信息：**安全警报**和**建议**。 
  - 可以选择添加另一信息类型（**原始事件**）的存储。 请注意，在 Log Analytics 中存储**原始事件**会带来额外的存储成本。 
- IoT 中心（标准层或更高层）

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>在 IoT 中心启用适用于 IoT 的 ASC 

若要在 IoT 中心启用安全性，请执行以下操作： 

1. 在 Azure 门户中打开 **IoT 中心**。 
2. 在左侧菜单中选择并打开“安全性”。 
3. 选择“启用 IoT 安全性”。 
4. 提供 Log Analytics 工作区详细信息。 
   - 选择存储除默认存储信息类型之外的“原始事件”，方法是让“原始事件”开关保持“打开”状态。 
   - 选择启用“孪生收集”，方法是让“孪生收集”开关保持“打开”状态。 
5. 单击“确定”。 
6. 单击“ **保存**”。 

祝贺你！ 你已完成在 IoT 中心启用适用于 IoT 的 ASC 的操作。 

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何配置解决方案...

> [!div class="nextstepaction"]
> [配置解决方案](quickstart-configure-your-solution.md)