---
title: 使用 Azure 流分析
description: 솔루션 개발을 위한 Azure SQL Data Warehouse와 함께 Azure Stream Analytics 사용을 위한 팁
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721194"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>将 Azure 流分析与 Azure Synapse Analytics 配合使用
Azure Stream Analytics는 완전히 관리되는 서비스로, 클라우드의 스트리밍 데이터에 대해 대기 시간이 짧고 확장성이 뛰어난 고가용성의 복합 이벤트 처리 기능을 제공합니다. [Azure Stream Analytics 소개](../stream-analytics/stream-analytics-introduction.md)를 읽어 기본 사항을 배울 수 있습니다. [Azure Stream Analytics를 사용하여 시작](../stream-analytics/stream-analytics-real-time-fraud-detection.md) 자습서에 따라 Stream Analytics로 엔드투엔드 솔루션을 만드는 방법에 대해 알 수 있습니다.

本文介绍如何使用数据仓库数据库作为流分析作业的输出接收器。

## <a name="prerequisites"></a>필수 조건
먼저, [Azure Stream Analytics를 사용하여 시작](../stream-analytics/stream-analytics-real-time-fraud-detection.md) 자습서에서 다음 단계를 실행합니다.  

1. 이벤트 허브 입력 만들기
2. 이벤트 생성기 애플리케이션 구성 및 시작
3. Stream Analytics 작업 프로비전
4. 작업 입력 및 쿼리 지정

그런 다음 SQL Data Warehouse 데이터베이스를 만듭니다.

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>작업 출력 지정: Azure SQL Data Warehouse 데이터베이스
### <a name="step-1"></a>1단계
在流分析作业中，单击页面顶部的 "**输出**"，然后单击 "**添加**"。

### <a name="step-2"></a>2단계
选择 "SQL 数据库"。

### <a name="step-3"></a>3단계
다음 페이지에 다음 값을 입력합니다.

* *출력 별칭*: 이 작업 출력의 이름을 입력합니다.
* *구독*:
  * SQL Data Warehouse가 Stream Analytics 작업과 동일한 구독 내에 있는 경우 현재 구독에서 SQL Database 사용을 선택할 수 있습니다.
  * 데이터베이스가 다른 구독에 있는 경우 다른 구독에서 SQL Database 사용을 선택합니다.
* *데이터베이스*: 대상 데이터베이스의 이름을 지정합니다.
* *서버 이름*: 방금 지정한 데이터베이스에 대한 서버 이름을 지정합니다. Azure Portal을 사용하여 찾을 수 있습니다.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *사용자 이름*: 데이터베이스에 대한 쓰기 권한이 있는 계정의 사용자 이름을 지정합니다.
* *암호*: 지정된 사용자 계정에 대한 암호를 제공합니다.
* *테이블*: 데이터베이스에서 대상 테이블의 이름을 지정합니다.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>4단계:
확인 단추를 클릭하여 이 작업 출력을 추가하고 Stream Analytics가 데이터베이스에 성공적으로 연결될 수 있는지 확인합니다.

成功连接到数据库后，会在门户中看到一条通知。 您可以单击 "测试" 以测试与数据库的连接。

## <a name="next-steps"></a>다음 단계
有关集成的概述，请参阅[集成其他服务](sql-data-warehouse-overview-integrate.md)。
有关更多开发技巧，请参阅[数据仓库的设计决策和编码技术](sql-data-warehouse-overview-develop.md)。

