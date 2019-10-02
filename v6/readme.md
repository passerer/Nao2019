## NAO V6 C++ SDK Installation Guide and Examples

> @author 汤远航

示例代码来源 https://github.com/robocupmipt/tutorials 请先下载好
环境 **Ubuntu 16.04**

##### 安装与配置

###### 1） 安装依赖库
` sudo apt-get install build-essential checkinstall`
` sudo apt-get install gcc-multilib libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libc6-i386 libbz2-dev `

###### 2） 安装 QTcreator + CMake
`sudo apt-get install gcc cmake qtcreator`

###### 3） 安装python 2.7 （若已有请忽略）
```
cd /usr/src 
wget https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tgz 
tar xzf Python-2.7.13.tgz 
cd Python-2.7.13 
sudo ./configure 
sudo make altinstall 
```

###### 4）安装 pip (若已有请忽略)
`sudo apt install python-pip`

###### 5）安装 qibuild
`pip install qibuild`

###### 6）安装 v6 C++ SDK 和 ctc（交叉编译链）
谷歌云盘下载:
[C++ SDK](https://drive.google.com/open?id=1vSsmdZ-FWL_bBMNC06_iaHsDi77jvbwS)
[cross-toolchain](https://drive.google.com/open?id=162PeZSlJ2_Skj8nzoH5qBYcyolB-7E3t)
或登录NAO官网下载v6对应版本（2.8）
下载完成后解压

###### 7）配置工具链（toolchain）

`qitoolchain create linux-sdk path_to_SDK/toolchain.xml`

`qibuild add-config linux-sdk -t linux-sdk`

linux-sdk 为C++_SDK的工具链名称，用于编译运行于PC端的 C++ 代码（remote）

` $ qitoolchain create atom-sdk path_to_cross-toolchain/toolchain.xml`

 `$ qibuild add-config atom-sdk -t atom-sdk`
 
atom-sdk 为C++_ctc的工具链名称，用于编译放在NAO终端的 C++ 代码（local）
此时输入命令

`qitoolchain list`

可以查看已有的工具链：
``` 
linux-sdk
atom-sdk
```
##### 实例 让NAO说话

###### 实例1： 编译动态链接库文件（.so）
进入 tutorials-master/2_local-module-creation/mymodule-example 文件夹内,打开终端:

`qibuild init` 
`qibuild configure -c atom-sdk` 
`qibuild make -c atom-sdk` 

然后可以看到 mymodule-example 文件夹内多了一个 build-linux-sdk 文件夹。

`cd build-linux-sdk/sdk/lib/naoqi/ `

可以看到 libmymodule.so 文件,这是一个动态连接库文件，需要将其传到 NAO 终端:

先检测是否可以连接Nao:

`ssh nao@<nao_ip>`

复制文件:

`scp libmymodule.so nao@<nao_ip>:~/nao/naoqi/lib`

更改autoload.ini文件。该文件指定了开机自启项:

`cd ~/nao/naoqi/preferences/` 
`vi autoload.ini` 

在[user]下方添加：

`path_to_libmymodule/libmymodule.so`

保存更改，重启NAO
至此,NAO有了我们自己写的库，可以随时通过代码调用它

###### 实例2：编译PC端的C++代码（bin）
在PC端进入 tutorials-master/2_local-module-creation/connect-to module 文件夹内

`qibuild init` 
`qibuild configure -c linux-sdk` 
`qibuild make -c linux-sdk` 

然后可以看到 mymodule-example 文件夹内多了一个 build-linux-sdk 文件夹。

`cd build-linux-sdk/sdk/bin`

启动生成的可执行文件myproject，输入NAO ip：

` ./myproject --pip <nao_ip>`

此时NAO应该能发出声音.

######  实例3：编译nao端的C++代码（bin）

在PC端进入 tutorials-master/2_local-module-creation/connect-to module 文件夹内

`qibuild configure -c atom-sdk`
`qibuild make -c atom-sdk`

然后可以看到 mymodule-example 文件夹内多了一个 build-linux-sdk 文件夹。

`cd build-linux-sdk/sdk/bin`

将可执行文件myproject复制到nao上

`scp myproject nao@<nao_ip>:~/nao/somewhere_you_like`

更改autoload.ini文件:

`cd ~/nao/naoqi/preferences/`
`vi autoload.ini`

在[program]下方添加：

`path_to_project/myproject`

保存更改，重启NAO。当NAO开机时，会自动执行myproject
