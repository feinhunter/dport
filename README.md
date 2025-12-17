# dport.sh - iptables 端口转发配置工具

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Shell Script](https://img.shields.io/badge/Shell-Bash-green.svg)](https://www.gnu.org/software/bash/)

一个自动化配置 iptables 端口转发规则的 Bash 脚本，支持 IPv4/IPv6 双栈，开机自动加载。

## ✨ 特性

- 🔍 自动检测网卡名称
- 🌐 同时支持 IPv4 和 IPv6
- ⚡ 规则立即生效，无需重启
- 🔄 开机自动加载规则
- 🎨 彩色交互式界面
- ✅ 自动安装依赖

## 📋 系统要求

- Linux 系统（支持 Debian/Ubuntu/CentOS/RHEL）
- Root 权限
- iptables/ip6tables

## 🚀 快速开始

### 1. 下载脚本

```bash
wget https://raw.githubusercontent.com/feinhunter/dport/main/dport.sh
chmod +x dport.sh
```

或使用 curl：

```bash
curl -O https://raw.githubusercontent.com/feinhunter/dport/main/dport.sh
chmod +x dport.sh
```

### 2. 运行脚本

```bash
sudo ./dport.sh
```

### 3. 按提示输入配置

- **网卡名称**：自动检测，直接回车使用默认值
- **跳转目标端口**：默认 `10593`
- **源端口范围**：默认 `10595:11596`

## 📖 使用示例

```bash
$ sudo ./dport.sh

=== iptables 端口转发配置工具 ===

检测到的网卡名称: eth0
按 Enter 使用检测到的网卡，或手动输入网卡名称: 
输入跳转目标端口 (默认: 10593): 
输入源端口范围 (默认: 10595:11596): 

配置信息:
  网卡: eth0
  源端口: 10595:11596
  目标端口: 10593

正在配置 IPv4 规则...
正在配置 IPv6 规则...
正在保存 iptables 规则...

✓ 配置完成！
✓ iptables 规则已立即生效
✓ 规则已配置为开机自动加载
```

## 🔧 工作原理

脚本会执行以下操作：

1. 自动检测活跃的网络接口
2. 配置 iptables NAT 规则进行 UDP 端口转发
3. 保存规则到系统持久化配置
4. 创建 systemd 服务实现开机自动加载

### 生成的文件

- `/usr/local/bin/apply-iptables-rules.sh` - 规则应用脚本
- `/etc/systemd/system/apply-iptables-rules.service` - Systemd 服务

## 🛠️ 手动管理

### 查看当前规则

```bash
# IPv4
sudo iptables -t nat -L PREROUTING -n -v

# IPv6
sudo ip6tables -t nat -L PREROUTING -n -v
```

### 删除规则

```bash
# 禁用服务
sudo systemctl disable apply-iptables-rules.service

# 删除规则（根据实际端口修改）
sudo iptables -t nat -D PREROUTING -i eth0 -p udp --dport 10595:11596 -j DNAT --to-destination :10593
sudo ip6tables -t nat -D PREROUTING -i eth0 -p udp --dport 10595:11596 -j DNAT --to-destination :10593
```

### 重新应用规则

```bash
sudo systemctl restart apply-iptables-rules.service
```

## 📝 常见问题

### Q: 如何检查规则是否生效？

```bash
sudo iptables -t nat -L PREROUTING -n -v | grep 10593
sudo ip6tables -t nat -L PREROUTING -n -v | grep 10593
```

### Q: 支持哪些系统？

支持主流 Linux 发行版：
- Debian 9+
- Ubuntu 16.04+
- CentOS 7+
- RHEL 7+

### Q: 可以配置多个端口转发吗？

可以，多次运行脚本即可添加多条规则。

## 📄 许可证

MIT License

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

项目地址：https://github.com/feinhunter/dport

## ⚠️ 注意事项

- 确保目标端口未被其他服务占用
- 配置防火墙时注意安全规则
- 建议先在测试环境验证
