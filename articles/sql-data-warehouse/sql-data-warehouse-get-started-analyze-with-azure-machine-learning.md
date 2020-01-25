---
title: Azure Machine Learning을 사용하여 데이터 분석
description: Azure Machine Learning을 사용하여 Azure SQL Data Warehouse에 저장된 데이터를 기반으로 예측 기계 학습 모델을 구축합니다.
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
ms.openlocfilehash: 892d4642d700949d1d1169c69926021c751cef67
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721279"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 데이터 분석
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

이 자습서는 Azure Machine Learning을 사용하여 Azure SQL Data Warehouse에 저장된 데이터를 기반으로 예측 기계 학습 모델을 구축합니다. 특히 고객이 자전거를 구매할 가능성 여부를 예측하여 자전거 매장인 Adventure Works에 대한 대상 마케팅 캠페인을 구축합니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>필수 조건
이 자습서를 단계별로 실행하려면 다음이 필요합니다.

* AdventureWorksDW 샘플 데이터로 미리 로드된 SQL Data Warehouse. 프로비전하려면 [SQL Data Warehouse 만들기](create-data-warehouse-portal.md) 를 참조하고 샘플 데이터 로드를 선택합니다. 데이터 웨어하우스는 있지만 샘플 데이터가 없는 경우 [샘플 데이터를 수동으로 로드](sql-data-warehouse-load-sample-databases.md)할 수 있습니다.

## <a name="1-get-the-data"></a>1. 获取数据
데이터는 AdventureWorksDW 데이터베이스의 dbo.vTargetMail 보기에 있습니다. 이 데이터를 읽으려면:

1. [Azure Machine Learning 스튜디오](https://studio.azureml.net/)에 로그인하고 내 실험을 클릭합니다.
2. 单击屏幕左下角的 " **+ 新建**"，并选择 "**空白试验**"。
3. 실험: 대상 마케팅에 대한 이름을 입력합니다.
4. 将 "**导入数据**" 模块从 "模块" 窗格中的 "**数据输入" 和 "输出**" 拖到画布中。
5. 속성 창에서 SQL Data Warehouse 데이터베이스에 대한 세부 정보를 지정합니다.
6. 관련 데이터를 읽을 데이터베이스 **쿼리** 를 지정합니다.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

실험 캔버스 아래에서 **실행** 을 클릭하여 실험을 실행합니다.

![실험 실행](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

실험이 성공적으로 실행되고 나면 판독기 모듈 아래쪽에서 출력 포트를 클릭하고 **시각화** 를 선택하여 가져온 데이터를 확인합니다.

![查看导入的数据](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. 清除数据
데이터를 정리하려면 모델에 관련되지 않은 일부 열을 삭제합니다. 다음을 수행합니다.

1. 将 "数据转换" 中的 "**选择列**" 模块拖 **< 操作**到画布中。 将此模块连接到 "**导入数据**" 模块。
2. 속성 창에서 **열 선택기 시작** 을 클릭하여 삭제하려는 열을 지정합니다.

   ![项目列](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. 다음 두 열을 제외합니다.

   ![불필요한 열 제거](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. 生成模型
데이터를 80-20으로 분할합니다. 80%는 기계 학습 모델을 학습하고 20%는 모델을 테스트합니다. 이진 분류 문제에 대해 "2클래스" 알고리즘을 활용합니다.

1. **분할** 모듈을 캔버스로 끌어서 놓습니다.
2. 在 "属性" 窗格中，为第一个输出数据集中的部分行输入0.8。

   ![훈련 및 테스트 집합으로 데이터 분할](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. **2클래스 향상된 의사 결정 트리** 모듈을 캔버스로 끌어서 놓습니다.
4. 将 "**训练模型**" 模块拖放到画布中并指定输入，方法是将其连接到 "**双类提升决策树**（ML 算法）"，并将 "**拆分**" （用于训练算法的数据）模块。 

     ![모델 학습 모듈 연결](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. 그런 다음 속성 창에서 **열 선택기 시작** 을 클릭합니다. **BikeBuyer** 열을 예측할 열로 선택합니다.

   ![选择要预测的列](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. 对模型进行评分
이제 모델이 테스트 데이터를 수행하는 방법을 테스트합니다. 더 잘 수행하는 알고리즘을 확인하도록 다른 알고리즘을 선택하여 비교합니다.

1. 将 "**评分模型**" 模块拖放到画布上，并将其连接到**定型模型**和**拆分数据**模块。

   ![모델 점수 매기기](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. **2클래스 Bayes 지점 컴퓨터** 를 실험 캔버스로 끌어서 놓습니다. 2클래스 향상된 의사 결정 트리와 비교하여 이 알고리즘이 수행하는 방법을 비교합니다.
3. 캔버스에서 모델 학습 및 모델 점수 매기기 모듈을 복사하고 붙여 넣습니다.
4. **모델 평가** 모듈을 캔버스로 끌어서 놓아 두 알고리즘을 비교합니다.
5. **실행** 합니다.

   ![실험 실행](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. 모델 평가 모듈의 아래쪽에서 출력 포트를 클릭하고 시각화를 클릭합니다.

   ![평가 결과 시각화](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

提供的度量值包括 ROC 曲线、精度召回关系图和提升曲线。 이러한 메트릭을 살펴보면 첫 번째 모델이 두 번째보다 더 잘 실행하는 것을 볼 수 있습니다. 若要查看第一个模型的预测内容，请单击 "评分模型" 的输出端口，并单击 "可视化"。

![점수 결과 시각화](media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

테스트 데이터 세트에 두 개의 열이 추가됩니다.

* 점수가 매겨진 확률: 고객이 자전거 구매자일 가능성입니다.
* 점수가 매겨진 레이블: 모델에 의해 분류가 실행되었습니다. – 자전거 구매자(1) 혹은 아님(0) 레이블 지정에 대한 확률 임계값은 50%로 설정되고 조정할 수 있습니다.

점수가 매겨진 레이블(예측)로 열 BikeBuyer(실제) 비교를 통해 모델이 얼마나 잘 실행했는지 확인할 수 있습니다. 接下来，可以使用此模型对新客户进行预测，并将此模型发布为 web 服务，或将结果写回到 SQL 数据仓库。

## <a name="next-steps"></a>다음 단계
예측 기계 학습 모델을 구축하는 방법에 대한 자세한 내용은 [Azure의 Machine Learning 소개](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/)를 참고하세요.