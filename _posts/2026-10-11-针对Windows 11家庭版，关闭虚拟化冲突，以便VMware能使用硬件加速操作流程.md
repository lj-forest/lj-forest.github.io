---
layout: post
title: "针对Windows 11家庭版，关闭虚拟化冲突，以便VMware能使用硬件加速操作流程"
date: 2026-10-11 12:01:00 +0800
categories: 教程
---

# 针对Windows 11家庭版，关闭虚拟化冲突，以便VMware能使用硬件加速操作流程

问题：

<img src="E:\lj-forest.github.io\assets\images\my-post\image-20261011180516976.png" alt="image-20261011180516976" style="zoom:50%;" />

### 🔧 第一步：关闭 Windows 功能中的冲突组件

1.  按 `Win + R` 键，输入 `optionalfeatures`，然后按回车，打开“Windows 功能”窗口。
2.  在列表中找到并**取消勾选**以下选项（如果存在的话）：
    *   **Hyper-V**
    *   **虚拟机平台 (Virtual Machine Platform)**
    *   **Windows 虚拟机监控程序平台 (Windows Hypervisor Platform)**
    *   **Windows 沙盒 (Windows Sandbox)**
3.  点击“确定”，然后根据提示**重启电脑**。

### 💻 第二步：以管理员身份运行命令，强制关闭 Hypervisor

1.  右键点击“开始”菜单，选择“**终端(管理员)**”或“**Windows PowerShell (管理员)**”。
2.  在弹出的窗口中，输入以下命令并按回车：
    ```cmd
    bcdedit /set hypervisorlaunchtype off
    ```
    看到“操作成功完成”的提示即可。
3.  继续输入第二条命令并按回车：
    ```cmd
    bcdedit /set vsmlaunchtype off
    ```
    如果提示“找不到元素”或类似信息，可以忽略。
4.  **再次重启电脑**，让命令生效。

### 🛡️ 第三步：关闭“内存完整性” (核心隔离)

1.  打开“**Windows 安全中心**” → “**设备安全性**” → “**内核隔离**”。
2.  点击“**内核隔离详细信息**”，将“**内存完整性**”的开关**关闭**。
3.  根据提示**重启电脑**。

### 📝 第四步：通过注册表彻底禁用 VBS (家庭版关键步骤)

由于家庭版没有组策略编辑器，我们需要通过注册表来确保 VBS 被禁用。

1.  按 `Win + R`，输入 `regedit`，按回车打开注册表编辑器（如果弹出用户账户控制提示，点击“是”）。
2.  导航至以下路径（可以直接复制粘贴到地址栏）：
    ```
    计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard
    ```
3.  在右侧空白处右键，选择“**新建**” → “**DWORD (32位) 值**”，命名为 `EnableVirtualizationBasedSecurity`，然后双击它，将数值数据改为 `0`。
4.  接着，再导航至以下路径：
    ```
    计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\HypervisorEnforcedCodeIntegrity
    ```
5.  在右侧找到 `Enabled`，双击并将数值数据改为 `0`。如果找不到，可以右键新建一个 DWORD (32位) 值，命名为 `Enabled`，值设为 `0`。
6.  关闭注册表编辑器，**最后一次重启电脑**。

### ✅ 第五步：验证是否成功

完成所有步骤并重启后，进行最终验证：

1.  按 `Win + R`，输入 `msinfo32`，打开“系统信息”。
2.  在右侧摘要中，确认 **“基于虚拟化的安全性”** 的状态显示为 **“未启用”**。
3.  同时，确认摘要中**不再出现** “**已检测到虚拟机监控程序**” 的字样。

现在，重新打开 VMware，可以在虚拟机设置中顺利勾选“虚拟化 Intel VT-x/EPT”选项了。
