
### <a name="create-an-application-express"></a>创建应用程序（快速）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：

1.  通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)注册应用程序
2.  输入应用程序的名称和你的电子邮件
3.  确保选中“指导式设置”选项
4.  按照说明获取应用程序 ID，并将其粘贴到代码中

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到解决方案（高级）

1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入应用程序的名称和你的电子邮件 
3. 确保取消选中“指导式设置”选项
4.  单击 `Add Platform`，然后选择 `Web`
5. 将重定向 URL 添加到应用程序中。 重定向 URL 是基于 Web 服务器的 `index.html` 页面 URL
6. 单击“保存”

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>使用 SSL 获取重定向 URL 的 Visual Studio 说明
> 如果使用的是 Visual Studio，请按照以下说明将项目配置为使用 SSL，然后使用 SSL URL 配置应用程序的注册信息：
> 1.    在解决方案资源管理器中，选择项目并查看`Properties`窗口（如果你看不到“属性”窗口，请按 F4）
> 2.    将 `SSL Enabled` 更改为 `True`
> 3.    将 `SSL URL` 中的值复制到剪贴板：<br/> ![项目属性](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    选择 `Project` 菜单，然后选择 `{Project} Properties...`（其中 {Project} 是项目的名称）
> 5.    打开 `Web` 选项卡
> 6.    将 `SSL URL` 的值粘贴到 `Project Url` 字段
> 7.    切换回应用程序注册门户，并将 `Redirect URL` 中的值粘贴为重定向 URL，然后单击“保存”


#### <a name="configure-your-javascript-spa-application"></a>配置 JavaScript SPA 应用程序

1.  创建包含应用程序注册信息且名为 `msalconfig.js` 的文件。 如果使用的是 Visual Studio，请选择项目（项目根文件夹），然后右键单击并选择：`Add` > `New Item` > `JavaScript File`。 将其命名为 `msalconfig.js`
2.  将以下代码添加到 `msalconfig.js` 文件：

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
用刚注册的应用程序 ID 替换 <code>Enter_the_Application_Id_here</code>
</li>
</ol>