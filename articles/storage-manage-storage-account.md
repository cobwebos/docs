<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="如何管理" pageTitle="如何管理 Windows Azure 上的存储帐户" metaKeywords="Azure 管理存储帐户, 存储帐户管理门户, 存储帐户地域复制, Azure 地域复制, Azure 访问密钥" description="了解如何使用管理门户在 Windows Azure 中管理存储帐户。" metaCanonical="" services="storage" documentationCenter="" title="如何管理存储帐户" authors=""  solutions="" writer="tysonn" manager="" editor=""  />



<h1><a id="managestorageaccounts"></a>如何管理存储帐户</h1>

##目录##

* [如何管理存储帐户复制](#georeplication)
* [如何查看、复制和重新生成存储访问密钥](#regeneratestoragekeys)
* [如何删除存储帐户](#deletestorageaccount)

<h2><a id="georeplication"></a>如何管理存储帐户复制</h2>

有三个选项可用于复制您的存储帐户：

-	地域冗余复制。默认情况下，为您的存储帐户启用地域冗余复制。使用地域冗余复制，您的数据将复制到辅助地理位置，这样就能够在主要位置发生重大灾难时将故障转移到该位置。辅助位置与主要位置位于同一区域，但距离主要位置数百英里。

-	地域冗余复制。使用本地冗余复制，将在同一数据中心复制您的存储帐户数据三次。本地冗余复制将以优惠价提供。
	
	请注意，如果您为存储帐户指定本地冗余复制，但稍后决定启用地域冗余复制，那么在您将现有数据复制到辅助位置时将向您收取一次性数据费用。

- 读取访问地域冗余复制（仅限预览）。读取访问地域冗余复制将数据复制到辅助地理位置，并且还提供在该辅助位置对您的数据的读取访问权限。通过读取访问地域冗余复制，您可以在一个位置变得不可用时从主位置或辅助位置访问您的数据。

	<div class="dev-callout"> 
	<b>注意</b>

	<p>若要使用读取访问地域冗余复制（尽管它现在处于预览状态），您必须手动请求为您的订阅启用该功能。请访问 <a href="https://account.windowsazure.com/PreviewFeatures">Windows Azure 预览功能</a>页，请求针对您的订阅的读取访问地域冗余复制。有关读取访问地域冗余复制的详细信息，请参阅 <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx">Windows Azure 存储团队博客</a>。</p>

	<p>如果没有将读取访问地域冗余复制作为针对您的订阅的预览功能启用，将禁用为您的存储帐户选择它的选项。</p>
	</div>

有关存储帐户复制的定价信息，请参见[存储定价详细信息](http://www.windowsazure.com/zh-cn/pricing/details/storage/)。

### 为存储帐户指定复制设置###

1. 在 [Windows Azure 管理门户](https://manage.windowsazure.com)中，单击“存储”，然后单击存储帐户名称以显示仪表板。

2. 单击“配置”。

3. 在“复制”字段中，选择您的存储帐户所需的复制类型。

4. 单击“保存”，在系统提示时确认您的选择。


<h2><a id="regeneratestoragekeys"></a>如何查看、复制和重新生成存储访问密钥</h2>
当您创建存储帐户时，Windows Azure 将生成两个 512 位存储访问密钥，用于在用户访问该存储帐户时对其进行身份验证。通过提供两个存储访问密钥，Windows Azure 使您能够在不中断存储服务的情况下重新生成用于访问该服务的密钥。

在[管理门户](http://manage.windowsazure.com)中，可使用仪表板或“存储”页上的“管理密钥”查看、复制和重新生成用于访问 Blob、表和队列服务的存储访问密钥。

### 复制存储访问密钥###

您可以使用“管理密钥”复制要在连接字符串中使用的存储访问密钥。连接字符串需要在进行身份验证时使用存储帐户名称和密钥。有关配置连接字符串以访问 Windows Azure 存储服务的信息，请参阅[配置连接字符串](http://msdn.microsoft.com/zh-cn/library/ee758697.aspx)。

1. 在[管理门户](http://manage.windowsazure.com)中，单击“存储”，然后单击存储帐户名称以打开仪表板。

2. 单击“管理密钥”。

 	“管理访问密钥”将打开。

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)

 
3. 若要复制存储访问密钥，请选择密钥文本。然后右键单击，并单击“复制”。

### 重新生成存储访问密钥###
您应定期更改存储帐户的访问密钥以帮助确保存储连接更安全。将分配两个访问密钥，以便在您重新生成其中一个访问密钥时，始终能够使用另一个访问密钥连接到存储帐户。

<div class="dev-callout"> 
    警告
    <p>重新生成访问密钥会影响虚拟机、媒体服务以及任何依赖于存储帐户的应用程序。
    </p> 
    </div>

虚拟机 - 如果您的存储帐户包含任何正在运行的虚拟机，则在重新生成访问密钥后必须重新部署所有虚拟机。若要避免重新部署，请在重新生成访问密钥之前关闭虚拟机。
 
媒体服务 - 如果您的媒体服务依赖于存储帐户，则必须在重新生成密钥后将访问密钥与媒体服务重新同步。
 
应用程序 - 如果您具有使用存储帐户的 Web 应用程序或云服务，则重新生成密钥将失去连接，除非您滚动使用密钥。过程如下：

1. 更新应用程序代码中的连接字符串以引用存储帐户的辅助访问密钥。

2. 为您的存储帐户重新生成主访问密钥。在[管理门户](http://manage.windowsazure.com)中，从仪表板或“配置”页，单击“管理密钥”。单击主访问密钥下的“重新生成”，然后单击“是”确认您要生成新密钥。

3. 更新代码中的连接字符串以引用新的主访问密钥。

4. 重新生成辅助访问密钥。


<h2><a id="deletestorageaccount"></a>如何删除存储帐户</h2>

若要删除不再使用的存储帐户，请使用仪表板或“配置”页上的“删除”。“删除”操作将删除整个存储帐户，包括帐户中的所有 Blob、表和队列。

<div class="dev-callout">
	<b>警告</b>
	<p>无法从删除的存储帐户中还原内容。请确保
	在删除帐户之前对要保存的所有内容进行备份。
	</p>
	<p>
	如果您的存储帐户包含用于 Windows Azure 虚拟机的
	任何 VHD 文件或磁盘，则必须删除使用这些 VHD 文件的任何
	映像和磁盘，然后才能删除存储帐户。首先，如果虚拟机正在运行，则停止运行，然后将其删除。若要删除磁盘，请导航到“磁盘”选项卡并删除存储帐户中包含的所有磁盘。若要删除映像，请导航到“映像”选项卡并删除存储帐户中存储的任何映像。
	</p>
</div>


1. 在[管理门户](http://manage.windowsazure.com)中，单击“存储”。

2. 单击存储帐户条目中名称之外的任何位置，然后单击“删除”。

	 -或-

	单击存储帐户的名称以打开仪表板，然后单击“删除”。

3. 单击“是”确认您要删除存储帐户。

