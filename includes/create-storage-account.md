若要执行存储操作，您需要一个 Windows Azure 存储帐户。可
通过以下步骤创建存储帐户。（也可以
[使用 REST API]创建存储帐户。）

1. 登录到 [Windows Azure 管理门户]。

2. 在导航窗格的底部，单击“+新建”。

	![+新建][plus-new]

3. 依次单击“数据服务”、“存储”和“快速创建”。

	![“快速创建”对话框][quick-create-storage]

4. 在 URL 中，键入要在存储帐户的 URI 中使用的子域名称。输入的名称可包含 3-24 个小写字母和数字。此值将成为用于对订阅的 Blob、
    队列或表资源进行寻址的 URI
    中的主机名。

5. 选择要在其中查找存储的区域/地缘组。如果要使用 Windows Azure 应用程序中的存储，
    请选择要在其中部署该应用程序的区域。

6. 还可以选择启用地域复制。

6. 单击“创建存储帐户”。

[使用 REST API]: http://msdn.microsoft.com/zh-cn/library/windowsazure/hh264518.aspx
[Windows Azure 管理门户]: http://manage.windowsazure.com
[plus-new]: ./media/create-storage-account/plus-new.png
[quick-create-storage]: ./media/create-storage-account/quick-storage-2.png

