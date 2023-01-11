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

## 如何在生产环境中对electron程序进行调试
```
/**
 * 终端启动时使用远程端口
 * xxx.exe --remote-debugging-port=9999
 *
 * 浏览器打开远程控制台
 * http://localhost:9999/
 *
 * 介绍
 * https://chromedevtools.github.io/devtools-protocol/
 */
```
## 加载外部c++程序时，加载node-gyp生成的.node接口文件找不到文件的问题
*electron-vue脚手架中配置webpack.main.config.js中的module*
```
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: 'esbuild-loader'
      },
      {
        test: /\.node$/,
        //这个地方需要使用ext-loader，不然会报找不到文件
        loader: 'native-ext-loader',
        options: {
          //basePath对应的是最终打包的js文件对应的目录，通过相对路径找到.node所在的目录
          basePath: ['..','..','..','..','meeting/output/win/x64']
        }
      }
    ]
  }
```
引用.node文件的时候也是用相对路径
```
require('../../../../meeting/output/win/x64/wemeet_electron_sdk.node');
```
## electron中屏蔽快捷键如果快捷键中带有+号之类的冲突字符如何处理
<https://www.electronjs.org/zh/docs/latest/tutorial/keyboard-shortcuts>
<http://www.electronjs.org/zh/docs/latest/api/accelerator>

*官网是使用+号连接组合，但是如果快捷键本身就带有+号就不行了*
```
      //屏蔽ctrl+shift+'+'号
      globalShortcut.register(`ctrl+shift+Plus`, () => {
        console.log('用户禁止放大')
        return false;
      })
```



# uos 开启root权限，在系统设置中登录并进入开发者模式，修改root密码 
- sudo passwd root

# 无法远程连接，首先需要安装sshd服务
- apt install openssh-server
- debian 开启SSH
1、修改sshd_config文件，命令为：vi /etc/ssh/sshd_config 

2、将#PasswordAuthentication no的注释去掉，并且将NO修改为YES  //我的kali中默认是yes

3、将#PermitRootLogin yes的注释去掉 //我的kali中默认去掉了注释

4、启动SSH服务，命令为：/etc/init.d/ssh start // 或者service ssh start

5、验证SSH服务状态，命令为：/etc/init.d/ssh status

6. 添加开机自启动   update-rc.d ssh enable
7  重启ssh服务   service ssh restart


