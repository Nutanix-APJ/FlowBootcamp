.. _windows_tools_vm:

----------------
Windows工具VM
----------------

总览
+++++++++

此Windows Server 2012 R2镜像预装来许多工具，包括:

- Microsoft远程服务器管理工具 (RSAT)
- PuTTY, CyberDuck, WinSCP
- Sublime Text 3, Visual Studio Code
- OpenOffice
- Python
- pgAdmin
- Chocolatey Package Manager

在**Lab Setup**上将此VM部署到您分配的群集上.

.. raw:: html

  <strong><font color="red">仅部署一次虚拟机，不需要在实验完成后进行清理</font></strong>

部署工具VM
++++++++++++++++++

在 **Prism Central** > 选择 :fa:`bars` **> Virtual Infrastructure > VMs**, 点击 **Create VM**.

填写以下字段：

- **Name** - *Initials*-Windows-ToolsVM
- **Description** - (Optional) Description for your VM.
- **vCPU(s)** - 1
- **Number of Cores per vCPU** - 2
- **Memory** - 4 GiB

- 选择 **+ Add New Disk**
    - **Type** - DISK
    - **Operation** - Clone from Image Service
    - **Image** - ToolsVM.qcow2
    - 选择 **Add**

.. -------------------------------------------------------------------------------------
.. 在 5.11 版本发布后我们的实验会按照新版本的配置要求来实现!!!!

.. - **Boot Configuration**
 ..  - 默认选择 **Legacy Boot**

   .. .. 注意::
   ..  通过下面这个URL，您可以找到支持的操作系统
   ..  http://my.nutanix.com/uefi_boot_support

.. -------------------------------------------------------------------------------------

- 选择 **Add New NIC**
    - **VLAN Name** - Secondary
    - 选择 **Add**

点击 **Save** 创建 VM.

开启VM.

使用以下用户名和密码通过RDP或控制台登录到VM:

- **Username** - NTNXLAB\\Administrator
- **password** - nutanix/4u
