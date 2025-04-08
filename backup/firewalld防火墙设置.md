检查状态：

```bash
sudo systemctl status firewalld  
```
启动和停止：

```bash
sudo systemctl start firewalld  
sudo systemctl stop firewalld  
```
查看当前配置：

```bash
sudo firewall-cmd --list-all  
```
# firewalld 怎么允许入站端口

在 `firewalld` 中允许入站端口非常简单。你可以使用 `firewall-cmd` 命令来完成这项配置。以下是允许入站端口的步骤和示例：

### 允许特定入站端口
1. **使用 `firewall-cmd` 添加规则**：
   语法如下：
   ```bash
   sudo firewall-cmd --zone=<zone> --add-port=<port>/<protocol> --permanent
   ```

   - `<zone>`：指定要应用规则的区域（如 `public`）。
   - `<port>`：要打开的端口号。
   - `<protocol>`：协议类型，例如 `tcp` 或 `udp`。

   **示例**：允许 TCP 端口 8080
   ```bash
   sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
   ```

2. **重新加载防火墙配置**：
   规则添加后，需要重新加载防火墙以使更改生效：
   ```bash
   sudo firewall-cmd --reload
   ```

3. **验证规则是否生效**：
   使用以下命令检查当前已打开的端口：
   ```bash
   sudo firewall-cmd --zone=public --list-ports
   ```

### 允许特定服务
如果你要允许某个服务，而不是手动指定端口，可以使用服务名称：
```bash
sudo firewall-cmd --zone=public --add-service=<service-name> --permanent
sudo firewall-cmd --reload
```
**示例**：允许 HTTP 服务
```bash
sudo firewall-cmd --zone=public --add-service=http --permanent
sudo firewall-cmd --reload
```

### 需要注意的事项
- 确保使用 `--permanent` 选项，以便在防火墙重启后保持配置。
- 如果不使用 `--permanent` 选项，则更改只会在当前会话中生效。
 