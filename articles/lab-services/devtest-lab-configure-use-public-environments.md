---
title: Azure DevTest Labs에서 공용 환경 구성 및 사용 | Microsoft Docs
description: 本文介绍如何在 Azure 开发测试实验室中配置和使用公共环境（Git 存储库中的 Azure 资源管理器模板）。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 127a6986e04cf90f69b2a8ec70b90b877e534708
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721687"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공용 환경 구성 및 사용
Azure DevTest Labs에 제공되는 [Azure Resource Manager 템플릿의 공용 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Environments)를 사용하면 외부 GitHub 소스에 직접 연결하지 않고도 환경을 구축할 수 있습니다. 이 리포지토리에는 Azure Web Apps, Service Fabric Cluster 및 개발 SharePoint Farm 환경처럼 자주 사용되는 템플릿이 포함되어 있습니다. 이 기능은 사용자가 만든 모든 랩에 포함된 아티팩트의 공용 리포지토리와 비슷합니다. 이 환경 리포지토리를 사용하면 랩 내에서 PaaS 리소스에 원활한 시작 환경을 제공하기 위해 최소한의 입력 매개 변수로 미리 작성된 환경 템플릿으로 빠르게 시작할 수 있습니다. 

## <a name="configuring-public-environments"></a>공용 환경 구성
랩 소유자는 랩을 만드는 동안 랩의 공용 환경 리포지토리를 활성화할 수 있습니다. 랩의 공용 환경을 활성화하려면 랩을 만들 때 **공용 환경** 필드에 **켜기**를 선택합니다. 

![새 랩에 공용 환경 활성화](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


기존 랩의 경우 공용 환경 리포지토리가 활성화되어 있지 않습니다. 리포지토리에서 템플릿을 사용하려면 수동으로 활성화하세요. Resource Manager 템플릿을 사용하여 생성된 랩의 경우 리포지토리도 기본적으로 비활성화됩니다.

랩에 공용 환경을 활성화/비활성화할 수 있고, 다음 단계를 따라 랩 사용자가 특정 환경만 사용하도록 할 수도 있습니다. 

1. 랩의 **구성 및 정책**을 선택합니다. 
2. **가상 머신 기본** 섹션에서 **공용 환경**을 선택합니다.
3. 랩에 공용 환경을 활성화하려면 **예**를 선택합니다. 그렇지 않은 경우 **아니요**를 선택합니다. 
4. 공용 환경을 활성화하면 리포지토리의 모든 환경이 기본적으로 활성화됩니다. 특정 환경을 선택 해제하여 랩 사용자가 사용하지 못하게 할 수 있습니다. 

![공용 환경 페이지](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>랩 사용자 권한으로 환경 템플릿 사용
랩 사용자는 랩 페이지의 도구 모음에서 간단히 **+추가**를 선택하여 환경 템플릿의 활성화된 목록에서 새 환경을 만들 수 있습니다. 베이스 목록에는 랩 관리자가 목록 상단에 활성화한 공용 환경 템플릿이 포함되어 있습니다.

![공용 환경 템플릿](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>다음 단계
이 리포지토리는 자주 사용하는 유용한 사용자 지정 Resource Manager 템플릿을 추가하는 데 기여할 수 있는 오픈 소스 리포지토리입니다. 기여하려면 리포지토리에 끌어오기 요청을 제출하기만 하면 됩니다.  
