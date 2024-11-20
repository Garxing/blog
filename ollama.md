# 在windows系统下部署Ollama和OpenWebUI
主要参考了[wsl安装](https://blog.csdn.net/wangtcCSDN/article/details/137950545)和[docker安装](https://blog.csdn.net/qq_43840665/article/details/140684568)两篇博客

### 1、安装WSL
<p>打开开始菜单，在开始菜单中输入启用或关闭 Windows 功能，在弹出的窗口中勾选**虚拟机平台**和**适用于 Linux 的 Windows 子系统**，确定之后<b>重启系统</b>。</p>

重启电脑后，打开**Windows PowerShell**用管理员身份运行，执行
```bash
wsl.exe –update
```
命令，即可装入WSL。

### 2、安装Ubuntu24.04
打开**Microsoft Store**，搜索Ubuntu并下载，下载完成后，会让你创建用户和密码，然后即可安装成功。

查询当前安装的 Ubuntu 版本用命令`lsb_release -a`即可。

前往[清华镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)即可配置对应的镜像源，如果是Ubuntu 24.04之前的版本，那么应该用`sudo nano /etc/apt/sources.list`，如果是Ubuntu 24.04的版本那么要用`sudo nano /etc/apt/sources.list.d/ubuntu.sources`,下面是**ubuntu24.04**的对应软件源：
```
Types: deb
URIs: https://mirrors.tuna.tsinghua.edu.cn/ubuntu
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: https://mirrors.tuna.tsinghua.edu.cn/ubuntu
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```
然后，按下**Ctrl + O**并**Enter**保存，再按下**Ctrl + X**退出 nano，保存之后，输入以下内容更新镜像源`sudo apt update && sudo apt upgrade -y`

### 3、docker安装
#### 3.1 安装依赖包
`sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release`

#### 3.2 添加阿里云镜像源和密钥
`curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`
#### 3.3 添加阿里云镜像源
`echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`
#### 3.4 从软件源中更新安装工具包
`sudo apt update`
#### 3.5 安装Docker套件
`sudo apt install docker-ce docker-ce-cli containerd.io`
#### 3.6 配置Docker镜像源，前往[华为云镜像](https://console.huaweicloud.com/swr/?region=cn-north-4#/swr/mirror)
```
1.创建Docker文件夹
sudo mkdir -p /etc/docker
2.写入个人镜像源
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://要写入的镜像源.cn"]
}
EOF
3.重启守护进程和docker引擎
sudo systemctl daemon-reload
sudo systemctl restart docker
```
#### 3.7测试镜像源配置成功
` sudo docker pull hello-world`

### 4、docker desktop安装
在[docker官网](https://www.docker.com/products/docker-desktop/)里下载**docker desktop**的windows版本即可

### 5、下载 Ollama 和 OpenWebUI 
用管理员身份进入Windows PowerShell然后输入`wsl`进入Ubuntu系统
装Ollama
#### 5.1 下载Ollama CPU版本
如果是CPU版本，直接输入

`sudo docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama`
#### 5.2 下载Ollama GPU版本
如果是GPU版本，先装[英伟达工具](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#installation)，然后再输入
```
Sudo docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```
#### 5.3 运行 Ollama 库的模型
下载后，运行ollama2的命令为
```
docker exec -it ollama ollama run llama2
```
同理，运行Qwen2.5的命令为
```
docker exec -it ollama ollama run qwen2.5:7b
```
#### 5.4 安装 OpenWebUI
用docker安装**OpenWebUI**，在终端中输入
```
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```
即可，然后在**本地浏览器输入** [http://localhost:3000](http://localhost:3000) 中即可看到界面，自行创建**邮箱和密码**即可进入。进入后，即可看到Ollama库中正在运行的模型。

### 注意事项
先装WSL，确定版本为WSL2，然后装Docker Desktop，然后再在WSL2中装docker,**防止docker的容器出现消失的情况**

同时，要注意docker在封装ollama容器时，很可能会遗失启动指令，所以**最好不要直接用别人封装后的ollama容器**
