.. _labsetup:

----------------------
Calm 实验设置
----------------------




配置一个 Project（项目）
+++++++++++++++++++++

在本实验中，您将利用多个配置的的“ Calm Blueprints”来设置您的应用程序

#. 在 **Prism Central**, 选择 :fa:`bars` **> Services > Calm**.\

#. 从左侧菜单中选择选择 **Projects** 然后单击 **+ Create Project**.

   .. figure:: images/2.png

#. 填写以下字段:

   - **Project Name** - *Initials*\ -Project
   - 在 **Users, Groups, and Roles**, 选择 **+ User**
      - **Name** - Administrators
      - **Role** - Project Admin
      - **Action** - Save
   - 在**Infrastructure**, 选择 **Select Provider > Nutanix**
   - 点击 **Select Clusters & Subnets**
   - 选择 *Your Assigned Cluster*
   - 在 **Subnets**, 选择 **Primary**, **Secondary**, 点击 **Confirm**
   - 通过单击 :fa:`star` 将 **Primary** 标记为默认网络

   .. figure:: images/3.png

#. 点击 **Save & Configure Environment**.

部署Windows Tools VM
++++++++++++++++++++++++++++

本实验中的一些配置需要利用Windows Tools VM。请按照以下步骤从磁盘镜像配置您的个人VM。

#. 在 **Prism Central**, 选择 :fa:`bars` **> Virtual Infrastructure > VMs**.

#. 点击 **+ Create VM**.

#. 填写以下字段以完成用户VM请求:

   - **Name** - *Initials*\ -WinToolsVM
   - **Description** - Manually deployed Tools VM
   - **vCPU(s)** - 2
   - **Number of Cores per vCPU** - 1
   - **Memory** - 4 GiB

   - 选择 **+ Add New Disk**
      - **Type** - DISK
      - **Operation** - Clone from Image Service
      - **Image** - WinToolsVM.qcow2
      - 选择 **Add**

   - 选择 **Add New NIC**
      - **VLAN Name** - Secondary
      - 选择 **Add**

#. 选择 **Save** to create the VM.

#. 开启 *Initials*\ **-WinToolsVM**.
