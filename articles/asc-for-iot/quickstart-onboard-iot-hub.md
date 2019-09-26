---
title: 在 IoT 中心启用适用于 IoT 的 Azure 安全中心服务 | Microsoft Docs
description: 了解如何在 IoT 中心启用适用于 IoT 的 Azure 安全中心服务。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d9c5352a90d5bcacbaf27b7b62be61fc404e87a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299487"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>快速入门：在 IoT 中心启用适用于 IoT 的 Azure 安全中心服务

本文介绍如何在现有 IoT 中心启用适用于 IoT 的 Azure 安全中心服务。 如果当前没有 IoT 中心，请参阅[使用 Azure 门户创建 IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)以开始操作。 

> [!NOTE]
> 适用于 IoT 的 Azure 安全中心目前仅支持标准层 IoT 中心。
> 适用于 IoT 的 Azure 安全中心是一种单中心解决方案。 如果需要多个中心，则需要多个适用于 IoT 的 Azure 安全中心解决方案。 

## <a name="prerequisites-for-enabling-the-service"></a>启用服务的先决条件

- Log Analytics 工作区
  - 默认情况下，适用于 IoT 的 Azure 安全中心将两种类型的信息存储在 Log Analytics 工作区中：**安全警报**和**建议**。 
  - 可以选择添加另一信息类型（**原始事件**）的存储。 请注意，在 Log Analytics 中存储**原始事件**会带来额外的存储成本。 
- IoT 中心（标准层）
- 满足所有[服务先决条件](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>在 IoT 中心启用适用于 IoT 的 Azure 安全中心 

若要在 IoT 中心启用安全性，请执行以下操作： 

1. 在 Azure 门户中打开你的 **IoT 中心**。 
1. 在“安全性”  菜单下，单击“保护 IoT 解决方案” 
1. 将“启用”  保留为默认选中状态。 
1. 选择 Log Analytics 工作区。
1. 提供 Log Analytics 工作区详细信息。 
   - 选择启用“孪生收集”，方法是让“孪生收集”开关保持“打开”状态。   
   - 通过在 Log Analytics 中选择“存储原始设备安全事件”  ，选择除存储默认存储信息类型之外还存储**原始事件**。 将“原始事件”  切换为“打开”  。 
    
1. 单击“ **保存**”。 

祝贺你！ 你已完成在 IoT 中心启用适用于 IoT 的 Azure 安全中心。 

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何配置解决方案...

> [!div class="nextstepaction"]
> [配置解决方案](quickstart-configure-your-solution.md)


