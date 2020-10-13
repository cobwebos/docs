---
title: Windows 虚拟桌面 .MSIX 应用附加概述-Azure
description: 什么是 MSIX 应用附加？ 请参阅此文。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556218"
---
# <a name="what-is-msix-app-attach"></a>什么是 MSIX 应用附加？

.MSIX 是一种新的打包格式，它提供了许多功能，旨在提高所有 Windows 应用的打包体验。 若要了解有关 .MSIX 的详细信息，请参阅 [.msix 概述](/windows/msix/overview)。

.MSIX 应用附加是将 .MSIX 应用程序提供给物理计算机和虚拟机的一种方法。 但是，.MSIX 应用附加不同于常规 .MSIX，因为它特别适用于 Windows 虚拟桌面。 本文将介绍什么是 .MSIX 应用附加内容以及可为你实现哪些功能。

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Windows 虚拟桌面中的应用程序传递选项

可以通过以下方法之一在 Windows 虚拟桌面中传递应用程序：

- 将应用放入主映像
- 使用 SCCM 或 Intune 之类的工具进行中心管理
- 动态应用设置 (AppV、VMWare AppVolumes 或 Citrix AppLayering) 
- 使用 Microsoft 和第三方工具创建自定义工具或脚本

## <a name="what-does-msix-app-attach-do"></a>.MSIX 应用附加有什么作用？

在 Windows 虚拟桌面部署中，.MSIX 应用附加可以：

- 使用 [.msix 容器](/windows/msix/msix-container)创建用户数据、OS 和应用程序之间的隔离。
- 动态提供应用程序时不必重新打包。
- 减少用户登录所用的时间。
- 降低基础结构需求和成本。

.MSIX 应用附加必须在 VDI 或 SBC 外部适用。

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>与 .MSIX 应用附加相比，传统应用分层

下表对 .MSIX 应用附加和应用分层的主要功能进行了比较。

| Feature | 传统应用程序分层  | MSIX 应用附加  |
|-----|-----------------------------|--------------------|
| 格式               | 不同的应用程序分层技术需要不同的专用格式。 | 使用本机 .MSIX 打包格式。        |
| 重新打包开销 | 专用格式要求按更新进行排序和重新打包。         | 发布为 .MSIX 的应用不需要重新打包。 但是，如果 .MSIX 包不可用，则仍会应用重新打包开销。 |
| 生态系统            | N/A (例如，供应商不提供 App-v)   | .MSIX 是 Microsoft 的主流技术，主要的 ISV 合作伙伴和公司内的应用（如 Office）采用。 可以在虚拟机和物理 Windows 计算机上使用 .MSIX。 |
| 基础结构       | 需要 (服务器、客户端等的其他基础结构)  | 仅存储   |
| 管理       | 需要维护和更新   | 简化应用程序更新 |
| 用户体验      | 影响用户登录时间。 存在操作系统状态、应用状态和用户数据之间的边界。  | 提供的应用与本地安装的应用程序没有区别。 |

## <a name="next-steps"></a>后续步骤

若要了解有关 .MSIX 应用附加的详细信息，请查看我们的 [术语表](app-attach-glossary.md) 和 [常见问题解答](app-attach-faq.md)。 否则，开始 [安装应用程序](app-attach.md)。
