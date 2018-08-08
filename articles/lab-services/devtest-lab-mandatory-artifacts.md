---
title: 指定 Azure 开发测试实验室的必需项目 | Microsoft Docs
description: 了解如何指定在实验室中的虚拟机 (VM) 上安装任何用户选定项目之前需要安装的必需项目。
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: a739b958ad60e39c38e81ce887edf68349340bb0
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295176"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>为 Azure 开发测试实验室中的实验室指定必需项目
实验室的所有者可以指定要应用到实验室中创建的每台计算机的必需项目。 假设你要将实验室中的每台计算机连接到企业网络。 在这种情况下，每个实验室用户必须在创建虚拟机期间添加一个域加入项目，以确保其计算机连接到企业域。 换而言之，实验室用户最起码要重新创建一个计算机，以防忘记在计算机上应用必需的项目。 实验室的所有者可将域加入项目指定为实验室中的必需项目。 此步骤可确保每台计算机连接到企业网络，并节省实验室用户的时间和精力。
 
其他必需项目可以包括团队常用的工具，或者每台计算机默认需要包含的平台相关安全包，等等。简言之，实验室中每台计算机必须包含的任何常用软件将成为必需项目。 如果在应用了必需项目的计算机中创建自定义映像，然后从该映像创建全新的计算机，则创建期间，将在该计算机上重新应用必需项目。 此行为也意味着，即使自定义映像变得陈旧，每次从该映像创建计算机时，也会在执行创建流期间向该计算机应用最新版本的必需项目。 
 
仅支持将不带参数的项目用作必需项目。 在创建实验室期间，实验室用户不需要输入附加的参数，因此简化了 VM 的创建过程。 

## <a name="specify-mandatory-artifacts"></a>指定必需的项目
可以单独为 Windows 和 Linux 计算机选择必需项目。 此外，可以根据这些项目的应用顺序，将这些项目重新排序。 

1. 在实验室的主页上，选择“设置”下的“配置和策略”。 
3. 选择“外部资源”下的“必需项目”。 
4. 在“Windows”部分或“Linux”部分选择“编辑”。 本示例使用“Windows”选项。 

    ![必需项目页 -“编辑”按钮](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. 选择项目。 本示例使用“7-Zip”选项。 
5. 在“添加项目”页上，选择“添加”。 

    ![必需项目页 - 添加 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. 若要添加另一个项目，请选择该项目，然后选择“添加”。 本示例添加“Chrome”作为第二个必需项目。

    ![必需项目页 - 添加 Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. “必需项目”页上会显示一条消息，其中指定了选定的项目数。 如果单击该消息，会看到选定的项目。 选择“保存”以保存项目。 

    ![必需项目页 - 保存项目](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. 重复上述步骤以指定 Linux VM 的必需项目。 
    
    ![必需项目页 - Windows 和 Linux 项目](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. 若要从列表中**删除**某个项目，请选择行尾的“...”（省略号），然后选择“删除”。 
10. 若要将列表中的项目**重新排列**，请将鼠标悬停在该项目上，选择行首显示的“...”（省略号），并将项拖到新位置。 
11. 若要在实验室中保存必需项目，请选择“保存”。 

    ![必需项目页 - 在实验室中保存项目](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. 关闭“配置和策略”页（选择右上角的“X”），返回实验室主页。  

## <a name="delete-a-mandatory-artifact"></a>删除必需项目
若要从实验室中删除某个必需项目，请执行以下操作： 

1. 选择“设置”下的“配置和策略”。 
2. 选择“外部资源”下的“必需项目”。 
3. 在“Windows”部分或“Linux”部分选择“编辑”。 本示例使用“Windows”选项。 
4. 在顶部选择包含必需项目数的消息。 

    ![必需项目页 - 选择消息](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. 在“选定的项目”页上，选择要删除的项目旁边的“...”（省略号），然后选择”删除”。 
    
    ![必需项目页 - 删除项目](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. 选择“确定”关闭“选定的项目”页。 
7. 在“必需项目”页上选择“保存”。
8. 根据需要，针对 **Linux** 映像重复上述步骤。 
9. 选择“保存”，保存对实验室所做的全部更改。 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>创建 VM 时查看必需项目
现在，实验室用户可以在实验室中创建 VM 时查看必需项目的列表。 无法编辑或删除实验室所有者在实验室中设置的必需项目。

1. 在实验室的主页上，从菜单中选择“概述”。
2. 若要将 VM 添加到实验室，请选择“+ 添加”。 
3. 选择一个**基本映像**。 本示例使用“Windows Server 版本 1709”。
4. 请注意，此时会出现一条**项目**消息，其中包含选定的必需项目数。 
5. 选择“项目”。 
6. 确认看到了在实验室的配置和策略中指定的**必需项目**。 

    ![创建 VM - 必需项目](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>后续步骤
* 了解如何 [将 Git 项目存储库添加到实验室](devtest-lab-add-artifact-repo.md)。

