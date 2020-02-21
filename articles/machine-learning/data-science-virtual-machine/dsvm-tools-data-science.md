---
title: 机器学习和数据科学工具
titleSuffix: Azure Data Science Virtual Machine
description: 了解 Data Science Virtual Machine 上预安装的计算机学习工具和框架。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c6b7e3da6cb6fd87e3b43d6f310e3b76f8fc4d30
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526087"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure 数据科学虚拟机上的机器学习和数据科学工具
Azure 数据科学虚拟机（Dsvm）提供了一组丰富的工具和库，适用于广泛的语言（如 Python、R 和 Julia）的机器学习。

下面是 Dsvm 上的一些机器学习工具和库。

## <a name="azure-machine-learning-sdk-for-python"></a>适用于 Python 的 Azure 机器学习 SDK

请参阅适用于 Python 的[AZURE 机器学习 SDK](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)的完整参考。

|    |           |
| ------------- | ------------- |
| 它是什么？   |   Azure 机器学习是一项云服务，可用于开发和部署机器学习模型。 可以在使用 Python SDK 构建、训练、缩放和管理模型时跟踪模型。 将模型部署为容器，并在云中、在本地或在 Azure IoT Edge 上运行它们。   |
| 支持的版本     | Windows（conda 环境：AzureML）、Linux（conda 环境：py36）    |
| 典型用途      | 一般计算机学习平台      |
| 如何配置或安装它？      |  使用 GPU 支持安装   |
| 如何使用或运行它      | 作为 Python SDK 和 Azure CLI 中。 激活到 Windows 版本上的 conda 环境 `AzureML` 或 Linux 版本上的 *。* `py36`      |
| 指向示例的链接      | Notebook 下的 `AzureML` 目录中包含了示例 Jupyter Notebook。  |
| 相关工具      | Visual Studio Code、Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| 它是什么？   | 一种开源 AI 平台，支持内存中的分布式、快速且可缩放的机器学习。  |
| 支持的版本      | Linux   |
| 典型用途      | 常规用途的分布式、可缩放机器学习   |
| 如何配置或安装它？      | H2O 安装在 `/dsvm/tools/h2o` 中。      |
| 如何使用或运行它      | 使用 X2Go 连接到 VM。 启动新的终端并运行 `java -jar /dsvm/tools/h2o/current/h2o.jar`。 然后启动 Web 浏览器并连接到 `http://localhost:54321`。      |
| 指向示例的链接      | 在 `h2o` 目录的 Jupyter 中的虚拟机上提供有示例。      |
| 相关工具      | Apache Spark、MXNet、XGBoost、Sparkling Water、Deep Water    |

Dsvm 上还有其他几个计算机学习库，如 Dsvm 的 Anaconda Python 分发版中的常用 `scikit-learn` 包。 若要查看 Python、R 和 Julia 中可用的程序包列表，请运行相应的程序包管理器。

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| 它是什么？   | 基于决策树算法的一种快速、分布式、高性能的梯度提升（GBDT、GBRT、GBM 或集市）框架。 它用于排名、分类和其他许多机器学习任务。    |
| 支持的版本      | Windows、Linux    |
| 典型用途      | 常规用途梯度提升框架      |
| 如何配置或安装它？      | 在 Windows 上，LightGBM 作为 Python 程序包安装。 在 Linux 上，命令行可执行文件位于 `/opt/LightGBM/lightgbm` 中，安装了 R 程序包，并安装了 Python 程序包。     |
| 指向示例的链接      | [LightGBM 指南](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| 相关工具      | MXNet、XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 它是什么？   |   使用 R 进行数据挖掘的图形用户界面。   |
| 支持的版本     | Windows、Linux     |
| 典型用途      | 常规 UI 数据-R 的挖掘工具    |
| 如何使用或运行它      | 作为 UI 工具。 在 Windows 上，启动命令提示符，运行 R，然后在 R 中运行 `rattle()`。 在 Linux 上，使用 X2Go 进行连接，启动终端，运行 R，然后在 R 中运行 `rattle()`。 |
| 指向示例的链接      | [Rattle](https://togaware.com/onepager/) |
| 相关工具      |LightGBM、Weka、XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| 它是什么？   |   一种快速开源的核心学习系统库    |
| 支持的版本     | Windows、Linux     |
| 典型用途      | 一般计算机学习库      |
| 如何配置或安装它？      |  Windows： msi 安装程序<br/>Linux： apt-get |
| 如何使用或运行它      | 作为 on-path 命令行工具（Windows 上为 `C:\Program Files\VowpalWabbit\vw.exe`，Linux 上为 `/usr/bin/vw`）    |
| 指向示例的链接      | [VowPal Wabbit 示例](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| 相关工具      |LightGBM、MXNet、XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 它是什么？   |  用于数据挖掘任务的机器学习算法的集合。 这些算法可以直接应用于数据集，也可以从你自己的 Java 代码中调用。 Weka 包含用于数据预处理、分类、回归、群集、关联规则和可视化的工具。 |
| 支持的版本     | Windows、Linux     |
| 典型用途      | 一般计算机学习工具     |
| 如何使用或运行它      | 在 Windows 上，搜索 "**开始**" 菜单上的 "Weka"。 在 Linux 上，使用 X2Go 进行登录，然后转到“应用程序” **“开发”** “Weka”。 >  >  |
| 指向示例的链接      | [Weka 示例](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| 相关工具      |LightGBM、Rattle、XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| 它是什么？   |   适用于 Python、R、Java、Scala 等C++的快速、可移植和分布式梯度提升（GBDT、GBRT 或 GBM）库。 它在一台计算机上运行，并且在 Apache Hadoop 和 Spark 上运行。    |
| 支持的版本     | Windows、Linux     |
| 典型用途      | 一般计算机学习库      |
| 如何配置或安装它？      |  使用 GPU 支持安装   |
| 如何使用或运行它      | 作为 Python 库（2.7 和3.5）、R 包和路径命令行工具（适用于 Windows 的`C:\dsvm\tools\xgboost\bin\xgboost.exe` 和 `/dsvm/tools/xgboost/xgboost` 适用于 Linux）    |
| 指向示例的链接      | 虚拟机上包含了示例，在 Linux 上位于 `/dsvm/tools/xgboost/demo` 中，在 Windows 上位于 `C:\dsvm\tools\xgboost\demo` 中。   |
| 相关工具      | LightGBM、MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| 它是什么？   | 大数据上的开源 SQL 查询引擎    |
| 支持的 DSVM 版本      | Windows 2019 （预览版）、Linux  |
| 如何在 DSVM 上配置并安装它？      |  仅以嵌入模式安装在 `/dsvm/tools/drill*` 中   |
| 典型用途      |  用于就地数据探索，无需提取、转换、加载（ETL）。 查询不同的数据源和格式，其中包括 CSV、JSON、关系表和 Hadoop。     |
| 如何使用和运行它      | 桌面快捷方式  <br/> [10 分钟后即可开始钻取](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 上的相关工具      |   Rattle、Weka、SQL Server Management Studio      |


