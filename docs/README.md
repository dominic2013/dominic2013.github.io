1 关于ctrl+shift+'+' 屏蔽快捷键
2 关于终端测试站搭建部署
3 关于我们模块
4 政经版菜单修改及添加
5 大版本更新时安装目录放到默认的C盘，应该放到安装目录下面（在推送大版本的时候，注释掉nsis脚本，防止安装程序默认选择配置的路径）
6 腾讯会议版本更新升级
7 下载链接放到OSS上面
8 消息列表页面无法打开系统消息 ok





1 关于bashpath路径的问题
2 关于引用外部js的问题
3 关于压力测试的问题
4 UMD规范
5 背景图片自适应
6 快捷键注册，+号用Plus代替
7 session storage和local storage 区别


    

1 版本升级时清理缓存目录的逻辑修改
2 第一次打开终端时，点击消息上面的选项卡切换时，消息面板消失的bug 
3 升级腾讯sdk版本为最新版
4 封装打开bv窗口函数
5 打开企微时，如果没有找到注册表，弹框的标题需要修改为可辨认的方式 ok
6 日志收集系统
7 electron前端公共组件
8 盖世汽车的URL嵌入 ok
9 关于金融版首页的政务URL嵌入问题
10 鼠标悬停标题栏工具按钮时变金色
11 OSS大版本推送测试 ok
12 点击父级菜单，同时打开两个菜单的问题 ok
13 sit集成环境搭建

# git相关问题
## .gitignore不生效
*在项目开发过程中个，一般都会添加 .gitignore 文件，规则很简单，但有时会发现，规则不生效。
原因是 .gitignore 只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。
那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交。*
```
git rm -r --cached .

git add .

git commit -m 'update .gitignore'
```
**这个只是提交到本地仓库，请放心修改**

# electron相关问题
## 如何在vscode中对electron主进程进行调试
*我用的electron-vue脚手架，但是其他的应该是差不多的*
1. 启动electron时，添加调试端口，对应于electron-vue脚手架中是.electron-vue目录中的dev-runner.js中electron启动参数添加5858端口
<https://www.electronjs.org/zh/docs/latest/tutorial/debugging-main-process#--inspectport>
```
  var args = [
    '--inspect=5858',
    path.join(__dirname, '../dist/electron/main.js')
  ]
```
2. 配置vscode调试器
<https://www.electronjs.org/zh/docs/latest/tutorial/debugging-vscode>
项目根目录新建.vscode目录，在目录中新建launch.json文件，并粘贴如下代码：
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "attach",
            "name": "Inspect Electron",
            "port": 5858,
            "sourceMaps": false,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null,
            "address": "localhost"
          }
    ],
    "compounds": [
      {
        "name": "Electron: All",
        "configurations": ["Electron: Main", "Electron: Renderer"]
      }
    ]
  }
```
3. 启动项目，启动调试器
  + 启动项目
  + 点击vscode左侧debug图标，左上角绿色小箭头右侧选中Inspect Electron，点击绿色箭头进行启动

4. 添加断点进行调试

*如果无法打断点，是一个白色圆圈，是因为不能直接在源码上加断点，而是需要在启动调试器之后，在vscode面板左下角找到断点，点击一下断点，找到右侧找到经过webpack转换之后的源码，需要在这个文件基础上进行调试*
