1. 登录到 Azure 经典门户。

2. 在门户的左侧导航窗格中，单击“服务总线”。

3. 在门户的下方窗格中，单击“创建”。

    ![选择“创建”][select-create]
   
4. 在“添加新命名空间”对话框中，输入命名空间名称。系统会立即检查该名称是否可用。

    ![命名空间名称][namespace-name]
  
5. 确保该命名空间名称可用之后，选择将托管命名空间的国家或地区。

6. 将对话框中的其他字段保留其默认值（“消息传送”和“标准层”），然后单击“确定”复选标记。系统现已创建命名空间并已将其启用。您可能需要等待几分钟，因为系统将为您的帐户配置资源。
 
    ![已成功创建][created-successfully]

###获取凭据
1. 在左侧导航窗格中，单击“服务总线”节点以显示可用命名空间的列表：
&nbsp;
    ![选择服务总线][select-service-bus]
 
2. 从显示的列表中选择刚刚创建的命名空间：
 
    ![选择命名空间][select-namespace]
 
3. 单击“连接信息”。

    ![连接信息][connection-information]
  
4. 在“访问连接信息”窗格中，找到包含 SAS 密钥和密钥名称的连接字符串。

    ![访问连接信息][access-connection-information]
  
5. 记下该密钥或将其复制到剪贴板。

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.cn
<!---HONumber=Mooncake_0718_2016-->