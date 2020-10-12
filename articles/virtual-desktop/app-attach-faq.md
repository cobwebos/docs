---
title: Windows 虚拟桌面 .MSIX 应用附加常见问题解答-Azure
description: 有关 Windows 虚拟桌面的 .MSIX 应用附加的常见问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556216"
---
# <a name="msix-app-attach-faq"></a>.MSIX 应用附加常见问题解答

本文解答了有关 Windows 虚拟桌面的 .MSIX 应用附加的常见问题。

## <a name="does-msix-app-attach-use-fslogix"></a>.MSIX 应用附加是否使用 FSLogix？

.MSIX 应用附加不使用 FSLogix。 但是，应用附加和 FSLogix 旨在提供无缝的用户体验。

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>能否在 Windows 虚拟桌面之外使用 .MSIX 应用连接？

是的，.MSIX 应用附加功能是 Windows 10 企业版随附的一项功能，可在 Windows 虚拟桌面之外使用。 但是，在 Windows 虚拟桌面外，.MSIX 应用附加的管理平面不存在。

## <a name="how-do-i-get-an-msix-package"></a>如何实现获取 .MSIX 包？

你的软件供应商将为你提供 .MSIX 包。 还可以将非 .MSIX 包转换为 .MSIX。 详细了解 [如何将现有安装程序移动到 .msix](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)。

## <a name="which-operating-systems-support-msix-app-attach"></a>哪些操作系统支持 .MSIX 应用附件？

Windows 10 企业版和 Windows 10 企业多会话。

## <a name="next-steps"></a>后续步骤

若要了解有关 .MSIX 应用附加的详细信息，请查看我们的[概述](what-is-app-attach.md)[词汇表](app-attach-glossary.md)。 否则，开始 [安装应用程序](app-attach.md)。