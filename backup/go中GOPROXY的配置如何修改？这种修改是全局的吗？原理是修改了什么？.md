> Summary：GO的配置都是读取环境变量，比如GOPROXY有默认值，修改就是修改环境变量，要么直接export修改当前shell的环境变量，或者把export加到profile里，每次登录都会执行，比较方便。

在Go语言中，`GOPROXY` 环境变量决定了 go 命令如何检索外部模块。默认情况下，Go 会使用其官方模块代理（`https://proxy.golang.org`），但你可以通过修改环境变量来更改这个设置。

### 修改 GOPROXY

你可以通过以下几种方式修改 `GOPROXY`：

1. **临时修改**：
   
   在命令行中，你可以设置环境变量来为当前会话修改 `GOPROXY`：
   ```shell
   export GOPROXY=https://goproxy.io
   ```
   这种修改只影响当前的shell会话。

2. **永久修改**：
   
   要永久修改 `GOPROXY`，你可以将上述 `export` 语句添加到你的 shell 配置文件中，如 `~/.bashrc`、`~/.bash_profile` 或 `~/.zshrc` 等，这样每次新的shell会话都会应用这个设置。
   ```shell
   echo "export GOPROXY=https://goproxy.io" >> ~/.bashrc
   source ~/.bashrc
   ```
   或者，你可以使用 `go env -w` 命令来永久设置环境变量：
   ```shell
   go env -w GOPROXY=https://goproxy.io
   ```

3. **项目级别修改**：
   
   如果你只想为特定项目修改 `GOPROXY`，你可以在项目的根目录下创建一个 `.env` 文件或者使用 `go env -w` 命令，结合项目使用的工具或脚本来设置环境变量。

### 修改是否为全局的

- 使用 `export` 添加到 `.bashrc` 或其他 shell 配置文件中的修改，对于所有在那个shell中启动的新进程都是全局的。
- 使用 `go env -w` 命令修改的环境变量则是针对当前用户的 Go 命令全局有效的，不限于某个shell会话。

### 原理

修改 `GOPROXY` 实际上是在告诉 Go 工具链从哪里下载外部模块。当你运行像 `go get`、`go build` 或 `go mod` 等需要依赖管理的命令时，Go 命令会根据 `GOPROXY` 环境变量所设置的代理地址去获取依赖。

当设置 `GOPROXY` 环境变量后，Go 命令会使用这个代理来加速模块的下载，特别是在某些模块在默认代理（如 `proxy.golang.org`）不可用或者在你所在地区访问缓慢的情况下。代理服务器通常会缓存公共模块，所以这也有助于减轻源服务器的负担，并提供更快的下载速度。