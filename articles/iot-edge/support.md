---
title: 支持的操作系统、容器引擎 - Azure IoT Edge | Microsoft Docs
description: 了解可以运行 Azure IoT Edge 守护程序和运行时的操作系统以及生产设备支持的容器引擎
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6443260de0a8bd8531edb303fa581d281034fef3
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555602"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 支持的系统

有多种方法可用来寻求对 Azure IoT Edge 产品的支持。

**报告 bug** – 涉及 Azure IoT Edge 产品的大多数开发都是在 IoT Edge 开放源代码项目中进行的。 可以在项目的[问题页面](https://github.com/azure/iotedge/issues)上报告 bug。 修复很快就会从项目实施到产品更新中。

**Microsoft 客户支持团队** – 拥有[支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)创建支持票证来与 Microsoft 客户支持团队进行沟通。

**功能请求** – Azure IoT Edge 产品通过产品的 [User Voice 页面](https://feedback.azure.com/forums/907045-azure-iot-edge)跟踪功能请求。

## <a name="operating-systems"></a>操作系统
Azure IoT Edge 在可以运行容器的大多数操作系统上运行，但是，并非同等程度地支持所有这些操作系统。 操作系统分组为各个层级，这些层级表示用户可以预期的支持级别。

### <a name="tier-1"></a>第 1 层
可以将第 1 层系统视为受官方支持。 这意味着 Microsoft：
* 将这些操作系统包括在自动化测试中
* 为它们提供安装程序包

正式发布
| 操作系统 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | 否 | 是|
| Ubuntu Server 16.04 | 是 | 否 |
| Ubuntu Server 18.04 | 是 | 否 |

公共预览版
| 操作系统 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT 核心版 17763 | 是 | 否 |
| 用于 Windows 容器的 Windows 10 版本 17763<br><br>用于 Linux 容器的 Windows 10 版本 14393 或更新版本\* | 是 | 否 |
| 用于 Windows 容器的 Windows Server 2019<br><br>用于 Linux 容器的 Windows Server 2016 或更新版本\* | 是 | 否 |

\* Microsoft 为 Windows 设备上的 Linux 容器提供仅用于开发和测试的安装包。 该配置不支持在生产环境中使用。 

### <a name="tier-2"></a>第 2 层
第 2 层系统可视为与 Azure IoT Edge 兼容并且可以相对容易地使用。 这意味着：
* Microsoft 已在这些平台上进行了特别的测试，或者知道合作伙伴已成功在平台上运行 Azure IoT Edge
* 适用于其他平台的安装程序包在这些平台上可能会正常工作

| 操作系统 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | 是 | 是 |
| Debian 8 | 是 | 是 |
| Debian 9 | 是 | 是 |
| RHEL 7.5 | 是 | 是 |
| Ubuntu 18.04 | 是 | 是 |
| Ubuntu 16.04 | 是 | 是 |
| Wind River 8 | 是 | 否 |
| Yocto | 是 | 否 |

## <a name="container-engines"></a>容器引擎
Azure IoT Edge 需要一个容器引擎来启动模块，无论它运行于哪个操作系统上。 Microsoft 提供了容器引擎 moby-engine 来满足此要求。 它基于 Moby 开放源代码项目。 Docker CE 和 Docker EE 是其他常用的容器引擎。 它们也基于 Moby 开放源代码项目并且与 Azure IoT Edge 兼容。 Microsoft 对使用那些容器引擎的系统提供尽力而为的支持；但是，Microsoft 没有能力为其中的问题提供修复。 因此，Microsoft 建议在生产系统上使用 moby-engine。

