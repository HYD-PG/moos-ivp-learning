# moos-ivp-learning
- ROS机器人操作系统：
-https://www.ros.org/blog/getting-started/
-Ubuntu下载安装ROS终端命令步骤
-http://wiki.ros.org/hydro/Installation/Source
-订阅和发送照片命令行代码：
-https://blog.csdn.net/hpuliyang/article/details/106695512
----------------------------------------------------------------
-编译开始moos-ivp命令：cd ~/moos-ivp
- ./build-moos.sh 

-./build-ivp.sh
-g++ -o wenjian wenjian.cpp
-----------------------------------------------------------------
-MOOS简介：
-最小配置启动MOOS：wget --no-check-certificate http://oceanai.mit.edu/2.680/examples/moosdb_alpha.moos
-启动后配置：MOOSDB moosdb_alpha.moos
-查看启动后的状态可以使用指令：uXMS --all
-uPokeDB:可以更改MOOSDB中的其他变量   eg:uPokeDB DEPLOY=true SPEED=2 alpha.moos
-但是如上面所示：输入指令时需要挨个打开终端比较麻烦，因此可以使用指令pAntler:
-首先利用vi或者vim创立一个mission文件，具体方法为输入：vi 文件名
-然后在其中将运行的数量和代码写入其中eg:
-// (wget http://oceanai.mit.edu/2.680/examples/db_and_uxms.moos)
-ServerHost = localhost//IP地址
-ServerPort = 9000//端口
-Community = alpha
-ProcessConfig = ANTLER
-{
-MSBetweenLaunches = 200
-Run = MOOSDB  @ NewConsole = false
-Run = uXMS  @ NewConsole = true//运行的两个指令
-}
-ProcessConfig = uXMS
-{
-AppTick  = 4
-CommsTick = 4
-VAR  = DB_CLIENTS, DB_UPTIME, DB_TIME
-DISPLAY_SOURCE = true
-DISPLAY_TIME  = true
-COLOR_MAP  = DB_CLIENTS, red//变量
-}
-最后保存输入pAntler 文件名---程序便一起运行
------------------------------------------------------------------------------
-#ls 命令用于显示指定工作目录下之内容
-所用参数：
--a 显示所有文件及目录
--l 除文件名称外，亦将文件型态、权限、拥有者、文件大小等资讯详细列出
--t 将文件依建立时间之先后次序列出
--F 在列出的文件名称后加一符号；例如可执行档则加 “*”, 目录则加 “/”
--R 若目录下有文件，则以下之文件亦皆依序列出
-————————————————-------------------------------------------------
-建立moosAPP应用：1.进入moos-ivp-extend/src文件中运行MyGenMOOSApp 文件名 p “作者”--------(问题：出现环境变量无法解决的问题,可进行换源)
-2.打开文件目录moos-ivp-extend/src/CMakeLists.txt，添加app名字：ADD_SUBDTRECTORY（p文件名）
-3.保存好后进行运行编译：./build.sh并进行相关的查阅 which p文件名(完成MOOSAPP的创建)
-4.找到自己的文件后缀名为.h类型的文件，进行变量的编写打开后缀名为.cpp 对变量进行赋值(若想实现订阅功能，则需要在.cpp文件中的迭代函数中使用Notify函数可向MOOSDB中传输数据，然后可以打开相关文件运行（换源）)
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
-C++类的相关知识：字符串转数字：strtoul()-----eg:string str = "5674032334252535"  uint64_t value = strtoul(str.c_str(), NULL, 0);
-将接受和发布数据的内容先全部都写到OnNewMail()函数当中（函数位于.cpp文件中）
- pAntler --MOOSTimeWarp=10 alder.moos

-----------------------------------------------------------------------------------------------------------------------------------
-设置发布新的变量：
-1.首先通过打开目录moos-ivp-extend/src      创建文件名称：MyGenMOOSApp 文件名 p "作者名"
2.打开文件目录moos-ivp-extend/src/CMakeLists.txt打开这个txt文件，添加ADD_SUBDIRECTORY(文件名)
3.cd ~/moos-ivp-extend   ./build.sh进行编译运行
4.从src进入到其中的文件，找到后缀名为.h的文档进行变量的添加  eg:int m_value;并进行保存
5.然后打开后缀名为.cpp的文件，在Constructor中对初始变量赋值（或者利用后面的mission文件中的事件event命令进行修改赋值，此项往往被使用）并在RegisterVariables()中进行订阅变量Register("变量名"，0)
6.在OnNewMail()中编写判断条件或者运行数学逻辑，利用Notify函数进行发布（个人认为此函数写在Iterate中也可以）
7.打开moos-ivp-extend中的build文件，输入cmake,运行后再输入make进行运行
8.启动MOOSDB，查看启动后的状态可以使用指令：uXMS --all(一直启动即可)
//uPokeDB:可以更改MOOSDB中的其他变量   eg:uPokeDB 变量赋值 文件名.moos//（此项可有可无，只是可利用其进行变量的修改）
但是如上面所示：输入指令时需要挨个打开终端比较麻烦，因此可以使用指令pAntler:
首先利用vi或者vim创立一个mission文件，具体方法为输入：vi 文件名
然后在其中将运行的数量和代码写入其中eg:
// (wget http://oceanai.mit.edu/2.680/examples/db_and_uxms.moos)
ServerHost = localhost//IP地址
ServerPort = 9000//端口
Community = alpha
ProcessConfig = ANTLER
{
MSBetweenLaunches = 200
Run = MOOSDB  @ NewConsole = false
Run = uXMS  @ NewConsole = true//运行的两个指令
Run = uTimerScript @ NewConsole = false //可添加
Run=自己所建文件
}
ProcessConfig = uXMS
{
AppTick  = 4
CommsTick = 4
VAR  = DB_CLIENTS, DB_UPTIME, DB_TIME
vent = var=NUM_VALUE, val="22", time = 0.50
   event = var=NUM_VALUE, val="234", time = 1.00
   event = var=NUM_VALUE, val="117", time = 1.50
   reset_max = nolimit
   reset_time = all-posted//输入事件，以上为例子，可进行更改
}
9.pAntler 文件名.moos,查看控制台输出指令
------------------------------------------------
查看日志：aloggrep MOOSLog_27_10_2020_____09_22_24.alog NUM_RESULT NUM_VALUE
-------------------------------------------------------------------------------------------------------------------------------------------
配置helm文件（自动驾驶）：
mkdir moos-ivp-jsmith/missions/lab_07
cd moos-ivp-jsmith/missions/lab07/s1_alpha
pAntler --MOOSTimeWarp=10 alpha.moos

