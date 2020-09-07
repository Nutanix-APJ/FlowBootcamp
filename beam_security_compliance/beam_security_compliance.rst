.. title:: Xi Beam - 安全合规性

.. Xi Beam - 安全合规性:

--------------------------------------------
Xi Beam的本地安全合规性
--------------------------------------------

总览
+++++++++

Xi Beam是一种安全合规性和成本管理服务，可与公共云、Nutanix私有云一起使用。本实验介绍了Beam针对Nutanix的安全合规性功能。针对公共云也可以使用相同的功能。

什么是安全合规性？这是通过自动审核云资源配置和轻松修复安全漏洞来建立和验证对安全基准的遵从性的做法。针对Nutanix私有云，Beam包括以下安全合规性功能：

	- 安全审核和修复-全局的安全审核结果和修复步骤，以提高Nutanix私有云的安全性
	- 法规政策合规性-实现您所在的组织、企业对PCI-DSS和STIG等法规政策合规性
	- 自定义审核-非常轻松地创建自己的自定义审核流程，以解决以往安全审核的复杂性

目的
++++++++++

本实验旨在模拟Nutanix私有云客户使用案例，以识别可能由于配置错误的云资源而导致的关键安全漏洞。在实验结束时，您将学到:

	- Beam如何报告Nutanix私有云中的安全性错误配置
	- 如何采取补救措施以改善私有云安全性
	- 如何创建自己的自定义安全审核策略
	- 如何设置规则以在新的安全审核失败时实现通知和告警

	.. figure:: images/beam_sc_image1.png

前提条件
++++++++++++++++

Beam针对Nutanix私有云的安全合规性功能包含两个组件-Beam SaaS实例和安装在Nutanix群集上的Beam-VM。这是本练习的一些先决条件：

#. Beam SaaS登录凭据:
	- 导航至 https://beam.nutanix.com/
	- 选择 “*Sign in with My Nutanix*" 然后选择 "*Login with your Company ID*”
	- 输入公司ID: *beam-lab@nutanix.com*
	- 输入唯一ID: *nutanix6-ad*
	- 这将带您进入Active Directory登录页面，您将在其中输入用户名和密码。使用从群集分配的电子表格分配的登录凭据。
#. Beam-VM: Beam需要在Nutanix群集上安装VM，以报告有关从Prism API到Beam SaaS引擎的资源配置的某些数据。每个Prism Central安装都需要一个Beam-VM，它可以安装在该Prism Central管理的任何群集上。本实验室中，我们已经完成了此操作.
#. Beam中的大多数安全审核功能都依赖于AHV作为管理程序.

如果这是您第一次访问Beam实验，则可能会导航至Beam的Nutanix成本管理模块，并且可能会看到两个弹出窗口，说明Beam如何计算Nutanix产品的成本数据。您可以在安全合规性实验中忽略这些消息，关闭弹出窗口，并导航至安全合规性模块。

	.. figure:: images/beam_sc_image2.png

	.. figure:: images/beam_sc_image2b.png

	.. 注意::

	  要登录到Beam SaaS实例，本练习将使用与HPOC群集相同的活动目录设置。使用从GTS群集分配电子表格，冰分配给您的登录凭据。

架构
+++++++++++++++++++++++++++

Beam-VM需要与Prism Central以及Beam SaaS实例进行双向通信。安全审核数据是使用来自v3 API的数据生成的。每个Prism Central实例需要安装一个VM。Beam-SaaS实例在AWS中运行，尽管这不会以任何方式影响用户。Beam-VM和Beam-SaaS实例之间的通信通过安全的gRPC通道进行。

	.. figure:: images/beam_sc_image3.png

Beam-VM安装
+++++++++++++++++++++++++++

Beam-VM已安装在实验群集上，并使用以下步骤配置了Prism Central帐户：

1. Beam-VM镜像可在portal.nutanix.com上的Xi Beam下载页面上找到。

使用以上图像在Prism Central中创建了一个VM。您可以登录此Prism Central帐户进行查看：
URL：https : //10.55.20.42 : 9440 / 
用户名：admin
密码：techX2020！

Beam-VM需要2个vCPU（每个vCPU至少1个core），2GB RAM和15GB存储空间。在Prism Central中，此实验室使用的VM名称为BeamVM-DoNotDelete。


	.. figure:: images/beam_sc_image2c.png


	.. figure:: images/beam_sc_image2d.png


