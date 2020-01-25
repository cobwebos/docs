---
title: 将数据加载到 Azure 存储环境-团队数据科学流程
description: 了解如何将数据引入存储和处理数据的各种目标环境。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720531"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>분석용 스토리지 환경에 데이터 로드

团队数据科学过程要求在每个阶段将数据引入或加载到最合适的方式。 数据目标可以包含 Azure Blob 存储、SQL Azure 数据库、SQL Server Azure VM、HDInsight （Hadoop）、Synapse 分析和 Azure 机器学习。 

다음 문서에서는 데이터가 저장되고 처리되는 다양한 대상 환경으로 데이터를 수집하는 방법을 설명합니다.

* [Azure Blob Storage](move-azure-blob.md) 간 수집
* [Azure VM의 SQL Server](move-sql-server-virtual-machine.md)로 수집
* 到[AZURE SQL Database](move-sql-azure.md)
* [Hive 테이블](move-hive-tables.md)로 수집
* [SQL 분할된 테이블](parallel-load-sql-partitioned-tables.md)로 수집
* [온-프레미스 SQL Server](move-sql-azure-adf.md)에서 수집

技术和业务需求，以及数据的初始位置、格式和大小将决定最佳的数据引入计划。 最佳计划需要几个步骤并不少见。 이 작업 시퀀스에는 데이터 탐색, 사전 처리, 정리, 다운 샘플링, 모델 학습 등이 포함될 수 있습니다.  Azure 数据工厂是一个建议的 Azure 资源，用于安排数据移动和转换。
