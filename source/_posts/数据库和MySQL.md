---
translate_title: ''
---
# 数据库和MySQL

## 关系数据库的关系完整性

### 实体完整性

实体完整性规则：如果一个属性A是基本关系R的**主属性（主键，主码）**，则属性A不能不能取**null**。

### 参数完整性

#### 举例

在关系模型中实体与实体之间的联系都是通过关系来描述的。

​	学生(<u>学号</u>，姓名，性别，专业号，年龄)

​	专业(<u>专业号</u>，专业名)

学生关系中专业号必须是专业关系中的专业号。也就是学生关系中的专业号属性需要参考专业关系的专业属性。

​	学生(<u>学号</u>，姓名，性别，专业号，年龄)

​	课程(<u>课程号</u>，课程名，学分)

​	选修(<u>学号</u>，<u>课程号</u>)

选修关系中的学号参考小学生关系中的学号，选修关系中的课程号参考课程关系中的课程号

​	学生(<u>学号</u>，姓名，性别，专业号，年龄，班长)

学生关系中的班长属性参考学生关系中的学号。

#### 参照完整性

若属性F是基本关系R的外键，它与基本关系S的主键K相对应，对于R中的每个元组的F的值必须是

- NULL
- S关系中某个元组的主键值

### 用户定义的完整性

针对某一具体关系数据库的约束条件，它反映某一具体应用所涉及的数据必须满足的条件。

例如：某个属性不能为NULL。某一属性的取值范围为0~100。

## 关系运算

自然连接：是等值连接，要求两个关系中进行比较的属性是同名的，在结果中去掉重复的属性。

两个关系中一个关系可能存在与另一个不同的属性值，这些元组称为悬浮元组，把空值设置为NULL这种连接叫做**外连接**

只保留左边关系的NULL值为**左外连接**

只保留右边关系的NULL值为**右外连接**

关系R

![image-20191125131412660](D:\面试\springbootimages\image-20191125131412660.png)

关系S

![image-20191125131453232](D:\面试\springbootimages\image-20191125131453232.png)

自然连接

![image-20191125131746271](D:\面试\springbootimages\image-20191125131746271.png)

外连接

![image-20191125131538010](D:\面试\springbootimages\image-20191125131538010.png)

左外连接

![image-20191125131617039](D:\面试\springbootimages\image-20191125131617039.png)

右外连接

![image-20191125131707613](D:\面试\springbootimages\image-20191125131707613.png)

## MySQL概述

### 概念

#### 数据库

数据库是**文件的集合**，是按照某种数据模式组织起来的并存放于二级存储器中的数据集合。

#### 数据库实例

数据库实例是**程序**，这是位于用户与操作系统之间的一层数据管理软件，用户对数据库数据的任何操作，包括数据库定义，数据查询，数据维护，数据库运行控制等都是在数据库实例下进行的，应用程序只有通过数据库实例才能与数据库打交道。

通俗说法：

数据库就是一个个文件组成，要对这些文件执行诸如SELECTINSERTUPDATEDELETE之类的数据库操作是不能通过简单地操作文件来修改数据库的内容，需要通过数据库实例来完成对数据库的操作。

### MySQL体系结构

![img](D:\面试\springbootimages\MySQL体系结构.jpg)

MySQL区别于其他数据库的重要特点就是插入式的表存储引擎。MySQL插入式的存储引擎架构提供了一系列标准的管理和服务支持，这些标准与存储引擎无关。

## 存储引擎

**联机分析处理OLAP(On-Line Analytical Processing)** **联机事务处理OLTP(On-line transaction processing)**。OLTP是传统的关系型数据库的主要应用，主要基本的、日常的事务处理，例如银行交易。

OLAP是数据仓库系统的主要应用，支持复杂的分析操作，侧重决策支持，并且提供直观易懂的查询结果。

OLTP系统强调数据库内存效率，强调内存各种指标命令率，强调绑定变量，强调并发操作。

OLAP系统则强调数据分析，强调SQL执行市场，强调磁盘I/O,强调分区等。

**ETL**，是英文Extract-Transform-Load的缩写，用来描述将数据从来源端经过萃取（extract）、转置（transform）、加载（load）至目的端的过程。

### InnoDB

#### Master Thread的工作方式

Master Thread是最高优先级的线程，内部有多个线程，主循环（loop）、后台循环（background loop）、刷新循环（flush loop）、暂停循环（suspend loop）。

##### innodb1.0.x版本以前

###### 主循环loop：每秒的操作和每10秒的操作

**每秒的操作：**

- 日志缓存刷新到磁盘，即使事务还没有提交（总是执行）
- 合并插入缓存（可能执行）
- 至多刷新100个InnoDB的缓冲池中的脏页到磁盘（可能执行）
- 如果没有当前用户活动，切换到后台循环（background loop）

