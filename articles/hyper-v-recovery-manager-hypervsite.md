<properties 
	pageTitle="教程：在本地 Hyper-V 站点与 Azure 之间设置保护" 
	description="Azure Site Recovery 可以在本地 Hyper-V 站点与 Azure 之间协调虚拟机的复制、故障转移和恢复。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

# 教程：在本地 Hyper-V 站点与 Azure 之间设置保护


<h2><a id="overview" name="overview" href="#overview"></a>概述</h2>
<p>Azure Site Recovery 可在许多部署方案中协调虚拟机的复制、故障转移和恢复，为业务和工作负载连续性策略发挥作用。<p>

本教程介绍如何部署 Azure Site Recovery，以保护本地站点装有 Windows Server 2012 R2 的 Hyper-V 服务器上运行的工作负载。Hyper-V 服务器上的虚拟机使用 Hyper-V 复制来复制到 Azure。如果你在办公室或分支机构部署了 Hyper-V 服务器但未部署 System Center VMM，则此部署将特别有用。


<LI>本教程将使用最快的部署路径以及最小和默认的设置。
若要了解完整部署说明，请阅读<a href="https://msdn.microsoft.com/zh-cn/library/azure/dn879140.aspx">规划</a>和<a href="https://msdn.microsoft.com/zh-cn/library/azure/dn879142.aspx">部署</a>指南。</LI>
<LI>若要了解其他 Azure Site Recovery 部署方案，请参阅 <a href="www.windowsazure.cn/documentation/articles/hyper-v-recovery-manager-overview/">Azure Site Recovery 概述</a>。</LI>
<LI>如果在使用本教程期间遇到问题，请查阅 Wiki 文章 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery：常见错误情况和解决方法</a>或者在 <a href="https://social.msdn.microsoft.com/Forums/azure/zh-CN/home?forum=windowsazurezhchs">Azure 恢复服务论坛</a>上发布你的问题。</LI>
</UL>

</div>

<h2><a id="before" name="before" href="#before"></a>先决条件</h2>
<div class="dev-callout"> 
<P>在开始之前，请确保做好了一切准备工作。</P>

<UL>
<LI><b>Azure 帐户</b> - 你需要一个 Azure 帐户。如果没有帐户，请参阅 <a href="http://www.windowsazure.cn/pricing/1rmb-trial/">Azure 免费试用版</a>。可在 <a href="http://www.windowsazure.cn/pricing/details/site-recovery/">Azure Site Recovery Manager 定价详细信息</a>中获取定价信息。</LI>
<LI><b>Hyper-V</b> - 源本地站点中至少有一台运行 Windows Server 2012 R2 的服务器，并且源站点中存在 Hyper-V 角色。Hyper-V 服务器应包含一个或多个虚拟机。Hyper-V 服务器应直接或通过代理连接到 Internet。</LI>
<LI><b>虚拟机</b> - 要保护的虚拟机应符合虚拟机的 Azure 先决条件。请参阅<a href="https://msdn.microsoft.com/zh-cn/library/azure/dn469078.aspx#BKMK_E2A">虚拟机支持</a>。</LI>

</UL>
<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>教程步骤</h2>
在确认符合先决条件后，执行以下操作：
<UL>
<LI><a href="#vault">步骤 1：创建保管库</a> - 创建 Azure Site Recovery 保管库。</LI>
<LI><a href="#site">步骤 2：创建 Hyper-V 站点</a> - 创建 Hyper-V 站点作为包含想要保护的虚拟机的所有 Hyper-V 服务器的逻辑容器。</LI>
<LI><a href="#download">步骤 3：准备 Hyper-V 服务器</a> - 生成注册密钥并下载提供程序安装程序文件。你需要在站点中的每个 Hyper-V 服务器上运行该文件，并选择用于在保管库中注册服务器的密钥。</LI>
<LI><a href="#resources">步骤 4：准备资源</a> - 创建一个 Azure 存储帐户用于存储复制的虚拟机。</LI>
<LI><a href="#protectiongroup">步骤 5：创建和配置保护组</a> - 创建一个保护组，并向其应用保护设置。将会向你添加到组中的每个虚拟机应用保护设置。</LI>
<LI><a href="#enablevirtual">步骤 6：为虚拟机启用保护</a> - 通过将虚拟机添加到保护组，来为这些虚拟机启用保护。</LI>
<LI><a href="#recovery plans">步骤 7：测试部署</a> - 针对虚拟机运行测试故障转移。</LI>