# 安装nodejs
[https://blog.csdn.net/lh155136/article/details/111194424]

# 安装yarn
`
sudo apt remove cmdtest
sudo apt remove yarn
 
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
 
sudo apt update
sudo apt install yarn
yarn -v
yarn	
`

# 设置npm,yarn仓库源
yarn config set registry https://registry.npm.taobao.org/

# cross-env not found
yarn global add cross-env

# /home/rathboma/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/bin/ruby: line 6: /home/rathboma/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/bin.real/ruby: cannot execute binary file: Exec format error
- apt-get install ruby-dev
- gem sources --remove http://rubygems.org/
- gem sources -a http://mirrors.aliyun.com/rubygems/
- gem install fpm

- 删除 $HOME/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/bin.real/ruby 文件
- ln -s /usr/bin/ruby $HOME/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/bin.real/

# 一直提示resources/app.asar" does not exist. Seems like a wrong configuration.  failedTask
  出现这种情况，请检查electron-vue package.json中的build命令是否设置了build.json配置

#  snapcraft is not installed, please: sudo snap install snapcraft --classic
- apt install snapd
- snap install snapcraft --classic

# Mount snap "snapcraft" (8532) (snap "snapcraft" assumes unsupported features: snapd2.39 (try to update snapd and refresh the core snap))
- 检查build.json中 linux的target为 deb 而不是空

# /root/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/lib/ruby/2.3.0/i686-linux/rbconfig.rb:9:in `<module:RbConfig>': ruby lib version (2.3.1) doesn't match executable version (2.5.5) (RuntimeError)
- 利用rvm进行ruby的版本管理
  `
	gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
	curl -sSL https://get.rvm.io | bash -s stable
	source ~/.bashrc
	echo "ruby_url=https://cache.ruby-china.com/pub/ruby" > /usr/local/rvm/user/db
	rvm pkg install openssl
	rvm pkg install openssl
	rvm reinstall 2.3.0 --with-openssl-dir=/usr/local/rvm/usr
	## 删除原来的软链
	rm /root/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/bin.real
	ln -s /usr/local/rvm/rubies/ruby-2.3.0/bin/ruby /root/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/bin.real/
  `

# 安装rvm 报错 Error running 'requirements_debian_libs_install g++ bison libffi-dev libgdbm-dev libncurses5-dev libsqlite3-dev libyaml-dev zlib1g-dev libgmp-dev libreadline-dev libssl1.0-dev',please read /usr/local/rvm/log/1667961828_ruby-2.3.0/package_install_g++_bison_libffi-dev_libgdbm-dev_libncurses5-dev_libsqlite3-dev_libyaml-dev_zlib1g-dev_libgmp-dev_libreadline-dev_libssl1.0-dev.logRequirements installation failed with status: 100.
- 
  apt-get install rvm
  apt-get install aptitude
  aptitude install libssl-dev
  
# Error running 'requirements_debian_libs_install libssl1.0-dev' libssl1.0-dev 没有可安装候选，apt install openssl
- 需要安装libssl1.0, 更新apt仓库数据源 
  `
  wget -O /etc/apt/sources.list https://repo.huaweicloud.com/repository/conf/Ubuntu-Ports-bionic.list
  apt-get update
  apt-cache policy libssl1.0-dev
  rvm install ruby-2.3.1
  `
# 切换ruby版本
 - rvm list
 - rvm use ruby-2.3.1 --default
# 2.3.0/rubygems/specification.rb:17:in `require': /root/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/lib/ruby/2.3.0/i686-linux/stringio.so: wrong ELF class: ELFCLASS32 - /root/.cache/electron-builder/fpm/fpm-1.9.3-2.3.1-linux-x86/lib/ruby/lib/ruby/2.3.0/i686-linux/stringio.so (LoadError)

# 直接使用build:dir命令编译绿色版本

# linux中的抓包工具
- 

# 通过npm全局安装软件，执行时提示无法找到命令
  - 任意目录执行 
  `
	echo -e "export PATH=$(npm prefix -g)/bin:$PATH" >> ~/.bashrc && source ~/.bashrc
  `



  



设置author的时候，需要同时使用用户名加邮箱



如何设置package编译linux


cross-env: Permission denied
npm rebuild
npm run prd



arm64/app-builder EACCES  failedTask=build stackTrace=Error: spawn
需要先进行npm build进行编译





• building        target=deb arch=arm64 file=build/sfc-connect_0.8.34_arm64.deb
  ⨯ Please specify project homepage, see https://electron.build/configuration/configuration#Metadata-homepage

Please specify author 'email' in the application package.json

See https://docs.npmjs.com/files/package.json#people-fields-author-contributors

It is required to set Linux .deb package maintainer. Or you can set maintainer in the custom linux options.
(see https://www.electron.build/configuration/linux).

解决: package.json中 author需要填写名称加email的形式 南方财经全媒体集团,<179546959@qq.com>



⨯ Please specify project homepage, see https://electron.build/configuration/configuration#Metadata-homepage  failedTask=build stackTrace=Error: Please specify project homepage, see 

解决： package.json设置homepage  "homepage": "https://sfconnect.sfccn.com",



如何在windows10中虚拟ARM架构的UOS操作系统
1.安装后无法上网的问题，需要安装tap-windows 虚拟网卡，通过桥接的形式，让虚拟机可以上网
https://blog.csdn.net/u013250169/article/details/120002629

# 如何查看全局安装的路径
- npm config get prefix


# 打包流程
## 首先需要先把使用electron-builder进行打包
  yarn:build:dir
## 安装electron-packager (这一步是为了生成符合debian打包格式的文件目录规范)
- npm install electron-packager -g
- 做软链接 ln -s /usr/local/nodejs/bin/electron-packager /usr/bin/electron-packager
   进入build/linux-arm64-unpacked/resources/app目录中执行 `electron-packager . --ignore="src|build.*|server|rootLib|config|devTools"` 会生成 sfc-connect-linux-arm64这个目录,这个是绿色版程序包
## 安装 electron-installer-debian
   npm install electron-installer-debian -g
   ln -s /usr/local/nodejs/bin/electron-installer-debian  /usr/bin/electron-installer-debian

## 打包为deb格式的包
-  electron-installer-debian --src sfc-connect-linux-arm64/   --dest dist/installers/ --arch arm64
   生成dist目录,d installers中可以找到deb格式的包

# 统信操作系统无法定位安装包
- https://blog.csdn.net/weixin_42328170/article/details/107411026
  添加数据源，更新数据源
  
# libx11-dev : 依赖: libx11-6 (= 2:1.6.4-3ubuntu0.4) 但是 2:1.6.12-2 已安装
- 出现这种情况，直接 apt-get install libx11-6=2:1.6.4-3ubuntu0.4

# libx11-dev : 依赖: libxau-dev (>= 1:1.0.0-1) 但是它将不会被安装
- 直接安装最新版 apt-get install libxau-dev

# ./src/mouse.c:12:11: fatal error: X11/extensions/XTest.h: 没有那个文件或目录
- apt-get install xorg-dev

# 重装uos系统，一直卡在logo界面
- 需要在启动的时候按e,然后在以linux开头的那一行命令最后添加 nomodeset,按f10保存启动

# 统信商店如何按照规范进行打包
- 首先我已经通过electron的打包工具打好了deb的包，可以先进行解压，把目录组织好再进行压缩
  dpkg-deb -b orig xxx.deb<---压缩
  dpkg-deb -R xxx.deb orig<--解压
  
# [6513:1125/170219.895373:FATAL:setuid_sandbox_host.cc(158)] The SUID sandbox helper binary was found, but is not configured correctly. Rather than run without sandboxing I'm aborting now. You need to make sure that /home/sfc-connect/sfc-connect-uos/build/linux-unpacked/chrome-sandbox is owned by root and has mode 4755.
- 利用root用户给打完包之后的根目录中的chrome-sandbox文件添加suid, chmod 4755 chrome-sandbox

# uos系统待机后无法唤醒
- 直接设置uos的电源选项为从不休眠，如果是ssh客户端断开，可以设置ssh的客户端休眠规则

# 关于arm64架构下,electron18,node16 无法手动编译成功的问题(github issue: https://github.com/wilix-team/iohook/pull/363)
- 下载官网的源码，通过npm run build会报错，网上找了一个分支可以正常的编译出来我要的版本
  `
    $ git clone https://github.com/ykhwong/iohook --branch=update-electron-v15j
	$ cd iohook
	$ npm i --ignore-scripts
	$ node build.js --runtime electron --version 18.0.1 --abi 103 --msvs_version=2022 --upload=false
  `

# uiohook 如何在arm平台上生
- 在uos arm64平台上无法正常运行uiohook，需要自己进行编译
  `
  cd node_modules/uiohook-napi/
  yarn install
  ## 如果执行后无法正常编译，可以强制执行编译
  npm install --build-from-source
  `
# uiohook 在uos20上编译出错 libuiohook/src/x11/input_hook.c:43:10: fatal error: X11/extensions/Xrandr.h: 没有那个文件或目录
- apt install libxrandr-dev

# include <X11/extensions/record.h>
- apt install libxtst-dev
# fatal error: X11/Intrinsic.h X11/Intrinsic.h
- apt-get install libxt-dev

# 龙芯架构无法使用electron-builder，因为不支持龙芯架构，需要使用electron-packager进行打包
- [http://docs.loongnix.cn/electron/doc/list/03.%E4%BD%BF%E7%94%A8electron-packager%E6%89%93%E5%8C%85%E7%A8%8B%E5%BA%8F.html]
- export ELECTRON_MIRROR=http://ftp.loongnix.cn/electron/LoongArch/
- npm config set registry https://registry.loongnix.cn:4873/
- electron-packager . --ignore="src|build.*|server|rootLib|config|devTools"
- 在进行安装时，去掉package.json中的esbuilder依赖，不然会报不支持loognarch的错误
  
# 统信商店打包步骤
- 1 上传源码，安装依赖 yarn
- 2 yarn install 
  