2. 如果虚拟机已启动，请单击启动控制台，然后使用以下默认凭据登录：
用户名：Beam 
密码：b3 @ mMeUp！

	.. 注意::

	  需要打开端口9440，以使Beam-VM连接到Prism Central，并且需要打开端口443，以使Beam-VM连接到Beam SaaS。

3. 通过提供IPv4地址，网络掩码，网关和DNS服务器地址，已经在VM中配置了网络设置。

4. Beam UI应用程序的本地实例应已在VM中运行。如果不是，请在浏览器中转到https：//beam.local/。您可以验证已在此Beam UI应用程序中配置了Prism Central详细信息，并且已生成令牌，该令牌也在Beam SaaS实例中进行了配置


	.. figure:: images/beam_sc_image2e.png


单击 "*Prism Central Connection*" 并验证是否已配置PC详细信息。


	.. figure:: images/beam_sc_image2f.png


5. 您无需为此实验生成新令牌，但是您可以熟悉在Beam UI应用程序中生成令牌并将其输入到Beam SaaS实例中的位置。


	.. figure:: images/beam_sc_image2g.png


6. 使用前提条件部分中提供的凭据登录到Beam SaaS帐户后，转到 **Configure* -> Nutanix Accounts** 并验证是否在其中输入了PC名称为 *PC-RTP-POC020*. 的令牌。请注意，此令牌在Beam SaaS实例中可能有所不同，因为自创建此脚本以来已刷新了实验室群集设置。在实际安装期间，您还可以选择要在Beam中配置的集群。已为此实验室配置了HPOC群集RTP-POC020。


	.. figure:: images/beam_sc_image4.png

安全审核和修复
+++++++++++++++++++++++++++

全球安全态势
.................

Beam提供了有关您的Nutanix环境安全状况的全局仪表板。该仪表板是使用Beam中的安全审核结果生成的。根据安全最佳实践，安全审核按严重性级别（高，中或低）进行分类。Beam提供了超过1000个针对公共云和私有云的即用型安全审计，其中500多个针对Nutanix私有云的安全性审计。全局安全摘要图用于轻松识别全局安全问题的数量及其严重性类型。


	.. figure:: images/beam_sc_image5.png

该仪表板还提供了安全审核失败总数的时间表。时间线有助于轻松地确定总体安全状况在一段时间内是在改善还是在恶化。向下滚动页面以查看合规性时间表。


	.. figure:: images/beam_sc_image6.png

单击仪表板顶部的 “High Severity” 以挖掘Beam识别出的高严重性审核失败的详细信息。

	.. figure:: images/beam_sc_image7.png


审核报告和补救
.................
您将被带到 **Compliance Remediation -> Audit Details** 选项卡。在这里，您可以查看按审核类型分类的安全审核结果的详细信息：

	- Host Security（本机安全）
	- Infrastructure Security（基础设施安全）
	- Network Security（网络安全）
	- Data Security（数据安全）
	- VM Security（虚拟机安全）
	- Access Security（访问安全）
	- Others（其它）

让我们逐步了解一些审核类型，以了解Beam可以在Nutanix环境中进行审核的一些示例。点击 **“Data Security”**.


	.. figure:: images/beam_sc_image8.png


在这里，您将看到分类为“数据安全性”类型的审核。您将看到Beam识别出一些未启用静态数据（DAR）加密的群集。这是一个严重的安全漏洞。单击审核名称以查看详细信息。


	.. figure:: images/beam_sc_image9.png


在这里，您会看到“群集UUID”和“群集名称”之类的详细信息，以便可以轻松识别需要启用DAR的群集详细信息。让我们回头查看更多审计详细信息。返回两个步骤，然后单击 **“Network Security”**.


	.. figure:: images/beam_sc_image10.png


在这里，您将看到某些网络安全审核类型的详细信息，包括可能对某些端口上的所有外部流量开放的VM。在这种情况下，它们是TCP端口2483和1521，但是Beam可以扫描很大范围的TCP和UDP端口。单击端口2483的审核详细信息。


	.. figure:: images/beam_sc_image11.png