</UL>




<a name="vault"></a> <h3>步骤 1：创建保管库</h3>

1. 登录到[管理门户](https://manage.windowsazure.cn)。


2. 依次展开"数据服务"、"恢复服务"，然后单击"Site Recovery 保管库"<b></b><b></b><b></b>。


3. 依次单击"新建"、"快速创建"<b></b><b></b>。
	
4. 在"名称"<b></b>中，输入一个友好名称以标识此保管库。

5. 在"区域"<b></b>中，为保管库选择地理区域。若要查看支持的区域，请参阅 <a href="http://www.windowsazure.cn/pricing/details/site-recovery/">Azure Site Recovery 定价详细信息</a>中的"上市地区"。

6. 单击"创建保管库"<b></b>。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>检查状态栏以确认保管库已成功创建。该保管库将在主"恢复服务"页上列为"活动"<b></b>。</P>

<a name="site"></a> <h3>步骤 2：创建 Hyper-V 站点</h3>

1. 在"恢复服务"页中，单击该保管库以打开"快速启动"页面。也可随时使用该图标打开"快速启动"。

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. 在下拉列表中，选择"本地 Hyper-V 站点与 Azure 之间"。

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. 在"创建 Hyper-V 站点"中，单击"创建 Hyper-V 站点"。指定站点名称并保存。

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h3>步骤 3：准备 Hyper-V 服务器</h3>
	

1. 在"准备 Hyper-V 服务器"中，单击"下载注册密钥"文件。
2. 在"下载注册密钥"页上，单击站点旁边的"下载"。将密钥下载到可由 Hyper-V 服务器方便访问的安全位置。该密钥生成后，有效期为 5 天。

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. 单击"下载该提供程序"<b></b>以获取最新版本。
5. 在想要注册到保管库中的每个 Hyper-V 服务器上运行该文件。该文件将安装两个组件：
	- **Azure Site Recovery 提供程序** - 处理 Hyper-V 服务器与 Azure Site Recovery 门户之间的通信和协调。
	- **Azure 恢复服务代理** - 处理源 Hyper-V 服务器上运行的虚拟机与 Azure 存储空间之间的数据传输。
6. 在"Microsoft Update"中，你可以选择获取更新。启用此设置后，将会根据你的 Microsoft Update 策略安装提供程序和代理更新。

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. 在"安装"中，指定要将提供程序和代理安装在 Hyper-V 服务器上的哪个位置。

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. 完成安装后，请继续设置以在保管库中注册服务器。

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. 在"Internet 连接"页，指定提供程序如何连接到 Azure Site Recovery。选择"使用默认系统代理设置"以使用服务器上配置的默认 Internet 连接设置<b></b>。 

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. 在"保管库设置"页上，单击"浏览"以选择密钥文件。指定 Azure Site Recovery 订阅、保管库名称和 Hyper-V 服务器所属的 Hyper-V 站点。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. 注册过程将开始在保管库中注册服务器。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. 完成注册后，Azure Site Recovery 将检索 Hyper-V 服务器中的元数据，该服务器将显示在保管库中"服务器"页上的"Hyper-V 站点"选项卡上。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h3><a id="resources"></a>步骤 4：准备资源</h3>


1. 在"快速启动"页上的"准备资源"中，选择"创建存储帐户"以创建一个 Azure 存储帐户（如果你没有的话）。该帐户应已启用地域复制。该帐户应与 Azure Site Recovery 保管库位于同一区域，并与同一订阅相关联。

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h3><a id="protectiongroup"></a>步骤 5：创建和配置保护组</h3>

<p>保护组会将具有相同保护设置的虚拟机组合在一起。可以将保护设置应用到保护组，这些设置将应用到你在组添加的所有虚拟机。</p>
1. 在"创建和配置保护组"中，单击"创建保护组"。如果缺少任何必备组件，系统将发出一条消息，你可以单击"查看详细信息"来了解详细信息。

2. 在"保护组"选项卡中，添加一个保护组。指定名称、 源 Hyper-V 站点、目标 **Azure**、你的 Azure Site Recovery 订阅名称和 Azure 存储帐户。

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. 在"复制设置"中保留默认设置。

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h3><a id="enablevirtual"></a>步骤 6：启用虚拟机保护</h3>
<p>通过将虚拟机添加到保护组，来为这些虚拟机启用保护。</p>

1. 在保护组的"计算机"选项卡上，单击"将虚拟机添加到保护组以启用保护"<b></b><b></b>。
2. 在"启用虚拟机保护"页上，选择你要保护的虚拟机。 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

随后将开始运行"启用保护"作业。你可以在"作业"选项卡上跟踪进度。在"完成保护"作业运行之后，虚拟机就可以进行故障转移了。启用保护并完成初始复制后，你可以在 Azure 中查看虚拟机。

在 Azure Site Recovery 中，你可以通过"受保护的项">"保护组">"protectiongroup_name">"虚拟机"查看受保护的虚拟机。 


<h3><a id="recoveryplans"></a>步骤 7：测试部署</h3>

测试部署，以便在不影响生产环境的隔离网络中模拟故障转移和恢复机制。为此，你需要为受保护的虚拟机运行测试故障转移。

1. 在"受保护的项">"保护组">"protectiongroup_name">"虚拟机"中，选择要故障转移的虚拟机，然后单击"测试故障转移"。
2. 在"确认测试故障转移"页上，选择"无"。 

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. 可以在"作业"选项卡上跟踪故障转移进度和状态。
4. 当故障转移达到"完成测试"阶段时，请按如下所述完成验证：
	- 故障转移后，在 Azure 门户中查看副本虚拟机。验证虚拟机是否启动成功。
	- 如果你已设置为从本地网络访问虚拟机，则可以启动与虚拟机的远程桌面连接。
	- 单击"完成测试"以完成测试。
	- 单击"说明"以记录并保存与测试故障转移相关联的任何观测结果。
	- 单击"测试故障转移已完成"。清理测试环境以自动关闭电源，并删除测试虚拟机。

<h2><a id="runtest" name="runtest" href="#runtest"></a>监视活动</h2>
<p>你可以使用"作业"选项卡和"仪表板"来查看和监视由 Azure Site Recovery 保管库执行的主作业，包括为云配置保护、为虚拟机启用和禁用保护、运行故障转移（有计划的、非计划的或测试），以及提交非计划故障转移<b></b><b></b>。</p>

<p>从"作业"选项卡中，你可以查看作业，深入了解作业详细信息和错误，运行作业查询以检索符合特定条件的作业，将作业导出到 Excel，以及重新启动失败的作业。<b></b></p>

<p>从"仪表板"中，你可以下载提供程序和代理安装文件的最新版本，获取保管库的配置信息，查看其保护是由保管库管理的虚拟机的数量，查看最近的作业，管理保管库证书，以及重新同步虚拟机<b></b>。</p>

<p>有关与作业和仪表板交互的详细信息，请参阅<a href="https://msdn.microsoft.com/zh-cn/library/dn788906.aspx">操作和监视指南</a>。</p>

<h2><a id="next" name="next" href="#next"></a>后续步骤</h2>
<UL>
<LI>若要在完全的生产环境中规划和部署 Azure Site Recovery，请参阅 <a href="https://msdn.microsoft.com/zh-cn/library/azure/dn879140.aspx">Azure Site Recovery 规划指南</a>和 <a href="https://msdn.microsoft.com/zh-cn/library/azure/dn879142.aspx">Azure Site Recovery 部署指南</a>。</LI>
<LI>如有问题，请访问 <a href="https://social.msdn.microsoft.com/Forums/azure/zh-CN/home?forum=windowsazurezhchs">Azure 恢复服务论坛</a>。</LI> 
</UL>
