---
title: 支持的操作系统，容器引擎-Azure IoT Edge
description: 了解可以运行 Azure IoT Edge 守护程序和运行时的操作系统以及生产设备支持的容器引擎
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f1f4efc13e2d11e7111264564a227a484d611d5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284806"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 支持的系统

本文提供了有关 IoT Edge 支持的系统和组件的详细信息，无论是正式还是预览版。

如果使用 Azure IoT Edge 服务时遇到问题，可以通过多种方式寻求支持。 尝试以下支持服务之一：

**报告 bug** – 涉及 Azure IoT Edge 产品的大多数开发都是在 IoT Edge 开放源代码项目中进行的。 可以在项目的[问题页面](https://github.com/azure/iotedge/issues)上报告 bug。 修复很快就会从项目实施到产品更新中。

**Microsoft 客户支持团队** – 拥有[支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)创建支持票证来与 Microsoft 客户支持团队进行沟通。

**功能请求** – Azure IoT Edge 产品通过产品的 [User Voice 页面](https://feedback.azure.com/forums/907045-azure-iot-edge)跟踪功能请求。

## <a name="container-engines"></a>容器引擎

Azure IoT Edge 模块作为容器实现，因此 IoT Edge 需要一个容器引擎来启动它们。 Microsoft 提供了容器引擎 moby-engine 来满足此要求。 此容器引擎基于小鲸鱼开源项目。 Docker CE 和 Docker EE 是其他常用的容器引擎。 它们还基于小鲸鱼开源项目，并与 Azure IoT Edge 兼容。 Microsoft 为使用这些容器引擎的系统提供最大的支持。但是，Microsoft 不会为他们提供问题的修补程序。 因此，Microsoft 建议在生产系统上使用 moby-engine。

<br>
<center>

![作为容器运行时](./media/support/only-moby-for-production.png)
的小鲸鱼引擎 </center>

## <a name="operating-systems"></a>操作系统

在可运行容器的大多数操作系统上运行 Azure IoT Edge;但是，并非所有这些系统都同样受支持。 操作系统分组为各个层级，这些层级表示用户可以预期的支持级别。

* 支持第1层系统。 对于第1层系统，Microsoft：
  * 将此操作系统包括在自动化测试中
  * 为它们提供安装程序包
* 第2层系统与 Azure IoT Edge 兼容，可以相对较容易地使用。 对于第2层系统：
  * Microsoft 在平台上进行了非正式测试，或了解到在平台上成功运行 Azure IoT Edge 的合作伙伴
  * 适用于其他平台的安装程序包在这些平台上可能会正常工作

主机 OS 系列必须始终与模块容器内部使用的来宾 OS 系列相匹配。 换言之，在 Linux 上只能使用 Linux 容器，在 Windows 上只能使用 Windows 容器。 使用 Windows 时，仅支持处理独立容器，而不支持 Hyper-v 隔离容器。  

<br>
<center>

![主机 OS 匹配来宾 OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>第 1 层

Microsoft 支持下表中列出的系统，该系统公开发布或公开预览版，并通过每个新版本进行测试。 

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 公共预览版  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 公共预览版 |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core)，版本17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT 企业版](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise)，版本17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19)，版本17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server)，版本17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

上面列出的 Windows 操作系统是在 Windows 上运行 Windows 容器的设备的要求，这是唯一受支持的生产配置。 适用于 Windows 的 Azure IoT Edge 安装包允许在 Windows 上使用 Linux 容器;但是，此配置仅用于开发和测试。 有关详细信息，请参阅[使用 Windows 上的 IoT Edge 运行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

### <a name="tier-2"></a>第 2 层

下表中列出的系统被视为与 Azure IoT Edge 兼容，但 Microsoft 不会对其进行主动测试或维护。

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7。5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [导师 Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![导师 Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![导师 Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![导师 Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [导师 Embedded Linux 全操作系统](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![导师 Embedded Linux 全操作系统 + AMD64](./media/tutorial-c-module/green-check.png) |  | ![导师 Embedded Linux 全操作系统 + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7。5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [风河8](https://docs.windriver.com/category/os-wind_river_linux) | ![风河 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 个系统，包括 Raspian Buster，请使用 IoT Edge 不支持的 OpenSSL 版本。 在安装 IoT Edge 之前，请使用以下命令安装早期版本：

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>虚拟机

Azure IoT Edge 可在虚拟机中运行。 如果客户想要使用边缘智能增强现有的基础结构，则使用虚拟机作为 IoT Edge 设备很常见。 主机 VM OS 系列必须与模块容器内部使用的来宾 OS 系列相匹配。 此要求与直接在设备上运行 Azure IoT Edge 时相同。 Azure IoT Edge不区分底层虚拟化技术，将在以 Hyper-V 和 vSphere 等平台为后盾的 VM 中工作。

<br>
<center>

VM](./media/support/edge-on-vm.png)
中 Azure IoT Edge ![</center>

## <a name="minimum-system-requirements"></a>最低系统要求

在 Raspberry Pi3 等小型设备以及服务器级硬件上，Azure IoT Edge 都能非常顺利地运行。 为方案选择适当的硬件取决于要运行的工作负荷。 在设备方面做出最终决策可能比较复杂；但是，可以在传统的便携式计算机或台式机上轻松开始设计解决方案的原型。

在设计原型期间获得的经验可以帮助你在设备方面做出最终的选择。 应考虑的问题包括：

* 工作负荷中有多少个模块？
* 模块容器共享了多少层？
* 你的模块的编写语言是什么？
* 模块将处理多少数据？
* 模块是否需要任何专用硬件来加速其工作负荷？
* 你的解决方案所需的性能特征有哪些？
* 你的硬件预算是什么？
