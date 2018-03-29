---
title: 机器学习和数据科学工具 - Azure | Microsoft 文档
description: 机器学习和数据科学工具
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: f8d7fff30d5f5289c362d78ad89027b8141bbbe6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="machine-learning-and-data-science-tools"></a>机器学习和数据科学工具
数据科学虚拟机 (DSVM) 拥有丰富的工具和库，可用于 Python、R、Julia 等热门语言的机器学习。 

以下是 DSVM 上的部分机器学习工具和库。 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| 它是什么？   |    快速、便携且分布式梯度提升（GBDT、GBRT 或 GBM）库，适用于 Python、R、Java、Scala、C++ 等。 在单个计算机、Hadoop、Spark 上运行    |
| 支持的 DSVM 版本     | Windows、Linux     |
| 典型用途      | 常规 ML 库      |
| 如何在 DSVM 上配置/安装它？      |  使用 GPU 支持安装   |
| 如何使用/运行它？      | 作为 Python 库（2.7 和 3.5）、R 程序包，以及 on path 路径命令行工具（`C:\dsvm\tools\xgboost\bin\xgboost.exe` 适用于 Windows，`/dsvm/tools/xgboost/xgboost` 适用于 Linux）运行    |
| 示例链接      | 示例包含在虚拟机上，在 Linux 上位于 `/dsvm/tools/xgboost/demo` 中，在 Windows 上位于 `C:\dsvm\tools\xgboost\demo` 中   |
| DSVM 上的相关工具      | LightGBM、MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| 它是什么？   |   Vowpal Wabbit（也称为“VW”）是一款开源、快速的核外学习系统库    |
| 支持的 DSVM 版本     | Windows、Linux     |
| 典型用途      | 常规 ML 库      |
| 如何在 DSVM 上配置/安装它？      |  Windows-msi 安装程序、Linux apt-get |
| 如何使用/运行它？      | 作为 on path 路径命令行工具（Windows 上为 `C:\Program Files\VowpalWabbit\vw.exe`，Linux 上为 `/usr/bin/vw`）    |
| 示例链接      | [VowPal Wabbit 示例](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| DSVM 上的相关工具      |LightGBM、MXNet、XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 它是什么？   |  Weka 是适用于数据挖掘任务的机器学习算法的集合。 这些算法可以直接应用于数据集，也可以从你自己的 Java 代码中调用。 Weka 包含用于数据预处理、分类、回归、群集、关联规则和可视化的工具。 |
| 支持的 DSVM 版本     | Windows、Linux     |
| 典型用途      | 常规 ML 工具     |
| 如何使用/运行它？      | 在 Windows 上，在“开始”菜单中搜索 Weka。 在 Linux 上，使用 X2Go 登录，然后导航到“应用程序”->“开发”->“Weka”。 |
| 示例链接      | [Weka 示例](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| DSVM 上的相关工具      |LightGBM、Rattle、XGBooost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 它是什么？   |   一种使用 R 编写的用于数据挖掘的图形用户界面   |
| 支持的 DSVM 版本     | Windows、Linux     |
| 典型用途      | R 适用的常规 UI 数据挖掘工具    |
| 如何使用/运行它？      | UI 工具。 在 Windows 上，启动命令提示符，运行 R，然后在 R 中运行 `rattle()`。 在 Linux 上，使用 X2Go 连接，启动终端，运行 R，然后在 R 中运行 `rattle()`。 |
| 示例链接      | [Rattle](https://togaware.com/onepager/) |
| DSVM 上的相关工具      |LightGBM、Weka、XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| 它是什么？   | 基于决策树算法的快速、分布式、高性能梯度提升（GBDT、GBRT、GBM 或 MART）框架，用于排名、分类及许多其他机器学习任务。    |
| 支持的 DSVM 版本      | Windows、Linux    |
| 典型用途      | 常规用途的梯度提升框架      |
| 如何在 DSVM 上配置/安装它？      | 在 Windows 上，LightGBM 作为 Python 程序包安装。 在 Linux 上，命令行可执行文件位于 `/opt/LightGBM/lightgbm` 中，安装了 R 程序包，并安装了 Python 程序包。     |
| 示例链接      | [LightGBM 指南](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| DSVM 上的相关工具      | MXNet、XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| 它是什么？   | 支持内存中、分布式、快速且可缩放机器学习的开源 AI 平台  |
| 支持的 DSVM 版本      | Linux   |
| 典型用途      | 常规用途的分布式可缩放 ML   |
| 如何在 DSVM 上配置/安装它？      | H2O 安装在 `/dsvm/tools/h2o` 中。      |
| 如何使用/运行它？      | 使用 X2Go 连接到虚拟机。 启动新的终端并运行 `java -jar /dsvm/tools/h2o/current/h2o.jar`。 然后启动 Web 浏览器并连接到 `http://localhost:54321`。      |
| 示例链接      | 在 `h2o` 目录的 Jupyter 中的虚拟机上提供有示例。      |
| DSVM 上的相关工具      | Apache Spark、MXNet、XGBoost、Sparkling Water、Deep Water    |

DSVM 上还有其他几个 ML 库，如热门的 `scikit-learn` 程序包，它作为安装在 DSVM 上的 Anaconda Python 发行版的一部分。 请务必通过运行相应的程序包管理器来查看 Python、R 和 Julia 中可用的程序包列表。 
