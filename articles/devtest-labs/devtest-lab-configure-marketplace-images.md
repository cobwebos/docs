---
title: 在 Azure 开发测试实验室中配置 Azure 市场映像设置
description: 在 Azure 开发测试实验室中创建 VM 时，配置可以使用哪个 Azure 市场映像
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512429"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置 Azure 市场映像设置
开发测试实验室支持基于 Azure 市场映像创建 VM，具体取决于配置的 Azure 市场映像在实验室中的使用方式。 本文演示如何指定在实验室中创建 VM 时可使用哪些 Azure 市场映像（如果有的话）。 它可确保你的团队仅有权访问所需的 Marketplace 映像。 

## <a name="specify-allowed-images-for-creating-vms"></a>指定允许用于创建 Vm 的映像
执行以下步骤可指定创建 VM 时允许哪些 Azure Marketplace 映像。 

1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“所有服务”****，并从列表中选择“开发测试实验室”****。
3. 在实验室列表中，选择实验室。 
4. 在 "实验室" 页的 "主页" 页上，选择 " **配置和策略**"。
5. 在实验室的 " **配置和策略** " 页的 " **虚拟机基础**" 下，选择 " **Marketplace 映像**"。
6. 指定是否希望所有限定的 Azure 市场映像可用作新 VM 的基础。 如果选择“是”****，则在实验室中允许符合以下所有条件的所有 Azure 市场映像：
   
   * 由映像创建单个 VM，**和**
   * 映像使用 Azure 资源管理器来预配 VM，**和**
   * 映像不需要购买额外的许可计划
     
     如果想要允许任何映像，或者想要指定可以使用的图像，则选择“否”****。
     
     ![此选项可允许所有市场映像用作 VM 的基础映像](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. 如果选择“否”**** 进入上一步，将启用“允许的映像/全选”**** 复选框。 
   可使用此选项以及搜索框快速选择或取消选择显示在列表中的所有项。
   * 选中每个映像对应的复选框，分别选择允许用于创建 VM 的 Azure 市场映像。
   * 如果不希望允许在实验室中使用任何 Azure 市场映像，请勿从列表中选择任何项。
   
     ![可指定哪个 Azure 市场映像可以用作 VM 的基础映像](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>疑难解答
如果无法找到要为实验室启用的特定映像，请执行以下步骤： 

- 查看是否可以在创建计算 VM 时查看映像。
- 所使用的订阅类型中的映像可能不可用。 请与订阅管理员联系，以获取订阅的类型 (例如： MSDN、free、即用即付等。 ) 。 
- 在开发测试实验室中对第2代映像的支持是有限的。 如果第 1 代版本和第 2 代版本都可用于映像，则开发测试实验室在创建 VM 时仅显示映像的 1 代版本。 解决方法是在实验室外创建自定义 Gen 2 映像，并使用它来创建 VM。 如果只有第2代映像可用，则支持并在列表中通过开发测试实验室显示该版本。 
      


## <a name="next-steps"></a>后续步骤
配置了创建 VM 时允许 Azure Marketplace 映像的方式后，下一步是 [将 vm 添加到实验室](devtest-lab-add-vm.md)。

