---
title: 使用课堂实验室培训-Azure 实验室服务
description: 本文介绍如何使用 Azure 开发测试实验室在 Azure 上创建实验室，以实现培训方案。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717979"
---
# <a name="use-classroom-labs-for-trainings"></a>使用课堂实验室培训
可以为培训设置实验室。 使用 Azure 实验室服务的教室实验室，您可以为您的培训创建实验室，其中每个学员都使用完全相同的隔离环境进行定型。 각 실습생이 필요할 때에만 교육 환경을 이용할 수 있도록 하고 교육에 필요한 리소스(예: 가상 머신)를 충분히 포함하는 정책을 적용할 수 있습니다. 

![课堂实验室](../media/classroom-labs-scenarios/classroom.png)

课堂实验室满足在任何虚拟环境中进行培训所需的以下要求： 

- 실습생은 학습 환경을 신속하게 프로비전할 수 있습니다.
- 모든 학습 컴퓨터는 동일해야 합니다.
- 실습생은 다른 실습생이 만든 VM을 볼 수 없습니다.
- 실습생이 학습에 필요한 만큼의 VM만 사용하고 사용하지 않을 때는 VM을 종료하도록 하여 비용을 제어합니다.
- 각 실습생과 학습 랩을 쉽게 공유합니다.
- 학습 랩을 반복해서 다시 사용합니다.

本文介绍了各种 Azure 实验室服务功能，这些功能可用于满足前面所述的培训要求，并详细介绍了如何设置实验室进行培训。  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>创建实验室帐户作为实验室帐户管理员
使用 Azure 实验室服务的第一步是在 Azure 门户中创建实验室帐户。 实验室帐户管理员创建实验室帐户后，管理员会将想要创建实验室的用户添加到实验室创建**者**角色。 讲师通过虚拟机创建实验室，以便学生为其讲授课程做练习。 有关详细信息，请参阅[创建和管理实验室帐户](how-to-manage-lab-accounts.md)。

## <a name="create-and-manage-classroom-labs"></a>클래스룸 랩 만들기 및 관리
作为实验室帐户中的实验室创建者角色成员的培训人员可以在实验室帐户中创建一个或多个实验室。 你可以创建和配置模板 VM，其中包含在你的课程中执行练习所需的所有软件。 从可用映像中选择现成的映像，以创建教室实验室，然后通过安装实验室所需的软件对其进行自定义。 有关详细信息，请参阅[创建和管理教室实验室](how-to-manage-classroom-labs.md)。

## <a name="configure-usage-settings-and-policies"></a>사용 설정 및 정책 구성
实验室创建者可以向实验室添加用户或从中删除用户，获取注册链接以发送到实验室用户，设置策略，例如设置每个用户的单个配额、更新实验室中可用的 Vm 数量等。 有关详细信息，请参阅[配置使用设置和策略](how-to-configure-student-usage.md)。

## <a name="create-and-manage-schedules"></a>일정 만들기 및 관리
일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 有关详细信息，请参阅[创建和管理教室实验室计划](how-to-create-schedules.md)。

## <a name="set-up-and-publish-a-template-vm"></a>设置和发布模板 VM
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 设置模板 VM，使其完全按你要提供给培训参与者的内容进行配置。 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 그런 후 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 有关详细信息，请参阅[设置和发布模板虚拟机](how-to-create-manage-template.md)。 

## <a name="use-vms-in-the-classroom-lab"></a>使用课堂实验室中的 Vm
学生或培训与会者注册到实验室，并连接到 VM，以便在课程中完成练习。 有关详细信息，请参阅[如何访问教室实验室](how-to-use-classroom-lab.md)。

## <a name="next-steps"></a>다음 단계
按照文章：[教程：使用 Azure 实验室服务设置实验室帐户](tutorial-setup-lab-account.md)中的说明，开始在课堂实验室中创建实验室帐户。