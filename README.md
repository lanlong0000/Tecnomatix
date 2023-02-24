# Tecnomatix
##　1.布局：
准备阶段，把LAYOUT显示出来，在ps里面加载资源的时候我们第一步需要定义我们所需要加载的文件的格式，对所有的资源进行定义，加载我们自己已经定义好的资源，加载好资源之后，对资源进行分类，把对应的资源放到对应的文件夹下，以方便处理。
选中一个元件，右键选择zoom to selection，可以单独显示该元件。使用relocate功能，可以把原件精准定位到某一位置，可以用点对点，也可以边对边，也可以使用frame对frame定位。把底座放置在对应的位置处，把机器人放到底座上。选择机器人，选择部件坐标系命令，点击一下底座自身，绿框里自动会出现底座的自身坐标系。Apply，机器人会自动到底座上，点击机器人，右键，选择mount tool，绿框里选择抓手，frame选择self，点击apply，抓手会自动到机器人上。
### 1.机构的定义：
#### 升降台定义：
首先，点击LIFT，选择红框中的命令使LIFT变成可编辑的状态。选择运动学编辑，点击创建lnk，在lnk1框里面选择动作时的固定端，点击ok。然后隐藏lnk1，创建一个lnk2，选择在固定端上升降的移 动端，（这次要选全）然后隐藏lnk2，创建一个lnk3，选取夹具的旋转端。把lnk1和lnk2之间连线，在固定端选取一 条直线（升降台上下移动方向上的一条直 线）选择这条线上两个点，然后在红框里 把旋转，改为直线运动。点击ok，把lnk2和lnk3之间连接起来。 From点选取夹具的旋转端面的圆心，to选取另一边的旋转端面的圆心。点击ok。
#### 动作编辑：
点击动作编辑器，（pose editor） 点击new新建动作，这里输入不同 的值就能创造出不同的动作。点击end modeling结束编辑保存修改。
#### 定义回转台：
点击TABLE，使它进入可编辑状态，在运动学编辑器里面创建 两个lnk，在lnk里面选取该颜色的部件连接两个lnk，在from和to里面分别选取它们两个的上下旋转中心。点击ok。点击编辑动作（pose editor），点击新建， 把动作的名字改为FWD,数值改为180，最后点击ok。点击回转台上的夹具台，把它attach到TABLE的 lnk2上，让夹具跟着回转台一起转动。保存修改。
### 2.路径规划：
确定机器人焊接的位置和路径。
创建一个device operation，选择 升降机，做出它的升降动作。同理。做出升降机上夹具的夹紧动作。利用attachment命令，把板件 attach到升降机的运动单元。选择合适的home点，把机器人的当前状态设为动作。再为两个机器人设置home点，并设置好焊接路径。

