---
title: "在 Azure 上预配地理人工智能虚拟机 - Azure | Microsoft Docs"
description: "如何预配 Azure 上的地理 AI 虚拟机。"
keywords: "深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析"
services: machine-learning
documentationcenter: 
author: gopitk
manager: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 2994ef858e960640d98ab2f7d02a401b11aa9e8f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>在 Azure 上预配地理人工智能虚拟机 

地理 AI 数据科学虚拟机 (Geo-DSVM) 是流行的 [Azure 数据科学虚拟机](http://aka.ms/dsvm)的一个专门配置的扩展，它将 AI 和地理空间分析组合在一起。 VM 中的地理空间分析由 [ArcGIS Pro](https://www.arcgis.com/features/index.html) 提供支持。 数据科学 VM 能够基于增加了地理空间信息的数据快速训练机器学习模型甚至是深度学习模型。 它仅在 Windows 2016 DSVM 上受支持。 

Geo-DSVM 包含用于人工智能的多个工具，包括：

- 流行的深度学习框架的 GPU 版本，例如 Microsoft Cognitive Toolkit、TensorFlow、Keras、Caffe2、Chainer； 
- 用来获取和预处理图像、文本数据的工具， 
- 用于开发活动的工具，例如 Microsoft R Server 开发人员版、Anaconda Python、用于 Python 和 R 的 Jupyter Notebook、用于 Python 和 R 的 IDE、SQL 数据库
- ESRI 的 ArcGIS Pro 桌面软件以及可以处理来自人工智能应用程序的地理空间数据的 Python 和 R 接口。 


## <a name="create-your-geo-ai-data-science-vm"></a>创建地理 AI 数据科学 VM

下面是创建地理 AI 数据科学 VM 实例的过程： 


1. 在 [Azure 门户](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)中导航到虚拟机列表。
2. 选择底部的“创建”按钮来进入向导。
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. 用于创建 Geo-DSVM 的向导要求针对上图右侧列举的每个步骤（共有**四个步骤**）提供**输入**。 以下是配置每个步骤所需的输入：



   - **基础知识**

      1. **名称**：要创建的数据科学服务器的名称。

      2. **用户名**：管理员帐户登录 ID。

      3. **密码**：管理员帐户密码。

      4. **订阅**：如果有多个订阅，请选择要在其上创建虚拟机并对其计费的订阅。

      5. **资源组**：可以创建一个新资源组，也可以使用订阅中现有的**空** Azure 资源组。

      6. **位置**：选择最适合的数据中心。 通常，最合适的数据中心应拥有大部分数据，或者最接近实际位置以实现最快的网络访问。 如果需要在 GPU 上进行深度学习，必须选择 Azure 中包含 NC 系列 GPU VM 实例的某个位置。 目前具有 GPU VM 的位置包括：**美国东部、美国中北部、美国中南部、美国西部 2、北欧、西欧**。 有关最新列表，请查看[“Azure 产品(按区域)”页](https://azure.microsoft.com/regions/services/)并在“计算”下查找“NC 系列”。 


   - **设置**：如果计划在地理 DSVM 上的 GPU 上运行深度学习，请选择 NC 系列 GPU 虚拟机大小之一。 否则，可以选择基于 CPU 的实例之一。  为 VM 创建存储帐户。 
   
   - **摘要**：检查输入的所有信息是否正确。

   - **购买**：单击“购买”开始预配。 此时会显示服务条款的链接。 除计算**大小**步骤中选择的服务器大小所产生的费用外，VM 没有任何其他费用。 

>[!NOTE]
> 预配大约需要 20 到 30 分钟。 预配的状态在 Azure 门户上显示。


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>如何访问地理 AI 数据科学虚拟机

创建 VM 后，便可以开始使用在其上安装并预配置的工具。 许多工具带有开始菜单磁贴和桌面图标。 可以使用在前面的**基本信息**部分中配置的管理员帐户凭据从远程桌面登录 VM。 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>使用 VM 中安装的 ArcGIS Pro

Geo-DSVM 已经预先安装了 ArcGIS Pro 桌面并且预配置了环境来使用 DSVM 中的所有工具。 启动 ArcGIS 时，它会提示你登录到 ArcGIS 帐户。 如果你已有 ArcGIS 帐户并且有该软件的许可证，则可以使用现有凭据。  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

否则，可以注册新的 ArcGIS 帐户和许可证或者获取[免费试用版](https://www.arcgis.com/features/free-trial.html)。 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

在注册付费或免费试用版 ArcGIS 帐户后，可以根据 [ArcGIS Pro 入门文档](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)中的说明授权你的帐户使用 ArcGIS Pro。 

使用 ArcGIS 帐户登录到 ArcGIS Pro 桌面后，可以开始将 VM 上安装并配置的数据科学工具用于地理空间分析和机器学习项目。

## <a name="next-steps"></a>后续步骤

根据下列主题中的指南开始使用地理 AI 数据科学 VM：

* [使用地理 AI 数据科学 VM](use-geo-ai-dsvm.md)