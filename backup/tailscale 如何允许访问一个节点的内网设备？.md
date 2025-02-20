> deepseek

要允许通过 Tailscale 访问某个节点的内网设备，可以按照以下步骤进行配置：

1. **启用子网路由**：
   - 在目标节点上，运行以下命令以启用子网路由：
     ```bash
     sudo tailscale up --advertise-routes=<内网子网>
     ```
     例如，如果你的内网子网是 `192.168.1.0/24`，则命令如下（这里可以写多个子网，在控制台中配置启用哪个子网）：
     ```bash
     sudo tailscale up --advertise-routes=192.168.1.0/24
     ```
 


2. **在 Tailscale 管理面板中批准子网路由**：
   - 登录 Tailscale 管理面板（https://login.tailscale.com/admin/machines）。
   - 找到目标节点，点击右侧的“Edit route settings”。
   - 启用你想要路由的子网。

![Image](https://github.com/user-attachments/assets/7c8603dd-7ec5-42a3-83aa-9c2df877840b)

3. **确保其他节点可以访问该子网**：
   - 在其他节点上，运行以下命令以确保它们可以访问该子网：
     ```bash
     sudo tailscale up --accept-routes
     ```
   这将使其他节点接受来自 Tailscale 的子网路由。

4. **测试连接**：
   - 在其他节点上，尝试 ping 或访问内网设备的 IP 地址，确保连接正常。

通过这些步骤，你可以通过 Tailscale 访问某个节点的内网设备 