在审核详细信息中，您可以轻松识别群集详细信息，主机IP和VM名称。Beam还提供了补救说明，以便用户可以采取必要的措施来关闭这些端口上的全局访问。单击 **“how to fix”** 以查看这些修复详细信息。


	.. figure:: images/beam_sc_image12.png


	.. figure:: images/beam_sc_image13.png


	.. 注意::

	  Beam大约每6小时运行一次所有安全审核并报告审核失败。在即将发布的版本中，此时间段将缩短。

让我们再看看一种审计类型。返回两个步骤，然后单击 **“Host Security”**. 在这里，您将看到Beam审核的长长的STIG要求列表。单击STIG要求RHEL-07-040400。


	.. figure:: images/beam_sc_image14.png


您将看到这是一次检查SSH守护程序使用哪种哈希算法的审计。如果SSH守护程序配置了不使用FIPS 140-2哈希算法的消息认证代码（MAC），则Beam将对其进行标识。


	.. figure:: images/beam_sc_image15.png


Beam对Nutanix环境进行了数百次此类审核。完成整个审核列表需要花费一些时间，因此在本练习中，我们将跳过这些时间。但是，您可以通过转到右上角下拉菜单中的**Configure -> Compliance Policy** 并查看Beam Security Policy来找到整个审核列表。


	.. figure:: images/beam_sc_image16.png


	.. figure:: images/beam_sc_image17.png



法规政策合规
++++++++++

除了Beam的默认安全策略中包含的各种安全审核之外，Beam还为合规报告提供了诸如PCI-DSS之类的监管策略，以及即将推出的更多策略，如HIPAA，NIST等。返回并导航到 **Compliance** 选项卡。您将看到与PCI-DSS以及STIG策略（包括Beam执行的所有与STIG相关的审计的合规性）级别的整体视图。单击PCI-DSS合规性策略以查看详细信息。


	.. figure:: images/beam_sc_image18.png


符合PCI-DSS
.................

Beam提供了组织应遵守PCI-DSS等法规政策而应采取的所有措施的详尽列表。可以将法规政策合规性视图视为 **a system of records** 以识别您对符合法规政策需要执行的所有任务的合规性。

这些可以分为三类-与流程，文档和配置相关的任务。与您需要维护的安全流程和支持文档有关的流程和文档任务。配置任务与Beam运行的自动资源配置审核有关。单击第1.1节以查看详细信息。


	.. figure:: images/beam_sc_image19.png


在这里，您可以看到遵守PCI-DSS政策所需采取的所有步骤的详细信息。**Process checks（流程检查）:** 关键要求之一是具有“批准和测试所有网络连接的正式流程”。你有这样的程序吗？如果是这样，您可以单击“ 标记为已解决”，然后上载组织已执行的流程的证明。

**Documentation checks（文档检查）:** 您需要具有“持卡人数据环境与其他网络之间的连接的当前网络图”。如果有此图，则可以单击“ 标记为已解决”，然后将该图上载为证明。

**Configuration checks（配置检查）:** 您的网络是否实际以某种方式配置为具有“ DMZ和外部Internet之间的防火墙”？如果没有，Beam将使用其自动审核检查来识别。如果没有适当的防火墙，Beam会将其标记为安全问题。配置任务的另一个示例是“限制入站和出站流量”。Beam认为这是一次审核失败。单击“检测到5个问题：以查看详细信息。


	.. figure:: images/beam_sc_image20.png


我们看到允许所有外部流量的TCP端口的详细信息，因此，“限制入站和出站流量”的PCI-DSS要求未得到满足，您的组织将不完全符合PCI-DSS策略。


STIG 遵从
.................

返回上一步，单击STIG策略并熟悉STIG合规性视图。


	.. figure:: images/beam_sc_image21.png


在这里，我们将看到在符合STIG政策的情况下进行的所有审核的详细信息-哪些通过了哪些失败了。



定制安全审核
++++++++++

除了在Nutanix和公有云上进行1000多次安全审核外，Beam还使您可以非常轻松地创建自己的自定义安全审核。就可用于审核的功能而言，这极大地扩展了产品功能。创建自定义审核后，它将被添加到默认的Beam Security Policy中，并以自动化方式与所有其他现成的审核一起运行。

Beam 查询语言
.................


	.. figure:: images/beam_sc_image22.png


导航到 **Configure -> Custom Audits** 点击 *Add New Custom Audit*, 然后选择Nutanix。


	.. figure:: images/beam_sc_image23.png