Behavior=BHV_Loiter
{
  name = east_zone
  pwt  = 100

  condition = DEPLOY = true
  condition = RETURN = false
  condition = LOITER_REGION=east
  updates    = LOITER_UPDATE
  duration = 150  //行为执行150s后，行为结束
  endflag=LOITER_REGION=west//换区域
  duration_idle_decay = false
  perpetual  = true

  // 题目要求的相关参数设定
   // ------------------------------------
      acquire_dist = 10               // default
      capture_radius = 3                // default  
      center_activate = false            // default
      clockwise = false             // default
      slip_radius = 15               // default
      speed = 1.3                // default
      spiral_factor = -2               // default

      polygon = radial:: x=120, y=-75, radius=20, pts=8 // 重心、半径、定点数
      post_suffix = HENRY                             // example


  // 在界面上画出相关的图形
        visual_hints = vertex_size  = 1             // default
        visual_hints = edge_size    = 1             // default
        visual_hints = vertex_color = dodger_blue   // default
        visual_hints = edge_color   = white         // default
        visual_hints = nextpt_color = yellow        // default
        visual_hints = nextpt_lcolor = aqua         // default
        visual_hints = nextpt_vertex_size = 5       // default
        visual_hints = label = west_zone         // example

}
//返回点配置
Behavior=BHV_Waypoint
{
  name       = waypt_return
  pwt        = 100
  condition  = RETURN = true
  condition  = DEPLOY = true
  perpetual  = true
  updates    = RETURN_UPDATE
  endflag    = RETURN = false
  endflag    = DEPLOY = false
  endflag    = MISSION = complete

           speed = 2.0
  capture_radius = 2.0
     slip_radius = 8.0
          points = 0,0
}
-----------------------------------------------------------------------------------------
pShare的用法：
设立社区的过程：（1）初始化：Community=apples  （2）运行Run=pShare Run= pLogger【运行时给予初始值是true or false】 并添加ProcessConfig = pLogger
{
  AsyncLog = true
  WildCardLogging = true
}
--------------------------------------------------------------
pShare的具体含义：
src_name：共享变量的名称
input=route=localhost:9201
output=src_name=PEARS,route=localhost:9200（注意共享的两个社区端口要互反）
如上，另一个社区为
input=route=localhost:9200
output=src_name=APPLES,route=localhost:9201

gen_hazards --polygon=



---------------------------------------------------
更改背景地图tiff_file = data/portsmouth2.tif
并且在plogger模块中添加 log_the_image = true
PLOGGER_CMD = COPY_FILE_REQUEST = /home/jake/images/lake_george.tif
PLOGGER_CMD = COPY_FILE_REQUEST = /home/jake/images/lake_george.info


--------------------------------
与信标通信：uFldBeaconRangeSensor 
https://oceanai.mit.edu/ivpman/pmwiki/pmwiki.php?n=IvPTools.UFldBeaconRangeSensor
ProcessConfig = uFldBeaconRangeSensor
  {
  AppTick   = 4   // Standard MOOSApp configurations
  CommsTick = 4

// Configuring aspects of te vehicles
reach_distance = default = nolimit
ping_wait      = 30
ping_payments  = upon_accept

 report_vars    = short

default_beacon_freq  = never       // Only on request (ping)
default_beacon_shape = circle
default_beacon_color = orange
default_beacon_width = 5
default_beacon_report_range = 100
 beacon  = label=01, x=200, y=0
beacon  = label=02, x=400, y=-200
  beacon  = label=03, x=0,   y=-200, color=red, shape=triangle, report_range=80
}
------------------------------------------------------------------------------------------------
hazard路径模拟
pHandleSensorData   传感器数据输出
uFldNodeComms 与岸基进行信息传输，确定AUV的位置与轨迹
pHostInfo:MOOSapp自动发现自己的IP地址，并将其传输给MOOSDB
uFldNodeBroker:寻找岸基系统，确定其IP地址和pShare的输入路线以及确定本地的输出线路（一般为IP地址）
uFldshoreBroker:发送shoreside的IP地址与pShare的输入线路，自动确定本地的输出IP地址

