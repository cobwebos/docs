---
title: 不同 Azure 存储位置中的示例数据-团队数据科学流程
description: 작지만 대표적이며 보다 관리하기 쉬운 크기로 줄이기 위한 Azure Blob 컨테이너, SQL Server 및 Hive 테이블의 샘플 데이터
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
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718597"
---
# <a name="heading"></a>Azure blob 컨테이너, SQL Server 및 Hive 테이블의 데이터 샘플링

다음 문서에서는 세 가지 Azure 위치 중 하나에 저장된 데이터를 샘플링하는 방법을 설명합니다.

* [**Azure BLOB 컨테이너 데이터**](sample-data-blob.md)는 프로그래밍 방식으로 다운로드한 다음 샘플 Python 코드로 샘플링함으로써 샘플링됩니다.
* [**SQL Server 데이터**](sample-data-sql-server.md)는 SQL 및 Python 프로그래밍 언어를 둘 다 사용하여 샘플링됩니다. 
* [**Hive 테이블 데이터**](sample-data-hive.md)는 Hive 쿼리를 사용하여 샘플링됩니다.

이 샘플 작업은 [TDSP(팀 데이터 과학 프로세스)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)의 단계입니다.

**데이터를 샘플링하는 이유**

분석할 데이터 세트가 큰 경우 일반적으로 데이터를 다운 샘플링하여 작지만 전형적이고 관리하기 쉬운 크기로 줄이는 것이 좋습니다. 缩小可以简化数据理解、探索和特征工程。 Cortana Analytics 过程中的这一采样角色是启用数据处理功能和机器学习模型的快速原型设计。

