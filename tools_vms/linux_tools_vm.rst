.. _linux_tools_vm:

---------------
Linux工具VM
---------------

总览
+++++++++

此CentOS VM镜像也同样支持其它支持多个实验练习的软件包。

请在**Lab Setup**上将此VM部署到您分配的群集上.

.. raw:: html

  <strong><font color="red">仅部署一次虚拟机，不需要完成实验后进行清理</font></strong>

部署 CentOS
++++++++++++++++

在 **Prism Central** > 中选择 :fa:`bars` **> Virtual Infrastructure > VMs**, 然后单击 **Create VM**.

填写以下字段：

- **Name** - *Initials*-Linux-ToolsVM
- **Description** - (Optional) Description for your VM.
- **vCPU(s)** - 1
- **Number of Cores per vCPU** - 2
- **Memory** - 2 GiB

- 选择 **+ Add New Disk**
    - **Type** - DISK
    - **Operation** - Clone from Image Service
    - **Image** - CentOS7.qcow2
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

开启 VM.

安装工具
++++++++++++++++

使用下列用户名和密码，通过ssh或控制台登录到:

- **Username** - root
- **password** - nutanix/4u

运行以下命令来安装所需的软件:

.. code-block:: bash

  yum update -y
  yum install -y ntp ntpdate unzip stress nodejs python-pip s3cmd awscli
  npm install -g request
  npm install -g express


配置 NTP
...............

运行以下命令来启用和配置NTP：

.. code-block:: bash

  systemctl start ntpd
  systemctl enable ntpd
  ntpdate -u -s 0.pool.ntp.org 1.pool.ntp.org 2.pool.ntp.org 3.pool.ntp.org
  systemctl restart ntpd

禁用防火墙和SELinux
..............................

运行以下命令来禁用防火墙和SELinux：

.. code-block:: bash

  systemctl disable firewalld
  systemctl stop firewalld
  setenforce 0
  sed -i 's/enforcing/disabled/g' /etc/selinux/config /etc/selinux/config

安装 Python
.................

运行以下命令来安装Python:

.. code-block:: bash

  yum -y install python36
  python3.6 -m ensurepip
  yum -y install python36-setuptools
  pip install -U pip
  pip install boto3