## 2．CEE的基本行为，物料流，传感器。
在Line Simulation模式中打开文件。在STATION” process下，创建一个Non-Sim操作并将其命名为“INITIALIZATION。将新的“INITIALIZATION操作放置为序列中的第一个操作并将其关联到“LIFT_DRIVE_DOWN”在Signal Viewer的上部工具栏中，点击Create New Signal。选择Display Signal类型并命名为“FIRST” 打开“INITIALIZATION”操作的过渡条件，点击Edit Condition。删除默认“INITIALIZATION_end”信号，然后键入“NOT FIRST”或者也加一个KEY信号，去强制控制启动。选择“LIFT_DRIVE_DOWN”操作，将操作开始处的“First”信号值设置为TRUE。运行仿真并暂停。把TIP DRESSING操作从执行完整的Non-Sim方法中禁用。将WELD和TIP DRESS取消相互关。在WELDING复合操作下，创建一个新的 Non-Sim操作并命名为SERVICE,将其关联到WELD和TIP DRESS操作。更改SERVICE的过渡条件来阻止TIP DRESSING并总是允许WELDING。为了确保后续的循环也被执行，我们将更改从“ INITIALIZATION”到“ LIFT_DRIVE_DOWN ”的过渡条件，以便其 在在序列的最后一个操作“ R1 REMOVE PART ”作结束时也将被求值为TURE。 打开从“I NITIALIZATION”到“ LIFT_DRIVE_DOWN ”的过渡条件,将条件修改为NOT FIRST OR R1 REMOVE PART_end.打开“R3 WELD操作的过渡条件，点击Edit Condition，删除当前条件，在两个对口框中都点击OK关闭它们。
### 物料流：
在Line Simulation模式下载入L02_2-E01.psz” study。完成Appearance定位，是通过触发静态的Appearance并将其手动放置正确来实现的。 在Operation Tree中，右键点击R1 LOAD PART并选择“Generate Appearances”.打开 LIFT_DRIVE_DOWN的Operation Properties菜单，将全部的两个零件都分配到Product tab中。点击OK并删除appearances。选择全部的两个appearances并且将其从它们的SELF frame重放置到PartPosition” frame。在LIFT_DRIVE_DOWN操作中创建一个Attach Event，并且将全部的两个Part Appearances (PART 1 & PART 附加到LIFT’s MOVE link。在Material Flow查看器中，移除在R1 LOAD PART“和两个焊接操作中的冗余link，并将“LIFT DRIVE DOWN”操作link到 最开始处。创建一个新的Non-Sim操作（持续时间=0），将其放在工艺的最后一个操作并命名为“KEEP_ALIVE”。创建一个新的输入信号，称其为START CYCLE 。使用START CYCLE的上升沿，作为从R1 REMOVE PART 到 KEEP_ALIVE的过渡。并更新INITIALIZATION操作的过渡。在Simulation Panel中增加START CYCLE并运行仿真。将keep alive 加入到物料流查看器中。创建一个新的复合操作并命名为MATERIAL FLOW。在其下面，嵌入4个Non-Sim操作（持续时间=0sec），分别命 名为GENERATE PARTS, PROCESS PARTS, ASSEMBLE PARTS, REMOVE ASSEMBLY并将其一个接一个的关联。将INITIALIZATION Non-sim 关联link到MATERIAL FLOW复合操作。
相应的，更新INITIALIZATION操作的过渡分别更改PROCESS PARTS 到 R2 WELD_end的过渡条件和更改ASSEMBLE PARTS到 R1 REMOVE PART_end的过度条件。产品数据分配到新的Material Flow操作结构。引发LIFT_DRIVE_DOWN 和 R1 REMOVE PART两者的appearances。分 配“PART 1” 和 “PART 2”的appearances到GENERATE PARTS操作(Operation Properties Product)以及“ASSEMBLY”的 复合appearance到ASSEMBLE PARTS operation.
同样的，取消“LIFT DRIVE DOWN操作的零件分配.打开Material Flow查看器，并将其更改为只包含MATERIAL FLOW的操作.在GENERATE PARTS操作上， 创建一个Attach Event，并 且将全部的两个Part Appearances (PART 1 & PART 2)附加到 LIFT’s MOVE link。删除KEEP ALIVE操作并恢复过渡条件。
### 传感器：
#### 创建传感器，
创建一个传感器 ， 右键点击 “GENERATE PARTS” 操作，选择 Generate Appearances ， 在 Object Tree中，选择并展开 SENSORS 组合 ，选择Control Sensor Sensors Create Photoelectric Sensor ， 选中Check Interference With 小框 ， 选择PART2（的appearance），然后点击OK。
#### 定位传感器：
在Objects Tree中选择“light_ sensor” • 在图像工具栏中选择Zoom to Selection。使用（图像工具栏中的）Relocate 命令来放置传感器 • 如下面的图片显示的那样，将传感器从其Self-Origin位置放置到 “Light Sensor Position位置 • 点击Apply 和Close。我们将使用光传感器信号作为“WELD”操作的触发，一但RE (light_sensor)”被评估为true，操作就执行。将现有的“WELD”条件从“true”改为“RE (light_sensor)” , 移除common condition，点击OK。配置Simulation Panel，显示light_sensor信号。
#### 编辑接近开关：
选择Modeling -> Sensors -> Create Proximity Sensor。 Create Proximity Sensor对话框展示如下。默认proximity_sensor 显示在Name栏中。
输入传感器名称。在Graphic Viewer或者Object Tree选择用于传感器基础对象的资源或位置来填充Graphic Representation栏。传感器的监测范围由它指向测量。在普通条件下，通过选择Normally False Signal选项将传感器信 号值定义为FALSE（当没有对象在监测范围中时）。
## 3.逻辑块和智能组件：
#### 创建编辑逻辑块
打开Object Tree并选择LOGIX组合 4、使用Create Logic Resource (LB)” command ( ). 来创建一个新的逻辑块。定义LB的名称为WeldCounter，点击Apply。点到Entries标签栏，创建一个新的布尔进口值，命名为Process_End” ，该进口值需要知道生产循环在什么时候 完成，所以我们会把适当的信号和他连接起来。
选到Connected Signals图表，并从Signal Viewer中选择R2 WELD_end信号。如果你愿意，你也可以键入信号名称。 一旦一个有效的信号被选中，您还可以看到它被分配给主入口表的“Connected Signals”列中的条目。切换到Parameters标签栏，并创建一个整数参数，命名其为ProducedParts”。在Value Expression框中，键入ProducedParts + Process_End。切换到Exits标签栏，创建一个新的整数出口，命名为“TotalProducedParts。在exit标签中的Value Expression section里面，键入ProducedParts并创建新的输出信号，然后将WeldCounter_TotalProducedParts”信号添加到Simulation Panel。展开Operation tree，找到“TIP DRESS”操作组合，设为当前操作并运行仿真。如您所见，TIP DRESS”操作组合包含了 两个电极帽修模操作（R2和R3）打开“SERVICE” 操作的过渡条件，该过渡引导向了两个组合 “WELD” 和“TIP DRESS”使用Edit Logic Resource命令打开WeldCounter” 。切换到进口标签栏，增加新的入口信号：ResetMaintenance并将其连接到R2 TDR_end。切换到参数标签栏，创建如下的新的参数切换到constants 常数标签，增加一个整数常数：NeedMaintenance。设置值为3、切换到exit标签，创建一个新的出口：ExecuteMaintenance” ，创建一个新的输出信号和它连接， 在value expression 处，键入： ("WeldCycles" = "NeedMaintenance") AND NOT ("NeedMaintenance" = "0")结束modeling。保存。
#### 智能组件：
学会创建一个直接作用于信号而没有任何操作的夹头。实现带有1个输入信号的夹头。当输入信号等于true时，夹具将移动到打开姿态，为false时夹头将移动到闭合姿态。进一步学习一个双程入口的夹头。与上一个练习中的夹头类型不同：这里的夹头有两个输入，一是移动为OPEN 姿态，另一个是移动为CLOSE姿态。使用自动的工具（指令）实现转台逻辑行为。通过copy夹头的逻辑来实现抓手的逻辑行为。定义一个升降机，它具有两个定位：上和下。另外，在升降机上定位一个双程入口连续运动的夹头。
## 4．工艺逻辑管理：
Editing Modules 编辑模块 1、在Inventory区域选择任意一个模块，在Controls Editor工具栏上点击Edit Control按钮。在Modules Viewer上选择New Entry按钮，开始着手在Expression area表达式区域中键入现有信号的名称，系统将向您显示下拉列表中的可能自动完成（输入）的信号或函数现在，点击Resulting Signal栏。通过在Signal Viewer中点击信号来插入信号。单击“Add as New添加为新”将Resulting Signal所得的信号字段和 Expression表达式区域的内容移到表中。
## 5.机器人控制器，路径，程序信号，OLP。
#### 解决机器人的干涉问题：
切换到Standard模式（不保存），然后运行„Automatic Interference“命令。将全部的两个Weld焊接操作加 入进去，然后使用默认设置来执行。右键点击R002，然后选择„Robot Signals。添加2个输入信号和2个输出信号。将类似的信号添加到R003中.关闭对话框，添加“R3_Weld”操作到Path Editor。点击“via10”位置的OLP 命令列所在的那行移除现存的OLP，点击添加Standard CommandsSynchronizationSet Signal在Signal Name下拉菜单选择„ReqZone“并设置Expression为1.然后，添加一个（名称为）„ReqZoneOK，值为1的 WaitSignal命令增加2个额外的OLP命令来处理InZone信号在“start”位置，增加如下命令 16、对于机器人R002的相应的位置„via5“ 和 „via7“（而非„via10“ and „via2“），重复以上两步。点击„Import Module(s)，浏览到...SystemRoot\PLC\RobotSyncModule.xml.在Modules Viewer中，点击导入„RobotSync“的模块并将其从Modules Inventory目录拖拽到Modules Hierarchy目录。将„R3_Weld“的过渡条件从无改为„R3 Weld_end“ ，并移除“R2 Weld”的过渡。
学习使用模块在机器人和设备之间进行信号交换。
导入机器人信号，创建新模块，添加输入。把信号添加到simulation panel中。
## 6.器人宏，传送带。
#### 传送带：
选择并modeling传送带，定义传送带，定义雪橇。
创建机运线的控制点，将机运线与其他设备相连接。
#### 机器人宏：
控制机器人与设备控制器之间的数据交换，创建可重用宏指令
## 7.机器人虚拟调试基础
学习创建机器人程序并将其设为默认，将路径添加到程序，并用机器人程序运行仿真。在“STATION”下创建4个新的复合compound操作，命名为：ROBOT OPERATIONS, R1, R2 和R3。创建一个新的Non-Sim操作，命名为“BLOCK” ,并将其与R1, R2 ,R3一起，放在ROBOT OPERATIONS下面。在Sequence Editor中，将BLOCK与R1, R2 ,R3连接，并将过渡条件设置为FALSE。这将确保在这3个复合操 作下的任何操作都不会从SOP执行。、从机器人的当前结构位置剪切所有机器人操作，并根据其执行机器人将其粘贴在每一个新的相应的复合操作下。运行仿真并注意“Running”列指示。正如预期的那样，块操作是不断运行的，但会阻止任何机器人操作运行。在接下来的步骤中，我们将创建机器人程序来执行这些机器人操作。选择R001机器人，打开Robotic Program Inventory 命令。
创建一个新的程序。选择程序，将其设置为默认程序（以粗体突出显示），然后在程序编辑器中打开。将新程序添加到Path Editor路径编辑器中。选择新创建的操作，在Path Editor中选择Add Operation to Program。在Path#列中设置值为2. 这个号码将作为使机器人执行R1 MAIN操作的程序号。选中R001机器人，打开Robot Signals对话框。我们将添加状态信号到R001的现存信号列表中。创建如下3个输出信号将为R001定义主循环。强制R1 CONTINUE_1信号为TRUE，R001开始执行R1 LOAD PART，在R001开始移动之后，将信号强制回FALSE。当焊接完成后，强制R1 CONTINUE_2信号为TURE，R001开始执行R1 REMOVE PART操作。在R001开始移动之 后，将信号强制回FALSE。执行R1 LOAD PART操作的条件是 "proximity_sensor" ；执行R1 REMOVE PART操作的条件是R3 WELD_end。我们还需要对R002和R003也进行同样的（编辑处理）过程。
## 8.从cee到外部控制
安装PS15.0, PLCSIM Advanced2.0。打开西门子PLC软件Tia15软件新建一个项目，项目名：Test,双击添加设备添加PLC型号：CPU 1500系列，版本号选择最高的。设置CPU的以太网地址，在变量表中设置相关变量。启动PLCSIM Advanced，地址为192.168.0.1，与CPU的IP地址一致，选择PLC进行下载（备注：选择项目属性→保护→块编译时支持仿真），按F6，打开操作栏，选择操作栏中的PLC，选择PLCSIM Advanced通讯 方式，选择通讯的方式分为信号地址通讯和信号名称通讯，这里我们分别进 行选择测试，将需要通讯的变量进行设置，选择连接PLC，选择外部连接。之后进行通讯测试。信号标签通讯无问题后，可以进行地址通讯测试，如下图所示，将PS中的信号的地址与PLC中的信号地址对应上。之后进行通讯测试。接着上一个PLC的项目，选择属性，勾选激活OPC服务器。在OPC UA的运行许可证中选择许可证，选择允许来自远程对象的通讯访问，打开外部连接，选择OPC UA的通讯方式，在host name选择和PLC中相同的以太网地址（192.168.0.10）（备注：如果没有在PLCSIM Advanced中新建实例还是原来的实例的话，选择 192.168.0.1即可。），在signal viewer中选择OPC-UA通讯模式的信号。之后在博图中对这些信号进行测试。PS中采用OPC-UA的方式，信号名称要与博图变量表中的信号一致。
## 体会与小结。
在生产实习之中，学习了了解自动化技术人员是如何设计自动化生产流程项目的，也了解了同步工程、虚拟工艺、制造技术、数字孪生，在运用Tecnomatix Process Simulation软件中，与之前在学校学习到的各种设备仿真有所不同，Tecnomatix Process Simulation软件更像是个集大成者，直接将工厂，流水线，作为模拟的一部分，不仅能作为考量现实意义的一部分，也能作为企业项目评估，流水线生产效率估算的一部分，更加基于现实，符合现实情况，在现实生活中不单单是物件物料的单纯堆叠，也要考虑经济性，效率性等因素。
