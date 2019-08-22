---
title: 使用基于虚拟机的文件共享为主机池创建 FSLogix 配置文件容器-Azure
description: 如何使用基于虚拟机的文件共享为 Windows 虚拟桌面预览主机池设置 FSLogix 配置文件容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
ms.openlocfilehash: cf3d682e4d0c68822267a4e63846d80b632cbdcc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876793"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>使用文件共享为主机池创建配置文件容器

Windows 虚拟桌面预览版服务将 FSLogix 配置文件容器作为建议的用户配置文件解决方案提供。 我们不建议使用用户配置文件磁盘 (UPD) 解决方案, 该解决方案将在未来版本的 Windows 虚拟桌面中弃用。

本文将介绍如何使用基于虚拟机的文件共享为主机池设置 FSLogix 配置文件容器共享。 有关更多 FSLogix 文档, 请参阅[FSLogix 站点](https://docs.fslogix.com/)。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>创建将用作文件共享的新虚拟机

创建虚拟机时, 请确保将该虚拟机放置在与主机池虚拟机相同的虚拟网络中, 或者放置到与主机池虚拟机连接的虚拟网络上。 可以通过多种方式创建虚拟机:

- [从 Azure 库映像创建虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [从托管映像创建虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [从非托管映像创建虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

创建虚拟机后, 通过执行以下操作将其加入域:

1. 用在创建虚拟机时提供的凭据[连接到虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)。
2. 在虚拟机上, 启动 "**控制面板"** , 然后选择 "**系统**"。
3. 选择 "**计算机名称**", 选择 "**更改设置**", 然后选择 "**更改 ...** "
4. 选择 "**域**", 然后在虚拟网络上输入 Active Directory 域。
5. 使用有权加入域的域帐户进行身份验证。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>准备虚拟机以用作用户配置文件的文件共享

下面是有关如何准备虚拟机以用作用户配置文件的文件共享的一般说明:

1. 将 Windows 虚拟桌面 Active Directory 用户添加到[Active Directory 安全组](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups)。 此安全组将用于向你刚刚创建的文件共享虚拟机验证 Windows 虚拟桌面用户的身份。
2. [连接到文件共享虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)。
3. 在文件共享虚拟机上, 在将用作配置文件共享的**C 驱动器**上创建一个文件夹。
4. 右键单击新文件夹, 选择 "**属性**", 选择 "**共享**", 然后选择 "**高级共享 ...** "。
5. 选择 "**共享此文件夹**", 选择 "**权限 ...** ", 然后选择 "**添加 ...** "。
6. 搜索向其中添加了 Windows 虚拟桌面用户的安全组, 并确保该组具有 "**完全控制**"。
7. 添加安全组后, 右键单击该文件夹, 选择 "**属性**", 选择 "**共享**", 然后向下复制要用于稍后的**网络路径**。

有关权限的详细信息, 请参阅[FSLogix 文档](https://docs.microsoft.com/fslogix/fslogix-storage-config-ht)。

## <a name="configure-the-fslogix-profile-container"></a>配置 FSLogix 配置文件容器

若要配置具有 FSLogix 软件的虚拟机, 请在注册到主机池的每个计算机上执行以下操作:

1. 用在创建虚拟机时提供的凭据[连接到虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)。
2. 启动 internet 浏览器并导航到[此链接](https://go.microsoft.com/fwlink/?linkid=2084562), 下载 FSLogix 代理。
3. 在 .zip 文件\\中导航到\\\\ \\Win32 版本或\\X64\\版本, 并运行**FSLogixAppsSetup**以安装 FSLogix 代理。  若要了解有关如何安装 FSLogix 的详细信息, 请参阅[下载并安装 FSLogix](https://docs.microsoft.com/fslogix/install-ht)。
4. 导航到**Program Files** > **FSLogix** > **Apps**以确认已安装代理。
5. 从 "开始" 菜单中, 以管理员身份运行**RegEdit** 。 导航到 **"\\计算机\\HKEY_LOCAL_MACHINE\\software FSLogix**"。
6. 创建名为 "**配置文件**" 的密钥。
7. 为配置文件密钥创建以下值:

| 姓名                | 类型               | 数据/值                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | 多字符串值 | "文件共享的网络路径"     |

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。