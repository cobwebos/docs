---
title: 在 Azure 实验室服务中配置实验室帐户 |Microsoft Docs
description: 本文介绍如何创建实验室帐户、查看所有实验室帐户或在 Azure 实验室服务中删除实验室帐户。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 597bbfdc35c556b5c75968084c01ac222fc468f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718090"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>在 Azure 实验室服务中配置实验室帐户 
在 Azure 实验室服务中，实验室帐户是托管实验室类型的容器，例如教室实验室。 관리자는 Azure Lab Services를 사용하여 랩 계정을 설정하고 계정에 랩을 만들 수 있는 랩 소유자에게 액세스 권한을 제공합니다. 이 문서는 랩 계정을 만들거나, 모든 랩 계정을 보거나, 랩 계정을 삭제하는 방법을 설명합니다.

## <a name="connect-with-a-peer-virtual-network"></a>与对等虚拟网络连接
若要将虚拟网络作为对等网络连接到实验室的虚拟网络，请执行以下步骤：

1. 在 "**实验室帐户**" 页上，选择左侧菜单上的 "**实验室配置**"。

    ![实验室配置页](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. 对于**对等虚拟网络**，选择 "**已启用**" 或 "**已禁用**"。 默认值为 "**已禁用**"。 若要启用对等虚拟网络，请执行以下步骤： 
    1. **사용**을 선택합니다.
    2. 从下拉列表中选择**VNet** 。 
3. 도구 모음에서 **저장**을 선택합니다. 

此帐户中创建的实验室已连接到所选虚拟网络。 它们可以访问所选虚拟网络中的资源。 有关详细信息，请参阅[在 Azure 实验室服务中将实验室的网络与对等虚拟网络连接](how-to-connect-peer-virtual-network.md)。

为**对等虚拟网络**字段选择虚拟网络后，将禁用 "**允许实验室创建者选取实验室位置**" 选项。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域，才能与对等虚拟网络中的资源进行连接。 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>允许实验室创建者选取实验室位置
可以通过执行以下步骤，允许 lab creator 在不同于实验室帐户位置的位置创建实验室： 

1. 在 "**实验室帐户**" 页上，选择左侧菜单上的 "**实验室配置**"。
2. 对于 "**允许实验室创建者选取实验室位置**"，请选择 "**已启用**" （如果希望实验室创建者能够选择实验室位置）。 如果已禁用，则会在实验室帐户所在的同一位置自动创建实验室。 
    
    为**对等虚拟网络**字段选择虚拟网络时，将禁用此字段。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域，才能访问对等虚拟网络中的资源。 
1. 도구 모음에서 **저장**을 선택합니다. 

    ![配置实验室位置设置](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>在实验室中为 Vm 指定地址范围
以下过程包含在实验室中为 Vm 指定地址范围的步骤。 如果更新之前指定的范围，则修改后的地址范围仅适用于在进行更改后创建的 Vm。 

在指定应记住的地址范围时，有一些限制。 

- 前缀必须小于或等于23。 
- 如果虚拟网络对等互连到实验室帐户，则提供的地址范围不能与对等互连虚拟网络中的地址范围重叠。

1. 在 "**实验室帐户**" 页上，选择左侧菜单上的 "**实验室配置**"。
2. 为 "**地址范围**" 字段指定将在实验室中创建的 vm 的地址范围。 地址范围应为无类域间路由（CIDR）表示法（例如： 10.20.0.0/23）。 랩의 가상 머신은 이 주소 범위에서 만들어집니다.
3. 도구 모음에서 **저장**을 선택합니다. 

    ![配置地址范围](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뛸 수 있습니다. 다른 사용자 계정을 사용하여 클래스룸 랩을 만들려면 다음 단계를 수행합니다. 

강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 **랩 작성자** 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 역할 할당 추가**를 클릭합니다. 

    ![액세스 제어 -> 역할 할당 추가 단추](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **역할 할당 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 추가](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > 如果要添加非 Microsoft 帐户用户作为实验室创建者，请参阅[将非 Microsoft 帐户用户添加为实验室创建者](#add-a-non-microsoft-account-user-as-a-lab-creator)部分。 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>랩 작성자에 사용할 수 있는 Marketplace 이미지 지정
랩 계정 소유자는 랩 작성자가 랩 계정에서 랩을 만들 때 사용할 수 있는 Marketplace 이미지를 지정할 수 있습니다. 

1. 왼쪽 메뉴에서 **Marketplace 이미지**를 선택합니다. 기본적으로 이미지의 전체 목록이 표시됩니다(사용 및 사용 안 함으로 설정된 이미지 모두 포함). 맨 위의 드롭다운 목록에서 **Enabled only**(사용으로 설정된 이미지만)/**Disabled only**(사용 안 함으로 설정된 이미지만) 옵션을 선택하여 사용/사용 안 함으로 설정된 이미지만 표시하도록 목록을 필터링할 수 있습니다. 
    
    ![Marketplace 이미지 페이지](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    다음 조건을 만족하는 Marketplace 이미지만 목록에 표시됩니다.
        
    - 단일 VM을 만듭니다.
    - Azure Resource Manager를 사용하여 VM을 프로비전합니다.
    - 추가 라이선스 플랜을 구입하지 않아도 됩니다.
2. 사용하도록 설정된 Marketplace 이미지를 **사용하지 않도록 설정**하려면 다음 작업 중 하나를 수행합니다. 
    1. 마지막 열에서 **...(줄임표)** 를 선택하고, **이미지 사용 안 함**을 선택합니다. 

        ![하나의 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 목록에서 이미지 이름 앞의 확인란을 선택하여 목록에서 하나 이상의 이미지를 선택하고, **선택한 이미지 사용 안 함**을 선택합니다. 

        ![여러 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 마찬가지로 Marketplace 이미지를 **사용하도록 설정**하려면 다음 작업 중 하나를 수행합니다. 
    1. 마지막 열에서 **...(줄임표)** 를 선택하고, **이미지 사용**을 선택합니다. 
    2. 목록에서 이미지 이름 앞의 확인란을 선택하여 목록에서 하나 이상의 이미지를 선택하고, **선택한 이미지 사용**을 선택합니다. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>添加非 Microsoft 帐户用户作为实验室创建者
若要将用户添加为实验室创建者，请使用其电子邮件帐户。 可以使用以下类型的电子邮件帐户：

- 大学办公室 365 Azure Active Directory （AAD）提供的电子邮件帐户。 
- Microsoft 电子邮件帐户，如 `@outlook.com`、`@hotmail.com`、`@msn.com`或 `@live.com`。
- 非 Microsoft 电子邮件帐户，如 Yahoo 或 Google 提供的帐户。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- GitHub 계정. 此帐户必须与 Microsoft 帐户链接。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户
实验室创建者/讲师可以使用非 Microsoft 电子邮件帐户来注册和登录到教室实验室。  但是，登录到实验室服务门户要求讲师首先创建一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。

许多教师可能已经有一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。 例如，如果讲师已经将其电子邮件地址与 Microsoft 的其他产品或服务（如 Office、Skype、OneDrive 或 Windows）一起使用，则该 Microsoft 帐户。  

当教师登录到实验室服务门户时，会提示他们输入其电子邮件地址和密码。 如果讲师尝试使用未链接 Microsoft 帐户的非 Microsoft 帐户进行登录，则讲师将收到以下错误消息： 

![오류 메시지](../media/how-to-configure-student-usage/cant-find-account.png)

若要注册 Microsoft 帐户，讲师应使用[http://signup.live.com](http://signup.live.com)。  


### <a name="using-a-github-account"></a>使用 GitHub 帐户
讲师还可以使用现有的 GitHub 帐户注册和登录到教室实验室。 如果讲师已经有一个链接到其 GitHub 帐户的 Microsoft 帐户，则他们可以登录并提供其密码，如前一部分中所示。 如果尚未将其 GitHub 帐户链接到 Microsoft 帐户，则应选择 "**登录" 选项**：

![登录选项链接](../media/how-to-configure-student-usage/signin-options.png)

在 "**登录选项**" 页面上，选择 "**用 GitHub 登录**"。

![用 GitHub 链接登录](../media/how-to-configure-student-usage/signin-github.png)

最后，系统将提示他们创建链接到其 GitHub 帐户的 Microsoft 帐户。 当讲师选择**下一个**时，会自动发生这种情况。  然后，教师会立即登录并连接到教室实验室。

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>断开连接时自动关闭 Vm
断开远程桌面连接后，可以启用或禁用 Windows 实验室 Vm （模板或学生）的自动关闭。 你还可以指定 Vm 在自动关机之前应等待用户重新连接的时间。

![实验室帐户中的自动关闭设置](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

此设置适用于在实验室帐户中创建的所有实验室。 实验室所有者可以在实验室级别覆盖此设置。 在实验室帐户中对此设置的更改将只影响在更改后创建的实验室。

若要了解实验室所有者如何在实验室级别配置此设置，请参阅[此文](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
