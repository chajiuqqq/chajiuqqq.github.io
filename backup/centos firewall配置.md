查看防火墙状态 `sudo systemctl status firewalld`
## firewall-cmd怎么放行端口

使用 `firewall-cmd` 放行端口的步骤如下：

1. **查看当前防火墙规则：**

   首先，查看当前防火墙的开放端口：

   ```bash
   firewall-cmd --list-all
   ```

2. **放行端口：**

   以放行端口 8080 为例，可以使用以下命令：

   ```bash
   sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
   ```

   说明：
   - `--zone=public`：表示在公共区域进行设置，可以根据实际需要选择其他区域。
   - `--add-port=8080/tcp`：表示放行端口 8080 的 TCP 协议流量。
   - `--permanent`：表示该规则永久生效。如果不加这个选项，规则只会在当前会话有效，重启后失效。

3. **重新加载防火墙配置：**

   执行完上面的命令后，需要重新加载防火墙配置：

   ```bash
   sudo firewall-cmd --reload
   ```

4. **验证端口是否已放行：**

   再次查看当前防火墙规则，确认端口是否已放行：

   ```bash
   firewall-cmd --list-all
   ```

这样，端口就已经放行了。如果需要放行其他端口，只需替换 `8080` 为你需要的端口号。