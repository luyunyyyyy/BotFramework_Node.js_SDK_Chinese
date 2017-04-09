# 部署到Azure

目前有很多将你的bot部署到Azure的方法。你可以根据自己的情况进行选择。
我们展示了三种使你能快速上手的方法。

 - 先决条件
 - 想要从本地的git配置持续集成
   - 步骤一：安装Azure CLI
   - 步骤二：创建一个Azure 站点，并针对Node.js/git 来进行配置
   - 步骤三：将你的更改提交到git，并推送到你的Azure 站点
   - 步骤四：测试到bot连接
 - 想从github配置持续部署
   - 先决条件
   - 步骤一：获得一个github项目
   - 步骤二：创建一个Azure web 应用
   - 步骤三：在Azure上从你的Github配置持续部署
   - 步骤四：在应用设置中输入你的临时BotFramework 应用id和应用密码
   - 步骤五：测试到bot的连接
 - 我想通过Visual studio来部署
   - 步骤一：获取Bot Builder SDK 示例
   - 步骤二：打开 hello-AzureWebApp 示例，安装缺少的npm包，配置你的临时应用id和密码
   - 步骤三：发布到Azure
   - 步骤四：测试连接
 - 使用 Bot Framework模拟器来测试你部署在Azure上的bot
 - 下一步 

## 先决条件
 - 为了完成以下操作，你需要一个[Azure 订阅](URL 'https://azure.microsoft.com/en-us/free/')

 ## 想要从我本地的git配置持续集成

 在这部分，我们假定你已经完成了 [核心概念指导]()
 ### 步骤一： 安装Azure CLI
 打开一个命令提示符，进入你的bot所在的文件夹，运行以下命令：
 ``` shell
 npm install azure-cli -g
 ```
### 步骤二：创建一个Azure 站点，并针对Node.js/git 来进行配置
首先，登录到你的Azure账户。在你的命令行中输入以下命令，然后按照说明进行操作。
```
azure login
```
登录之后，输入下面的命令去 创建一个新站点，并针对Node.js/git 来配置
```
azure site creat --git <appname>
```
<appname>里填的是你想要创建的站点的名字。将会产生一个像appname.azurewebsites.net这样的url


### 步骤三：将你的更改提交到git，并推送到你的Azure 站点

```
git add .
git commit -m "<your commit message>"
git push azure master
```

你将会被提示输入你的部署凭据。如果你没有，你可以按照下面的步骤来在Azure Portal（Azure 门户）中配置：
  1. 访问[Azure Portal](http://portal.azure.com/)
  2. 点击你刚刚创建的站点。
  3. 在Publishing（中文是“部署”）部分，点击 Deployment credentials（部署凭据），输入一个用户名和密码，然后保存。
  ![png](https://docs.botframework.com/en-us/images/builder/publishing-your-bot-deployment-credentials.png)

  4. 回到你刚刚的命令行，输入部署凭据。
  5. 你的bot将会部署到你的Azure 站点。
### 步骤四：测试到bot连接
[使用bot模拟器来测试你的bot](https://docs.botframework.com/en-us/node/builder/guides/deploying-to-azure/#test-your-azure-bot-with-the-bot-framework-emulator)


## 想从github配置持续部署

让我们来使用sample bot为你的基于Node.js的bot来作为一个新手示例吧。

*注意：在下面的示例中，在所有设置和URL里面，将"echobotsample"替换成你的bot ID
### 先决条件
 - 获得一个[Github](http://github.com/)账号

### 步骤一：获得一个github项目

在这个教程中，我们将使用[sample echobot](https://github.com/fuselabs/echobot) 项目。你需要fork这个项目。


### 步骤二：创建一个Azure web 应用

![pic](https://docs.botframework.com/en-us/images/builder/azure-create-webapp.png?raw=true)

### 步骤三：在Azure上从你的Github配置持续部署

你将被要求授权Azure访问你的Github项目，然后选择一个你的分支来部署。
![pic](https://docs.botframework.com/en-us/images/builder/azure-deployment.png?raw=true)

通过访问你的web 应用来确认部署已经完成。

### 步骤四：在应用设置中输入你的临时BotFramework 应用id和应用密码
### 步骤五：测试到bot的连接
## 我想通过Visual studio来部署
### 步骤一：获取Bot Builder SDK 示例
### 步骤二：打开 hello-AzureWebApp 示例，安装缺少的npm包，配置你的临时应用id和密码
### 步骤三：发布到Azure
### 步骤四：测试连接
## 使用 Bot Framework模拟器来测试你部署在Azure上的bot
## 下一步 