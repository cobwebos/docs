---
title: 配置适用于 IoT 的 Azure 安全中心解决方案 | Microsoft Docs
description: 了解如何使用适用于 IoT 的 Azure 安全中心配置端到端 IoT 解决方案。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: a546d153c6fe4f14ccc8c21308bd4a33385870c3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299686"
---
# <a name="quickstart-configure-your-iot-solution"></a>快速入门：配置 IoT 解决方案

本文介绍如何使用适用于 IoT 的 Azure 安全中心执行 IoT 安全解决方案的初始配置。 

## <a name="azure-security-center-for-iot"></a>适用于 IoT 的 Azure 安全中心

适用于 IoT 的 Azure 安全中心为基于 Azure 的 IoT 解决方案提供全面的端到端安全性。

使用适用于 IoT 的 Azure 安全中心时，可在一个仪表板中监视整个 IoT 解决方案，并显示 Azure 中你的所有 IoT 设备、IoT 平台和后端资源。

在 IoT 中心启用后，适用于 IoT 的 Azure 安全中心会自动识别也连接到 IoT 中心并与 IoT 解决方案相关的其他 Azure 服务。

除了自动关系检测以外，还可以选择要将其他哪些 Azure 资源组标记为 IoT 解决方案的一部分。 

做出选择后，可以添加整个订阅、资源组或单个资源。 

定义所有资源关系后，适用于 IoT 的 Azure 安全中心将利用 Azure 安全中心来提供安全建议，并针对这些资源发出警报。

## <a name="add-azure-resources-to-your-iot-solution"></a>将 Azure 资源添加到 IoT 解决方案

若要将新资源添加到 IoT 解决方案，请执行以下操作： 

1. 在 Azure 门户中打开你的 **IoT 中心**。 
1. 从左侧菜单的“安全性”  下选择并打开“资源”  。 
1. 选择“编辑”  并选择属于 IoT 解决方案的资源组。
1. 单击“添加”  。 

祝贺你！ 现已将新资源组添加到 IoT 解决方案。

适用于 IoT 的 Azure 安全中心现在会监视新添加的资源组，并在 IoT 解决方案中显示相关的安全建议和警报。

## <a name="next-steps"></a>后续步骤

请转到下一篇文章了解如何创建安全模块...

> [!div class="nextstepaction"]
> [创建安全模块](quickstart-create-security-twin.md)