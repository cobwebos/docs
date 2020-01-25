---
title: Azure에서 데이터 과학 환경 설정 - Team Data Science Process
description: 팀 데이터 과학 프로세스에서 사용되는 Azure의 데이터 과학 환경을 설정합니다.
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
ms.openlocfilehash: d11e1614307d5b6cb267ae72bae6f32adb4b244a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722214"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>팀 데이터 과학 프로세스에서 사용되는 데이터 과학 환경 설정
팀 데이터 과학 프로세스는 데이터의 스토리지, 처리 및 분석을 위해 다양한 데이터 과학 환경을 사용합니다. 여기에는 Azure Blob Storage, 여러 유형의 Azure 가상 머신, HDInsight(Hadoop) 클러스터 및 Azure Machine Learning 작업 영역이 포함됩니다. 사용할 환경에 대한 결정은 모델링할 데이터의 유형 및 양과 클라우드에서 해당 데이터의 대상에 따라 달라집니다. 

* 이러한 결정을 내릴 때 고려할 질문에 대한 지침은 [Azure Machine Learning 데이터 과학 환경 계획](plan-your-environment.md)을 참조하세요. 
* 고급 분석을 수행할 때 발생할 수 있는 일부 시나리오의 카탈로그는 [팀 데이터 과학 프로세스 시나리오](plan-sample-scenarios.md)

다음 문서에서는 팀 데이터 과학 프로세스에 사용되는 다양한 데이터 과학 환경을 설정하는 방법에 대해 설명합니다.

* [Azure storage-account](../../storage/common/storage-account-create.md)
* [HDInsight(Hadoop) 클러스터](customize-hadoop-cluster.md)
* [Azure 机器学习 Studio （经典）工作区](../studio/create-workspace.md)

**Microsoft DSVM(데이터 과학 Virtual Machine)** 은 Azure VM(가상 머신) 이미지로 사용할 수 있습니다. 이 VM은 데이터 분석 및 Machine Learning에 흔히 사용되는 몇 가지 인기 있는 도구로 사전 설치되고 구성됩니다. Windows 및 Linux에는 DSVM를 사용할 수 있습니다. 자세한 내용은 [Linux 및 Windows용 클라우드 기반 데이터 과학 Virtual Machine에 대한 소개](../data-science-virtual-machine/overview.md)를 참조하세요.

다음을 만드는 방법을 알아봅니다.

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
