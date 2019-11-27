---
title: 支持的操作系统、容器引擎 - Azure IoT Edge | Microsoft Docs
description: 了解可以运行 Azure IoT Edge 守护程序和运行时的操作系统以及生产设备支持的容器引擎
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0fe4a13a33b6d93266d68e632864e0b61a7eaf29
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452524"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 支持的系统

本文详述 IoT Edge（不管是正式版还是预览版）支持的系统和组件。 

如果在使用 Azure IoT Edge 服务时遇到问题，可以通过多种方式来寻求支持。 请尝试通过以下某个渠道来寻求支持：

**报告 bug** – 涉及 Azure IoT Edge 产品的大多数开发都是在 IoT Edge 开放源代码项目中进行的。 可以在项目的[问题页面](https://github.com/azure/iotedge/issues)上报告 bug。 修复很快就会从项目实施到产品更新中。

**Microsoft 客户支持团队** – 拥有[支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)创建支持票证来与 Microsoft 客户支持团队进行沟通。

**功能请求** – Azure IoT Edge 产品通过产品的 [User Voice 页面](https://feedback.azure.com/forums/907045-azure-iot-edge)跟踪功能请求。

## <a name="container-engines"></a>容器引擎

Azure IoT Edge 模块是以容器形式实现的，因此 IoT Edge 需要容器引擎来启动这些模块。 Microsoft 提供了容器引擎 moby-engine 来满足此要求。 该容器引擎基于 Moby 开源项目。 Docker CE 和 Docker EE 是其他常用的容器引擎。 它们也基于 Moby 开放源代码项目并且与 Azure IoT Edge 兼容。 Microsoft 对使用那些容器引擎的系统提供尽力而为的支持；但是，Microsoft 不能为其中的问题提供修复。 因此，Microsoft 建议在生产系统上使用 moby-engine。

<br>
<center>

![Moby 用作容器运行时](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>操作系统
Azure IoT Edge 在可以运行容器的大多数操作系统上运行，但并非同等程度地支持所有这些系统。 操作系统分组为各个层级，这些层级表示用户可以预期的支持级别。
* 支持第 1 层系统。 对于第 1 层系统，Microsoft 会采取以下措施：
    * 将此操作系统包括在自动化测试中
    * 为它们提供安装程序包
* 第 2 层系统与 Azure IoT Edge 兼容并且可以相对容易地使用。 对于第 2 层系统：
    * Microsoft 已在这些平台上进行了特别的测试，或者知道合作伙伴已成功在平台上运行 Azure IoT Edge
    * 适用于其他平台的安装程序包在这些平台上可能会正常工作
    
主机 OS 系列必须始终与模块容器内部使用的来宾 OS 系列相匹配。 换言之，在 Linux 上只能使用 Linux 容器，在 Windows 上只能使用 Windows 容器。 使用 Windows 时，仅支持处理独立容器，而不支持 Hyper-v 隔离容器。  

<br>
<center>

![主机 OS 与来宾 OS 相匹配](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>第 1 层

下表中列出的系统（不管是公开发布版还是公共预览版）受 Microsoft 的支持，并在每个新版本中进行了测试。 

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 公共预览版  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 公共预览版 |
| [Windows 10 IoT 核心版](https://docs.microsoft.com/windows/iot-core/windows-iot-core)内部版本 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT 企业版](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise)内部版本 17763 | ![Windows 10 IoT 企业版 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19) 内部版本 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server) 内部版本 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


在 Windows 上运行 Windows 容器的设备必须使用上面列出的 Windows 操作系统，这时候唯一支持的适用于生产的配置。 适用于 Windows 的 Azure IoT Edge 安装包允许在 Windows 上使用 Linux 容器；但是，只能将此配置用于开发和测试。 有关详细信息，请参阅[使用 Windows 上的 IoT Edge 运行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

### <a name="tier-2"></a>第 2 层

下表中列出的系统被视为兼容 Azure IoT Edge，但未由 Microsoft 进行积极测试或维护。

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 系统（包括 Raspian Buster）使用了 IoT Edge 不支持的 OpenSSL 版本。 请使用以下命令安装一个早期版本，然后再安装 IoT Edge： 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>虚拟机
Azure IoT Edge 可在虚拟机中运行。 当客户想要利用边缘智能增强现有的基础结构时，经常会使用虚拟机作为 IoT Edge 设备。 主机 VM OS 系列必须与模块容器内部使用的来宾 OS 系列相匹配。 直接在设备上运行 Azure IoT Edge 时，同样要满足此要求。 Azure IoT Edge不区分底层虚拟化技术，将在以 Hyper-V 和 vSphere 等平台为后盾的 VM 中工作。

<br>
<center>

![VM 中的 Azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>最低系统要求
在 Raspberry Pi3 等小型设备以及服务器级硬件上，Azure IoT Edge 都能非常顺利地运行。 哪种硬件适合方案取决于想要运行的工作负荷。 在设备方面做出最终决策可能比较复杂；但是，可以在传统的便携式计算机或台式机上轻松开始设计解决方案的原型。

在设计原型期间获得的经验可以帮助你在设备方面做出最终的选择。 应考虑的问题包括： 

* 工作负荷中有多少模块？
* 模块的容器共享多少层？
* 模块以什么语言编写？ 
* 模块将处理多少数据？
* 模块是否需要使用任何专用硬件来加速其工作负荷？
* 所需的解决方案性能特点有哪些？
* 硬件预算是多少？
