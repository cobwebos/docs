---
title: 快速入门：创建 Geo AI Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: 在 Azure 上配置和创建地理 AI 数据科学虚拟机，用于进行地理空间分析和机器学习。
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77525883"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>快速入门：在 Azure 上设置地理人工智能虚拟机 

地理 AI 数据科学虚拟机 (Geo-DSVM) 是流行的 [Azure 数据科学虚拟机](https://aka.ms/dsvm)的一个专门配置的扩展，它将 AI 和地理空间分析组合在一起。 VM 中的地理空间分析由 [ArcGIS Pro](https://www.arcgis.com/features/index.html) 提供支持。 Data Science Virtual Machine (DSVM) 能够快速训练机器学习模型，甚至深度学习模型。 为了开发这些模型，它将使用以地理信息扩充的数据。 Geo-DSVM 仅在 Windows 2016 DSVM 上受支持。 

Geo-DSVM 中的 AI 工具包括：

- 流行的深度学习框架的 GPU 版本，例如 Microsoft Cognitive Toolkit、TensorFlow、Keras、Caffe2 和 Chainer
- 用来获取和预处理图像与文本数据的工具
- 用于开发活动的工具，例如 Microsoft Machine Learning Server Developer Edition、Anaconda Python、用于 Python 和 R 的 Jupyter Notebook、用于 Python 和 R 的 IDE，以及 SQL 数据库
- ESRI 的 ArcGIS Pro 桌面软件以及可以处理来自人工智能应用程序的地理空间数据的 Python 和 R 接口
 

## <a name="create-your-geo-ai-data-science-vm"></a>创建地理 AI 数据科学 VM

若要创建 Geo AI Data Science VM 的实例，请执行以下步骤：

1. 转到 [Azure 门户](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)上的虚拟机列表。
1. 选择底部的“创建”以生成一个向导： 

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. 该向导要求为每个步骤（共四个步骤）提供输入。 有关这些输入的详细信息，请参阅以下部分。

### <a name="wizard-details"></a>向导详细信息 ###

**基本信息**：

- **Name**：要创建的数据科学服务器的名称。
    
- **用户名**：管理员帐户登录 ID。
    
- **密码**：管理员帐户密码。
    
- **订阅**：如果有多个订阅，请选择要在其上创建虚拟机并对其计费的订阅。
    
- **资源组**：可以创建一个新资源组，也可以使用订阅中现有的空 Azure 资源组  。
    
- **位置**：选择最合适的数据中心。 通常，最合适的数据中心应拥有大部分数据，或者最接近实际位置以实现最快的网络访问。 如果你打算在 GPU 上运行深度学习，必须选择 Azure 中包含 NC 系列 GPU VM 实例的某个位置。 目前，这些位置为：美国东部、美国中北部、美国中南部、美国西部2、北欧、西欧  。 有关最新列表，请查看[“Azure 产品(按区域)”页](https://azure.microsoft.com/regions/services/)并在“计算”  下查找“NC 系列”  。 
    
    
**设置**：如果你打算在 Geo DSVM 上的 GPU 上运行深度学习，请选择 NC 系列 GPU 虚拟机大小之一。 否则，可以选择基于 CPU 的实例之一。 为 VM 创建存储帐户。 
       
**汇总**：验证输入的所有信息是否正确。
    
**购买**：若要开始预配过程，请单击“购买”。  此时会显示服务条款的链接。 除计算“大小”步骤中选择的服务器大小所产生的计算费用外，VM 不产生其他任何费用。  
 
 >[!NOTE]
 > 预配大约需要 20 - 30 分钟。 预配的状态在 Azure 门户上显示。

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>如何访问地理 AI 数据科学虚拟机

 创建 VM 后，可以开始使用在其上安装并预配置的工具。 许多工具带有开始菜单磁贴和桌面图标。 可以使用在**基本信息**部分中配置的管理员帐户凭据通过远程桌面访问 VM。

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>使用 VM 中安装的 ArcGIS Pro

Geo-DSVM 上已预装了 ArcGIS Pro 桌面，并且预配置了相应的环境，可在其中使用 DSVM 上的所有工具。 启动 ArcGIS 时，系统会提示你输入 ArcGIS 帐户的凭据。 如果你已有 ArcGIS 帐户并且有该软件的许可证，则可以使用现有凭据。  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

否则，可以注册新的 ArcGIS 帐户和许可证或者获取[免费试用版](https://www.arcgis.com/features/free-trial.html)。 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

在注册标准 ArcGIS 帐户或免费试用帐户后，可以根据 [ArcGIS Pro 入门](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)中的说明授权你的帐户使用 ArcGIS Pro。

通过 ArcGIS 帐户登录到 ArcGIS Pro 桌面后，可以开始将 VM 上安装并配置的数据科学工具用于地理空间分析和机器学习项目。

## <a name="next-steps"></a>后续步骤

根据以下资源中的指导开始使用 Geo AI Data Science VM：

* [使用地理 AI 数据科学 VM](use-geo-ai-dsvm.md)
