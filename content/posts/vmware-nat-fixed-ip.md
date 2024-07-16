+++
title = '为 NAT 模式下的 VMware 虚拟机设置固定 IP'
date = 2024-07-16T15:09:19+08:00
draft = false
+++

为了满足各种需要，我在 Windows 下使用 VMware Workstation 开了多台 Linux 虚拟机。但是 VMware 的 DHCP 服务为虚拟机分配的 IP 并不是固定的，而且 VMware 的「虚拟网络编辑器」也没有提供为虚拟机设置固定 IP 的选项。实际上，是可以通过修改配置文件的方式为虚拟机设置固定 IP 的。

<!-- more -->

首先，需要获得虚拟机的主机名（假设为 `archlinux-vm`）和虚拟机以太网卡的 MAC 地址（可以使用 `ip a` 等命令活动，假设为 `12:34:56:78:90:ab`）。

然后，找到 VMware 的 DHCP 服务器配置文件，该文件通常位于以下位置：

- Windows：`C:\ProgramData\VMware\vmnetdhcp.conf`
- Linux：`/etc/vmware/vmnet8/dhcp/dhcp.conf`

使用文本编辑器打开文件，在文件末尾添加以下内容（假设需要设置的固定 IP 为 `192.168.30.233`）：

```
host archlinux-vm {
    hardware ethernet 12:34:56:78:90:ab;
    fixed-address 192.168.30.233;
}
```

修改配置文件后，在物理机上运行以下命令重启 VMware 的 DHCP 服务：

```plaintext
net stop vmnetdhcp
net start vmnetdhcp
```

> 也可以使用 `services.msc` 重启「VMware DHCP Service」。

如果虚拟机的 IP 依然没有改变，可以尝试重启虚拟机的 `dhcpcd` 等网络服务。

# 参考资料

- [Editing the DHCP Server Configuration File](https://docs.vmware.com/en/VMware-Workstation-Pro/17/com.vmware.ws.using.doc/GUID-04D783E1-3AB9-4D98-9891-2C58215905CC.html)
- [Assigning fixed IP by DHCP in VMWare Workstation](https://superuser.com/questions/768515/assigning-fixed-ip-by-dhcp-in-vmware-workstation)