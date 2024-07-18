https://devopscube.com/keep-docker-container-running/

## 背景

需要构建编译环境的镜像，但是直接docker build会出现很多错误，不好debug。于是构建一个基础镜像，然后exec进去尝试好再写dockerfile，发现基础镜像run之后会立刻退出Exited(0)，于是寻找debug方法。

## 方法1：直接在没有ENTRYPOINT的容器打开一个交互tty
```
docker run -it --name tty-container ubuntu /bin/bash
```
此时exit的话，容器直接停止了
![image](https://github.com/user-attachments/assets/bdf9dde9-63f7-4368-a741-c1aaf5723d6f)

注意，如果镜像指定了CMD或者ENTRYPOINT，容器会在执行完该命令后退出，无法持续running，可以用方法3替换。
## 方法2：让容器在后台keep running，然后exec进去

此时exit的话，容器还是running，可以多次exec进去
```
docker run -it -d --name tty-container ubuntu /bin/bash
docker exec -it tty-container /bin/bash
```
注意，必须加-it，不然没有持续运行的进程，就直接退出了。
![image](https://github.com/user-attachments/assets/a661f62c-68ac-4294-b24b-24453d9fe23e)

## 方法3：在run的最后添加tail命令让容器running
```
 docker run -d ubuntu tail -f /dev/null
```
![image](https://github.com/user-attachments/assets/5ad9cb03-ed23-4e74-b8df-d9578f9ef373)

## 方法4：sleep infinity命令
sleep infinity也可以让容器running，再exec进去
```
docker run -d ubuntu sleep infinity
```
![image](https://github.com/user-attachments/assets/e58f0fc0-321f-4952-aad6-8327c1d48474)

## 方法5：把能持续运行的命令加到dockerfile的ENTRYPOINT中

```
ENTRYPOINT ["tail", "-f", "/dev/null"]
```

