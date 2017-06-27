`Kubernetes`占据容器编排的霸主地位，我们一般都会通过`minikube`实验`kubernetes`功能，但是`minikube`是不能直接安装成功的,网上解决方案有2种：
- 科学上网
- 手动通过阿里云下载所需的google镜像

今天我在这里提供第三种方案，这种方案的优势是，我在本文编译的`minikube`，大家下载回去无需多余设置即可使用。本方法原理是修改`minikube`源码，将google镜像修改为阿里云镜像.
目前我只在MacOS下做了编译，Windows下原理一样，有时间我会编译一个windows版本供大家下载，大家有兴趣也可以自己编译.
### 1.安装go并配置GOPATH
- 使用`Homebrew`安装go语言：`brew install go`
- 在环境变量中配置`GOPATH`,`.bash_profile`中添加`export GOPATH=~/Documents/go
`,使用`source ~/.bash_profile`使配置生效

### 2.安装docker toolbox
- 在[https://www.docker.com/products/docker-toolbox](https://www.docker.com/products/docker-toolbox)下载docker toolbox
- 使用阿里云加速docker toolbox
```shell
docker-machine create --engine-registry-mirror=https://*.mirror.aliyuncs.com -d virtualbox default
docker-machine env default
eval "$(docker-machine env default)"
docker info
```
- 建议将Virtualbox的docker虚拟机的内存至少分配4G

### 3.下载代码
在`$GOPATH/src/k8s.io/`目录下克隆代码:
```shell
cd $GOPATH/src/k8s.io/
git clone https://github.com/kubernetes/minikube.git
```
大家不要克隆本文的源码，本文的源码只是参考演示作用。

### 4.替换镜像
用开发工具打开`minikube`目录，我使用的是`Intellij Idea`,全局替换`gcr.io/google_containers`为`registry.cn-hangzhou.aliyuncs.com/google_containers`
因为我这次使用的`minikube`版本较新，阿里云没有最新版本的`kube-dns`和`kube-dashboard`，所以`kube-dns-controller.yaml`中的image版本由`1.14.2`,`dashboard-rc.yaml`中的image版本由`v1.6.1`，改为`v1.6.0`,大家以后自己编译的时候可以在[https://dev.aliyun.com/search.html](https://dev.aliyun.com/search.html)检索下阿里云中最新镜像版本。

### 5.编译
在minikube目录下执行`make`命令进行编译,可执行文件将生成在当前目录下的out目录下，其中`minikube`,`minikube-darwin-amd64`均可。

### 6.使用
- 当前目录执行:
```shell
./minikube start

```
- 或复制到`/usr/local/bin`下直接执行:
```shell
minikube start
```

- 查看pod
请自行安装`kubectl`
```
wangyunfeideMBP:k8s.io wangyunfei$ kubectl get pod --all-namespaces
NAMESPACE     NAME                          READY     STATUS    RESTARTS   AGE
kube-system   kube-addon-manager-minikube   1/1       Running   0          33m
kube-system   kube-dns-3197702416-st4zq     3/3       Running   0          32m
kube-system   kubernetes-dashboard-n883k    1/1       Running   0          32m
```
镜像全部成功运行.

- 查看dashboard
```shell
wangyunfeideMBP:out wangyunfei$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```
浏览器访问[http://127.0.0.1:8001/ui](http://127.0.0.1:8001/ui)
![image](https://raw.githubusercontent.com/wiselyman/minikube/master/dashboard.png)

### 7.源码地址
[https://github.com/wiselyman/minikube](https://github.com/wiselyman/minikube)

### 8.二进制文件地址
[https://pan.baidu.com/s/1qYQ9DW4](https://pan.baidu.com/s/1qYQ9DW4)