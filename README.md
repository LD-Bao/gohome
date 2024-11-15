# 利用 OpenClash 和 Passwall 搭建回家的 SOCKS5 网络

本教程旨在指导如何通过 OpenClash 和 Passwall 配置 SOCKS5 网络，实现家庭网络的远程访问。

---

## 前期准备
1. 搭建好 OpenWrt 软路由环境。
2. 安装以下插件：
   - [OpenClash](https://github.com/vernesong/OpenClash)
   - [Passwall](https://github.com/xiaorouji/openwrt-passwall)

---

## OpenClash 设置
1. 打开 OpenClash 的插件界面。
2. 找到**覆写设置** -> **常规设置**。
3. 向下滚动，找到**设置 SOCKS5/HTTP(S) 认证信息**选项。
4. 添加一个自定义的用户名和密码，保存设置。

---

## Passwall 设置
1. 打开 Passwall 插件。
2. 不要启动主开关（与 OpenClash 不兼容，不能同时启用）。
3. 点击**服务器端**页面 -> **添加**按钮。
4. 按以下要求填写配置：
   - **启用**：勾选。
   - **备注**：随意填写。
   - **类型**：选择 `Xray`。
   - **协议**：选择 `VLESS`。
   - **监听端口**：填写自定义端口（如：`10008`），记住该端口号。
   - **加密方式**：选择 `none`。
   - **ID/密码**：使用 [UUID 生成器](https://www.uuidgenerator.net/)生成一个 UUID。
   - **传输层**：选择 `TCL`。
   - **防伪类型**：选择 `none`。
   - **接受局域网访问**：如果希望接入网络后能通过内网 IP 访问其他设备，勾选此项。
   - 其余选项保持默认或留空。

5. 保存并应用。

---

### 添加出站节点
如果 Passwall 仅作为代理工具，不需要配置出站节点。但本例中 Passwall 用于中转到 OpenClash，因此需要设置出站节点。

1. 点击**出站**，按以下要求配置：
   - **出站节点类型**：自定义 Socks。
   - **地址**：`127.0.0.1`。
   - **端口**：填写 OpenClash 的 Socks5 端口（默认是 `7891`，可在 OpenClash 中更改）。
   - **用户名**：填写在 OpenClash 中设置的用户名。
   - **密码**：填写在 OpenClash 中设置的密码。
   - **日志**：勾选。
   - **日志等级**：按需选择。

2. 保存并应用。

---

## 配置端口转发
1. 打开 OpenWrt 的 Web 界面。
2. 依次进入**网络** -> **防火墙** -> **端口转发**。
3. 配置端口转发规则：
   - **外部和内部端口**：填写 Passwall 设置的监听端口号（如：`10008`）。
   - **内部 IP 地址**：填写软路由本身的 IP（如：`192.168.1.1`）。
   - **源区域**：选择 `wan`。
   - **目标区域**：选择 `lan`。

4. 保存并应用。

---

## 配置 DDNS 动态解析
家庭网络的公网 IP 会不断变化，因此建议配置动态域名解析，推荐使用 [DDNS-GO](https://github.com/jeessy2/ddns-go)。

---

## 手机端配置（V2RayNG）
1. 打开 V2RayNG，点击 `+` 号手动添加节点。
2. 配置如下：
   - **类型**：选择 `VLESS`。
   - **地址**：填写动态解析的域名。
   - **端口**：填写 Passwall 的监听端口号（如：`10008`）。
   - **用户 ID**：填写 Passwall 配置的 UUID。
   - **加密方式**：选择 `none`。
   - **传输层**：选择 `TCL`。
3. 保存节点。

### 手机端其他设置
1. 打开设置页面，勾选以下选项：
   - **启用流量探测**。
   - **分应用代理**。
   - **启用本地 DNS**。
2. 进入路由设置页面，根据需求勾选：
   - **Google CN**。
   - **阻断 UDP 443**。
   - **阻断广告**。
   - **绕过局域网 IP 和域名**（如果无需通过内网 IP 访问家庭设备）。
   - **绕过中国 IP 和域名**（根据需要勾选）。

---

以上配置完成后，即可实现家庭网络的远程访问。