您将看到一个查询编辑器。该查询编辑器是使用基于SQL的查询语言（称为 **Beam Query Language**. ）构建的。您将看到一个下拉菜单，以帮助您开始构建自定义编辑。我们要创建一个审核，以检查是否具有允许通过公共IP 0.0.0.0进行入站流量的网络安全组规则的VM。以下是创建此审核的步骤：

**从:** Select *NX*. 选择NX。您还将看到其他云的选项。下一个弹出菜单将为您提供很多资源选项。选择虚拟机



	.. figure:: images/beam_sc_image2h.png


下一个变量将是 **Where:**. 选择类别，然后选择NetworkSecurityGroup。这将显示针对网络安全组分类的所有可审核功能。


	.. figure:: images/beam_sc_image2i.png


现在，我们要检查用于控制入站流量如何流向VM的安全组规则。选择**AppRule** 然后InboundAllowedGroup专门检查入站流量的规则。


	.. figure:: images/beam_sc_image2j.png


最后，我们要检查何时通过特定IP地址（公共IP 0.0.0.0）允许入站流量。选择 **IpSubnet** 然后选择ip。您将看到几个数学函数。选择包含，占位符文本foo将显示。您可以单击它并将其替换为0.0.0.0


	.. figure:: images/beam_sc_image2k.png


这样就完成了自定义审核。您可以选择 *Save Audit*.


	.. figure:: images/beam_sc_image2l.png


指定审核的名称，审核说明，严重性类型以及您希望对审核进行分类的方式。保存审核名称（例如 *XY-BeamLab*）时，请使用您的姓名缩写。这将有助于防止多个人选择相同的审核名称。


	.. figure:: images/beam_sc_image2m.png

您就完成部署审核了！在短短的几分钟内，我们能够创建高度自定义的安全审核，而无需知道任何编码或进行任何配置！

	.. 注意::

	  Beam Query Editor带有“查询库”，您可以在其中查看由组织中其他人创建的自定义审核。您还可以查看“实体详细信息”以了解查询编辑器可以支持哪些实体的详细信息。


警报通知规则
.................

本实验的最后一步是创建通知规则，以便在发生严重审核失败时向您发送警报。这可以通过每日系统生成的报告或自定义通知来完成。

转到 **Configure -> Integration Rules** ，然后单击创建新规则


	.. figure:: images/beam_sc_image31.png


在这里，您可以定义警报条件。此工作流程还可用于将通知发送到Splunk或创建Webhooks。在“事件类型”选项下，选择 *Any Issue State Change (All)*. 这将确保该通知对于安全问题的所有状态更改（包括新问题，已解决的问题和已抑制的问题）均有效。

	.. figure:: images/beam_sc_image32.png


从过滤条件中删除AWS和Azure。单击Nutanix旁边的过滤条件编辑。在显示的弹出窗口中，确保Cloud is Nutanix，单击“ selected audits”旁边的“ edit”，然后找到您在上一节中创建的自定义审核名称。单击蓝色复选标记，保存并关闭。这定义了警报条件。


	.. figure:: images/beam_sc_image33.png


现在，您要定义满足警报条件时将发生的情况。从左侧菜单中选择“New action”，选择“send email”并提供您的电子邮件地址。您可以选择默认的电子邮件模板。验证电子邮件地址，保存并关闭通知规则。


	.. figure:: images/beam_sc_image34.png

	.. figure:: images/beam_sc_image35.png


提供名称和描述，例如 *XY-BeamLabRule*.


	.. figure:: images/beam_sc_image36.png


现在，您已经定义了一个通知规则，该规则将在自定义审核定义失败时发送电子邮件通知。在这种情况下，将是使用AHV在群集中运行的CVM即将用尽磁盘空间。您可以创建任意数量的此类自定义审核。

这就完成了私有云成本治理实验。您可以登出Beam帐户

总结
+++++++++

- Beam的安全合规性功能可以使用1000多个安全审核来识别资源配置错误，这些审核针对基于Nutanix和公共云基础架构的本地私有云。
- Beam还使创建您自己的自定义审核和在您关注的审核失败时收到警报非常容易。
- 可以使用高度可定制的TCO模型来配置Nutanix成本，该模型可帮助您确定运行私有云的真实成本
- 您还可以将Beam用作记录系统，以验证对PCI-DSS等法规政策的遵守情况。