合并插入缓冲，如果在前一秒IO次数小于5认为IO压力不大，可以执行合并操作。

判断缓冲池中脏页比例，超多阈值则认为需要进行磁盘同步操作，将100个脏页写入磁盘。

**每十秒的操作：**

- 刷新100个脏页到磁盘（可能执行）
- 合并至多5个插入缓冲
- 将日志缓冲刷新到磁盘
- 删除无用undo页
- 刷新100个或10个脏页到磁盘

判断十秒内IO操作是否小于200，如果小于刷新100个脏页到磁盘。

判断缓冲池中脏页比例，超过70%，刷新100个脏页，小于70%刷新10个脏页到磁盘

###### 后台循环:

- 删除无用的Undo页
- 合并20个插入缓冲
- 跳回主循环
- 刷新100页直到符合条件

```c
void master_thread(){
    goto loop
loop:
//每秒的操作
for(int i = 0; i < 10; i++){
    thread_sleep(1)
        do log buffer flush to disk
    if(last_one_second_ios < 5)
        do merge at most 5 insert buffer
    if(buf_get_modified_ratio_pct > innodb_max_dirty_pages_pct)
        do bufffer pool flush 100 dirty page
    if(on user activity )
        goto background loop
 }
//十秒的操作
if（last_ten_second_ios<200)
    do buffer pool flush 100 dirty page
do merge at most 5 insert buffer
do log buffer flush to disk
do flush purge
if(buf_get_modified_ratio_pct > 70%)
    do buffer pool flush 100 dirty page
else
    do buffer pool flush 10 dirty page
goto loop
background loop:
    do flush purge //删除无用Undo页
    do merge 20 insert buffer
    if not idle:
    	goto loop
    else
        goto flush loop
flush loop:
    do buffer pool flush 100 dirty page
    if(buf_get_modified_ratio_pct > innodb_max_dirty_pages_pct)
        goto flush loop
    else
        goto suspend loop
suspend loop:
    suspend_thread()
    waiting event
    goto loop
}
```

##### innodb1.2.x版本之前

合并插入缓冲的数量：innodb_io_capacity值的5%

从缓冲池刷新脏页的数量：innodb_io_capacity.

innodb_max_dirty_pages_pct的默认值由90%改为75%

引入innodb_adaptive_flushing自适应刷新，影响每秒脏页刷新的数量。通过buf_flush_get_desired_flush_rate的函数判断需要刷新的脏页的合适数量。

引入innodb_purge_batch_size控制每次purge回收Undo页面的数量。

```c
void master_thread(){
    goto loop
loop:
//每秒的操作
for(int i = 0; i < 10; i++){
    thread_sleep(1)
        do log buffer flush to disk
    if(last_one_second_ios < 5% innodb_io_capacity)
        do merge 5% innodb_io_capacity insert buffer
    if(buf_get_modified_ratio_pct > innodb_max_dirty_pages_pct)
        do bufffer pool flush 100% innodb_io_capacity dirty page
    else if enable adaptive flush
        do buffer pool flush desired amount dirty page
    if(on user activity )
        goto background loop
 }
//十秒的操作
if（last_ten_second_ios<innodb_io_capacity)
    do buffer pool flush 100% innodb_io_capacity dirty page
do merge 5% innodb_io_capacity insert buffer
do log buffer flush to disk
do flush purge
if(buf_get_modified_ratio_pct > 70%)
    do buffer pool flush 100% innodb_io_capacity dirty page
else
    do buffer pool flush 10% innodb_io_capacity dirty page
goto loop
background loop:
    do flush purge //删除无用Undo页
    do merge 100% innodb_io_capacity insert buffer
    if not idle:
    	goto loop
    else
        goto flush loop
flush loop:
    do buffer pool flush 100% innodb_io_capacity dirty page
    if(buf_get_modified_ratio_pct > innodb_max_dirty_pages_pct)
        goto flush loop
    else
        goto suspend loop
suspend loop:
    suspend_thread()
    waiting event
    goto loop
}
```

##### innodb 1.2.x版本

```C
if Innodb is idle
    srv_master_do_idle_taskd();//每十秒的操作
else
    srv_master_do_active_tasks();//每一秒的操作
//刷新脏页的操作从Master Thread的线程中分离到单独的Page Cleaner Thread中。
```

### MyISAM

#### 存储过程

![image-20191110094747670](D:\面试\springbootimages\image-20191110094747670.png)



![image-20191110094837584](D:\面试\springbootimages\image-20191110094837584.png)

![image-20191110094617354](D:\面试\springbootimages\image-20191110094617354.png)

![image-20191110094642472](D:\面试\springbootimages\image-20191110094642472.png)