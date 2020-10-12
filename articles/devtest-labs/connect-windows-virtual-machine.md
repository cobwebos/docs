---
title: 在 Azure 开发测试实验室中连接到 Windows 虚拟机
description: '了解如何在 (Azure 开发测试实验室的实验室中连接到 Windows 虚拟机) '
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86540645"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a> (Azure 开发测试实验室连接到实验室中的 Windows VM) 
本文介绍如何在实验室中连接到 Windows VM。 

## <a name="connect-to-a-windows-vm"></a>连接到 Windows VM
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，搜索并选择 " **开发测试实验室**"。 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;":::    
1. 从实验室列表中，选择 **实验室**。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;" 列表中选择 Windows VM。 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;" 以启动 vm。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;":::                    
1. 如果使用的是 Edge 浏览器，则会看到指向浏览器底部 **下载的 RDP 文件** 的链接。 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;":::                        
1. 打开 RDP 文件，输入创建 VM 时键入的 VM 凭据。 现在应已连接到 Windows VM。 

## <a name="next-steps"></a>后续步骤
[如何：连接到 Linux VM](connect-linux-virtual-machine.md)
