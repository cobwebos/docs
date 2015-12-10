<properties 
	pageTitle="手机版 Azure 验证器应用" 
	description="了解如何升级到最新版本的 Azure 验证器。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="08/24/2015" 
	wacn.date=""/>



# 过渡到新的 Azure 验证器应用

随着适用于 [Windows Phone](http://www.windowsphone.com/zh-cn/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 和 [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) 的 Azure 验证器应用的发布，旧的 Multi-Factor Authentication 应用将被取代。你可以继续使用 Multi-Factor Authentication 应用，不过，如果你决定过渡到新的 Azure 验证器应用，则本文可为你提供帮助。


## 如何过渡到新的 Azure 验证器应用 

**步骤 1：**安装 Azure 验证器。

![云](./media/multi-factor-authentication-azure-authenticator/home.png)

**步骤 2：**使用新应用激活帐户

首先，请确保准备好 QR 代码，或者为要添加到应用中的帐户准备好可随时输入的代码和 URL。

> [AZURE.NOTE]不知道怎样获取 QR 代码？ 请联系技术支持以获得帮助。
> 
> 无法使用新应用激活你的帐户？ 请联系技术支持。
>


准备好 QR 代码后，请启动应用。单击“+”。


![添加帐户](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

随后将会启动相机来扫描 QR 代码。如果无法扫描 QR 代码，你始终可以使用手动输入选项。

若要确认是否已成功激活帐户，请检查新帐户是否显示在帐户页上。


针对你要迁移到新应用的所有帐户执行此步骤。



**步骤 3：**通过手机卸载旧的 Multi-Factor Authentication 应用。

将所有帐户添加到新应用后，请通过手机卸载旧应用。

想知道如何从旧应用中删除个人帐户？ 请点击该帐户。随后会出现“删除”选项。

![删除帐户](./media/multi-factor-authentication-azure-authenticator/remove.png)

## 如何使用条形码扫描仪添加帐户



- 首先，请转到安全验证设置页。有关如何访问此页的详细信息，请参阅[更改你的安全设置](multi-factor-authentication-end-user-manage-settings.md)。

- 单击“配置”按钮。
 
![添加帐户](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 此时将显示一个包含条形码的屏幕。
  
![扫描条形码](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 现在，请打开 Azure 验证器应用，你应会转到帐户页。此处会显示你已设置的帐户列表。如果你要添加新的帐户，请单击加号 (+)。此时将打开扫描仪。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 扫描该条形码。 

![添加帐户](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 稍候片刻，帐户即会激活。

- 这就是所有的操作。你现在应会在帐户页上看到新的帐户。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## 如何手动添加 Azure 帐户

若要手动添加帐户，可以执行以下操作：

- 首先，请转到安全验证设置页。有关如何访问此页的详细信息，请参阅[更改你的安全设置](multi-factor-authentication-end-user-manage-settings.md)。

- 单击“配置”按钮。
 
![添加帐户](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 此时将显示一个包含条形码的屏幕。请注意条形码以下代码和 URL。
  
![扫描条形码](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 现在，请打开 Azure 验证器应用，你应会转到帐户页。此处会显示你已设置的帐户列表。如果你要添加新的帐户，请单击加号 (+)。此时将打开扫描仪。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 单击底部的“手动输入”按钮。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 输入显示条形码的同一页面上所提供的代码和 URL。此信息将会填入移动应用上的代码和 URL 框中。激活过程随后将会开始。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/manual.png)

- 稍候片刻，帐户即会激活。

- 这就是所有的操作。你现在应会在帐户页上看到新的帐户。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## 如何手动添加非 Azure 帐户

若要手动添加非 Azure 帐户，例如 Microsoft 帐户，可以执行以下操作：


- 可以通过扫描 QR 代码或输入机密密钥来手动添加非 Azure 帐户。
- 如果要手动输入机密密钥，请从与帐户关联的站点获取机密密钥。例如，在 Outlook.com 中转到帐户设置、你的安全设置，然后选择设置身份验证应用。需要选择“我无法扫描条形码以获取机密密钥”。
- 

![添加帐户](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- 打开 Azure 验证器应用，你应会转到帐户页。此处会显示你已设置的帐户列表。如果你要添加新的帐户，请单击加号 (+)。此时将打开扫描仪。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 扫描 QR 代码，或单击底部的“手动输入”按钮。如果扫描 QR 代码，则会跳过下一步，因为会立即开始激活。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 如果你手动输入机密密钥，请输入显示条形码的同一页面上所提供的帐户名和机密密钥。此信息将会填入移动应用上的代码和 URL 框中。激活过程随后将会开始。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/manual.png)

- 稍候片刻，帐户即会激活。你现在应会看到新增的帐户。

![添加帐户](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- 完成该过程：输入帐户以下代码（在本例中为 875 756），然后将代码输入提供机密密钥的页面上的框中，然后单击“下一步”。  

![添加帐户](./media/multi-factor-authentication-azure-authenticator/verify.png)

## 如何使用 TouchID 添加帐户
iOS 上的 Azure 验证器移动应用支持 Touch ID。Azure Multi-Factor Authentication 可让组织除了持有已注册的设备之外，还可以请求 PIN。使用这项新功能，设备已启用 Touch ID 的 iOS 用户就不需要再输入 PIN。设置完成后，用户只要扫描其指纹即可，而不需要输入 PIN 并点按“批准”。

设置 Touch ID 和 Azure 验证器的过程非常简单。你只需完成 PIN 的正常验证质询，如果你的设备支持 Touch ID，我们已自动为你设置。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

以后当系统要求你验证登录时，只需点按收到的推送通知并扫描指纹，而无需输入 PIN。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

<!---HONumber=Mooncake_1207_2015-->