---
title: 用于在 SAP HANA （大型实例）中启用 Kdump 的脚本 |Microsoft Docs
description: 用于在 SAP HANA （大型实例）中启用 Kdump 的脚本，B-HLI 类型 I，B-HLI 类型 II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204931"
---
# <a name="enable-kdump-service"></a>启用 Kdump 服务

本文档介绍有关如何在 Azure HANA 大型实例上启用 Kdump 服务的详细信息（**类型 I 和键入 II**）

## <a name="supported-skus"></a>支持的 SKU

|  Hana 大型实例类型   |  操作系统供应商   |  OS 包版本   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   键入 I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   类型 II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   类型 II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   类型 II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   类型 II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>必备条件

- Kdump 服务使用`/var/crash`目录写入转储，请确保该分区对应于此目录有足够的空间来容纳转储。

## <a name="setup-details"></a>设置详细信息

- 可在[此处](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)找到用于启用 Kdump 的脚本

- 使用以下命令在 HANA 大型实例上运行此脚本

    > [!NOTE]
    > 运行此命令所需的 sudo 权限。

    ```bash
    sudo bash enable-kdump.sh
    ```

- 如果成功启用了命令输出 Kdump，请重新启动系统以应用更改，然后成功启用 Kdump。 重新启动系统以应用更改。

- 如果命令输出无法执行特定操作，请退出!!!!,，则不启用 Kdump 服务。 请参阅[支持问题](#support-issue)部分。

## <a name="test-kdump"></a>测试 Kdump

> [!NOTE]
>  以下操作将触发内核崩溃和系统重新启动。

- 触发内核故障

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- 系统成功重新启动后，检查内核`/var/crash`故障日志的目录。

- 如果`/var/crash`具有具有当前日期的目录，则 Kdump 已成功启用。

## <a name="support-issue"></a>支持问题

如果脚本失败并出现错误或未启用 Kdump，请向 Microsoft 支持团队上报服务请求，并提供以下详细信息。

* B-HLI 订阅 ID

* 服务器名称

* 操作系统供应商

* OS 版本

* 内核版本
