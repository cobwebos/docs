---
title: 渲染应用程序 - Azure Batch
description: 预安装的 Batch 渲染应用程序
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 30a365b36645dfe79f35b4bb889c0a06535a4c73
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212726"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>在渲染 VM 映像上预安装的应用程序

可以将任何渲染应用程序与 Azure Batch 配合使用。 不过，常见的预安装应用程序都提供了 Azure 市场 VM 映像。

在适用的情况下，可以为预安装的应用程序使用按使用付费许可。 创建 Batch 池时，可以指定必需的应用程序，将按分钟对 VM 和应用程序计费。 [Azure Batch 定价页面](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)上列出了应用程序价格。

某些应用程序仅支持 Windows，但大多数应用程序在 Windows 和 Linux 上都受支持。

## <a name="applications-on-centos-7-rendering-images"></a>CentOS 7 上的应用程序（渲染图像）

以下列表适用于 CentOS 7.6，版本1.1.6 呈现图像。

* Autodesk Maya I/O 2017 更新 5 (cut 201708032230)
* Autodesk Maya I/O 2018 更新 2 (cut 201711281015)
* Autodesk Maya i/o 2019 Update 1
* Autodesk Arnold for Maya 2017 （Arnold version 5.3.1.1） MtoA-3.2.1.1-2017
* Autodesk Arnold for Maya 2018 （Arnold version 5.3.1.1） MtoA-3.2.1.1-2018
* Autodesk Arnold for Maya 2019 （Arnold version 5.3.1.1） MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017（版本 3.60.04）
* Chaos Group V-Ray for Maya 2018（版本 3.60.04）
* Blender (2.68)
* Blender （2.8）

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>最新的 Windows Server 2016 上的应用程序（渲染图像）

以下列表适用于 Windows Server 2016，版本1.3.8 呈现图像。

* Autodesk Maya I/O 2017 更新 5（版本 17.4.5459）
* Autodesk Maya i/o 2018 Update 6 （版本18.4.0.7622）
* Autodesk Maya i/o 2019
* Autodesk 3ds Max I/O 2018 更新 4（版本 20.4.0.4254）
* Autodesk 3ds Max I/O 2019 更新 1（版本 21.2.0.2219）
* Autodesk 3ds Max i/o 2020 Update 2
* Autodesk Arnold for Maya 2017 （Arnold version 5.3.0.2） MtoA-3.2.0.2-2017
* Autodesk Arnold for Maya 2018 （Arnold version 5.3.0.2） MtoA-3.2.0.2-2018
* Autodesk Arnold for Maya 2019 （Arnold version 5.3.0.2） MtoA-3.2.0.2-2019
* Autodesk Arnold 3ds Max 2018 （Arnold 版本5.3.0.2）（版本1.2.926）
* Autodesk Arnold 3ds Max 2019 （Arnold 版本5.3.0.2）（版本1.2.926）
* Autodesk Arnold 3ds Max 2020 （Arnold 版本5.3.0.2）（版本1.2.926）
* Maya 2017 （版本4.12.01）的混乱组 V-Ray
* Maya 2018 （版本4.12.01）的混乱组 V-Ray
* Maya 2019 （版本4.04.03）的混乱组 V-Ray
* 3ds Max 2018 （版本4.20.01）的混乱组 V-射线
* 3ds Max 2019 （版本4.20.01）的混乱组 V-射线
* 3ds Max 2020 （版本4.20.01）的混乱组 V-射线
* Blender (2.79)
* Blender （2.80）
* AZ 10

> [!IMPORTANT]
> 若要在[Azure Batch 扩展模板](https://github.com/Azure/batch-extension-templates)之外运行具有 Maya 的 V-Ray， `vrayses.exe`请在运行该呈现之前开始。 若要在模板之外启动 vrayses，可以使用以下命令`%MAYA_2017%\vray\bin\vrayses.exe"`。
>
> 有关示例，请参阅 GitHub 上的[Maya 和上端 Ray 模板](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json)的启动任务。

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>以前的 Windows Server 2016 上的应用程序（渲染图像）

以下列表适用于 Windows Server 2016，版本1.3.7 呈现图像。

* Autodesk Maya I/O 2017 更新 5（版本 17.4.5459）
* Autodesk Maya I/O 2018 更新 4（版本 18.4.0.7622）
* Autodesk 3ds Max I/O 2019 更新 1（版本 21.2.0.2219）
* Autodesk 3ds Max I/O 2018 更新 4（版本 20.4.0.4254）
* Autodesk Arnold for Maya 2017（Arnold 版本 5.2.0.1）MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018（Arnold 版本 5.2.0.1）MtoA-3.1.0.1-2018
* Autodesk Arnold 3ds Max 2018 （Arnold 版本5.0.2.4）（版本1.2.926）
* Autodesk Arnold 3ds Max 2019 （Arnold 版本5.0.2.4）（版本1.2.926）
* Chaos Group V-Ray for Maya 2018（版本 3.52.03）
* Chaos Group V-Ray for 3ds Max 2018（版本 3.60.02）
* Chaos Group V-Ray for Maya 2019（版本 3.52.03）
* Chaos Group V-Ray for 3ds Max 2019（版本 4.10.01）
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray for 3ds Max 2019（版本 4.10.01）引入了对 V-ray 的中断性变更。 若要使用以前版本（版本 3.60.02），请使用 Windows Server 2016 版本 1.3.2（渲染节点）。

## <a name="next-steps"></a>后续步骤

若要使用渲染 VM 映像，需要在创建池时在池配置中指定它们；请参阅 [Batch 池渲染功能](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)。
