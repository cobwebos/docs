---
title: 在 Azure 开发测试实验室中连接到 Windows 虚拟机
description: 了解如何在实验室（Azure 开发测试实验室）中连接到 Windows 虚拟机
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540645"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>在实验室中连接到 Windows VM （Azure 开发测试实验室）
本文介绍如何在实验室中连接到 Windows VM。 

## <a name="connect-to-a-windows-vm"></a>连接到 Windows VM
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，搜索并选择 "**开发测试实验室**"。 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="搜索并选择 开发测试实验室":::    
1. 从实验室列表中，选择**实验室**。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="选择实验室":::            
1. 在实验室的主页上，从 "**我的虚拟机**" 列表中选择 Windows VM。 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="选择 Windows VM":::                
1. 在 VM 的 "**虚拟机**" 页上，选择工具栏上的 "**连接**"。 如果 VM 已停止，请选择 "**启动**" 以启动 vm。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="在工具栏上选择 连接":::                    
1. 如果使用的是 Edge 浏览器，则会看到指向浏览器底部**下载的 RDP 文件**的链接。 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="已下载 RDP":::                        
1. 打开 RDP 文件，输入创建 VM 时键入的 VM 凭据。 现在应已连接到 Windows VM。 

## <a name="next-steps"></a>后续步骤
[如何：连接到 Linux VM](connect-linux-virtual-machine.md)
