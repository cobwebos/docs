---
title: 部署 Windows 7 虚拟机 Windows 虚拟桌面-Azure
description: 如何在 Windows 虚拟机上配置和部署 Windows 7 虚拟机。
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0dcf21190b52f966dafb9caa9ae28fdf9b99ba86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88007567"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>在 Windows 虚拟桌面上部署 Windows 7 虚拟机

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)。

在 Windows 虚拟机上部署 Windows 7 虚拟机 (VM) 的过程与运行更高版本 Windows 的 Vm 略有不同。 本指南将介绍如何部署 Windows 7。

## <a name="prerequisites"></a>必备条件

在开始之前，请按照 [使用 PowerShell 创建主机池](create-host-pools-powershell.md) 中的说明创建主机池。 如果使用的是门户，请按照 [使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)的步骤1到步骤9中的说明进行操作。 在此之后，请选择 " **查看 + 创建** " 创建空的主机池。

## <a name="configure-a-windows-7-virtual-machine"></a>配置 Windows 7 虚拟机

完成先决条件后，即可在 Windows 虚拟桌面上配置 Windows 7 VM 以进行部署。

若要在 Windows 虚拟桌面上设置 Windows 7 VM：

1. 登录到 Azure 门户，搜索 Windows 7 企业版映像或上传自己的自定义 Windows 7 Enterprise (x64) 映像。
2. 将一个或多个虚拟机部署为 Windows 7 Enterprise 作为其主机操作系统。 请确保虚拟机允许远程桌面协议 (RDP)  (TCP/3389 端口) 。
3. 使用 RDP 连接到 Windows 7 企业主机，并使用配置部署时定义的凭据进行身份验证。
4. 将使用 RDP 连接到主机时使用的帐户添加到 "远程桌面用户" 组。 如果未添加该帐户，则在将其加入到 Active Directory 域后，可能无法连接到该 VM。
5. 请在 VM 上中转到 Windows 更新。
6. 安装 "重要" 类别中的所有 Windows 更新。
7. 在可选类别中安装所有 Windows 更新 (排除语言包) 。 此过程将安装完成这些说明所需的远程桌面协议8.0 更新 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) 。
8. 打开本地组策略编辑器，导航到 "**计算机配置**"  >  **管理模板**  >  **Windows 组件**"  >  **远程桌面服务**  >  **远程桌面会话主机**  >  **远程会话环境**"。
9. 启用远程桌面协议8.0 策略。
10. 将此 VM 加入到 Active Directory 域。
11. 通过运行以下命令重新启动虚拟机：

     ```cmd
     shutdown /r /t 0
     ```

12. 按照 [此处](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo?view=azps-4.3.0) 的说明获取注册令牌。

      - 如果你想要使用 Azure 门户，则还可以前往要将 VM 添加到的主机池的 "概述" 页，并在其中创建一个令牌。

13. [下载适用于 windows 7 的 Windows 虚拟桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [下载适用于 windows 7 的 Windows 虚拟桌面代理程序管理器](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. 打开 Windows 虚拟桌面代理安装程序，然后按照说明进行操作。 出现提示时，请提供在步骤12中创建的注册密钥。
16. 打开 Windows 虚拟桌面代理程序管理器，然后按照说明进行操作。
17. 或者，阻止 TCP/3389 端口删除对 VM 的直接远程桌面协议访问权限。
18. （可选）确认 .NET framework 至少为版本4.7.2。 如果要创建自定义映像，则更新框架尤其重要。

## <a name="next-steps"></a>后续步骤

你的 Windows 虚拟桌面部署现已准备就绪，可供使用。 [下载最新版本的 Windows 虚拟桌面客户端](https://aka.ms/wvd/clients/windows) 以开始工作。

有关 windows 虚拟桌面上 Windows 7 的已知问题和疑难解答说明的列表，请参阅故障排除一文，了解 windows [虚拟机中的 windows 7 虚拟机疑难解答](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md)。
