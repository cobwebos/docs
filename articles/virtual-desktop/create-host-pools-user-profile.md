---
title: Windows 虚拟桌面预览主机池-Azure 将用户配置文件共享设置
description: 如何设置 Windows 虚拟桌面预览主机池 FSLogix 配置文件容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: c9c2ca2cc27c5fa757b8ff6846e0a6a8f7087875
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403708"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>为主机池设置用户配置文件共享

Windows 虚拟桌面预览服务提供与建议的用户配置文件解决方案 FSLogix 配置文件的容器。 我们不建议使用用户配置文件磁盘 (UPD) 解决方案，并将 Windows 虚拟桌面的未来版本中弃用。

本部分将介绍如何设置主机池 FSLogix 配置文件容器共享。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>创建新的虚拟机将充当文件共享

创建虚拟机时，请务必将其放置在同一个虚拟网络作为托管池虚拟机上或已连接到托管池虚拟机的虚拟网络上。 可以通过多种方式来创建虚拟机：

- [从 Azure 库映像创建的虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [从托管映像创建虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [从非托管映像创建虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

创建虚拟机后，将其加入到域通过执行以下操作：

1. [连接到虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)时创建虚拟机时提供的凭据。
2. 虚拟机上启动**Control Panel** ，然后选择**系统**。
3. 选择**计算机名**，选择**更改设置**，然后选择**更改...**
4. 选择**域**，然后输入虚拟网络上的 Active Directory 域。
5. 使用有权限将计算机加入域的域帐户进行身份验证。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>准备虚拟机，使其作为用户配置文件的文件共享

以下是有关如何准备虚拟机的一般说明，使其作为用户配置文件的文件共享：

1. 加入到的会话主机虚拟机[Active Directory 安全组](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups)。 此安全组将用于进行身份验证到刚创建文件共享虚拟机的会话主机虚拟机。
2. [连接到文件共享虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)。
3. 在文件共享虚拟机上创建一个文件夹**C 驱动器**要使用的配置文件共享。
4. 右键单击新文件夹，选择**属性**，选择**共享**，然后选择**高级共享...**.
5. 选择**共享此文件夹**，选择**权限...**，然后选择**添加...**.
6. 搜索安全组添加会话主机虚拟机，则请确保该组具有**完全控制**。
7. 添加后的安全组，右键单击该文件夹中，选择**属性**，选择**共享**，然后向下复制**网络路径**以供稍后使用。

有关权限的最佳实践，请参阅以下[FSLogix 文档](https://support.fslogix.com/index.php/forum-main/faqs/84-best-practices#120)。

## <a name="configure-the-fslogix-profile-container"></a>配置 FSLogix 配置文件容器

若要配置 FSLogix software 虚拟机，请执行以下操作注册到主机池每台计算机上：

1. [连接到虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)时创建虚拟机时提供的凭据。
2. 启动 internet 浏览器并导航到以下[链接](https://go.microsoft.com/fwlink/?linkid=2084562)下载 FSLogix 代理。 作为 Windows 虚拟桌面公共预览版的一部分，你将获得许可证密钥以激活 FSLogix 软件。 该密钥是 FSLogix 代理.zip 文件中包含的 LicenseKey.txt 文件。
3. 安装 FSLogix 代理。
4. 导航到**Program Files** > **FSLogix** > **应用**以确认安装了代理。
5. 从开始菜单中，运行**RegEdit**以管理员身份。 导航到**计算机\\HKEY_LOCAL_MACHINE\\软件\\FSLogix\\配置文件**
6. 创建以下值：

| 名称                | Type               | 数据/值                        |
|---------------------|--------------------|-----------------------------------|
| 已启用             | DWORD              | 第                                 |
| VHDLocations        | 多字符串值 | "网络文件共享路径" |
| VolumeType          | String             | VHDX                              |
| SizeInMBs           | DWORD              | "配置文件的大小的整数"     |
| IsDynamic           | DWORD              | 第                                 |
| LockedRetryCount    | DWORD              | 第                                 |
| LockedRetryInterval | DWORD              | 0                                 |
