---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269015"
---
虚拟网络中的 IaaS 虚拟机 (VM) 和 PaaS 角色实例将基于连接到的子网自动接收来自指定范围的专用 IP 地址。 该地址会被这些 VM 和角色实例保留，直到这些 VM 和角色实例被停用。 通过从 PowerShell、Azure CLI 或 Azure 门户停止 VM 或角色实例可将其停用。 在这些情况下，重新启动该 VM 或角色实例后，它将从 Azure 基础结构接收一个可用的 IP 地址，该 IP 地址可能与以前使用的 IP 地址不相同。 如果从来宾操作系统关闭 VM 或角色实例，该 VM 或角色实例将保留它的 IP 地址。  

在某些情况下（例如，如果 VM 要运行 DNS 或者将作为域控制器），会希望 VM 或角色实例具有静态 IP 地址。 可通过设置静态专用 IP 地址来实现此目的。

