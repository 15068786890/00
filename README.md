# EASY-DBF工具包bb


## 1.前传：
### 1.1  前言：
dbf是一种比较老的数据库，对于其他行业可能已经意义不大，但是对教育系统来说，因为各种历史系统的原因，还是非常重要的，其api相对较复杂，并且学习价值并不大，故参照easy-poi的思路，单独做了封装。


### 1.2  dbf存在的问题：
dbf文件本身存在一些问题需要了解，这关系到DBF注解的设置

①列的名称最长10字节，即10个英文字母或者5个中文汉字

②fieldLength决定了当前列显示的宽度，定义多长显示就会多长。如果单元格内容的值超过fieldLength，超过部分的值将被截断

③fieldLength最长255个字节，所以每一行记录中每个单元格内容的值不会超过255字节

④dbf的数值类型Numeric，可以设置小数位，所以java实体类中数值的字段必须定义为Double类型

⑤由于导入导出需要赋值，java实体类中的每个属性都要设置get/set方法


### 1.3  dbf工具：
dbf是一种特殊的文件格式，需要使用工具来编辑dbf文件，比如DBF Manager，http://www.downcc.com/soft/289750.html（下载地址）

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic1.png?at=refs%2Fheads%2Fmaster)


### 1.4  测试项目：
http://172.172.1.11:7990/scm/eas/easy-dbf.git

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic2.png?at=refs%2Fheads%2Fmaster)

导出方法：

①普通导出

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic3.png?at=refs%2Fheads%2Fmaster)

②对于导入失败的数据，进行导出

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic4.png?at=refs%2Fheads%2Fmaster)

导入方法：

①只返回导入成功的list

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic5.png?at=refs%2Fheads%2Fmaster)

②返回一个对象DbfImportResult，包含导入成功和失败的list，导入的列，是否存在校验失败

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic6.png?at=refs%2Fheads%2Fmaster)

DbfImportResult：

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic7.png?at=refs%2Fheads%2Fmaster)


### 1.5  感谢EasyPoi教程：
http://easypoi.mydoc.io


## 2.DBF导入导出
### 2.1 注解介绍：
@Dbf 作用到filed上面,是对dbf一列的一个描述

@DbfIgnore 和名字一样表示这个字段被忽略，跳过这个导入导出

@DbfTarget 这个是作用于最外层的对象,描述这个对象的id,以便支持一个对象可以针对不同导入导出做出不同处理

@DbfEntity 表示一个继续深入导出的实体,告诉系统这个对象里面同样有导入导出的字段

### 2.2   @DBF：
这个是必须使用的注解,如果需求简单只使用这一个注解也是可以的,涵盖了常用的dbf需求

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic8.png?at=refs%2Fheads%2Fmaster)

dataType：

DBFField.FIELD_TYPE_C    字符串类型   对应实体类String

DBFField.FIELD_TYPE_D    日期类型     对应实体类Date

DBFField.FIELD_TYPE_N    数值类型     对应实体类Double  

通过@Dbf的decimalCount设置小数位数

DBFField.FIELD_TYPE_F     浮点类型    对应实体类Double 

如果定义的dataType和数据类型对应错误，javadbf会抛出异常

 "Invalid value for field " + i     i是数值，表示当前是第几个字段
 
### 2.3  Demo使用@Dbf、@DbfIgnore：
导出：

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic9.png?at=refs%2Fheads%2Fmaster)

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic10.png?at=refs%2Fheads%2Fmaster)

得到文件zkz.dbf

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic11.png?at=refs%2Fheads%2Fmaster)

导出的文件可以导入：

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic12.png?at=refs%2Fheads%2Fmaster)

### 2.4  @DbfTarget
这个是作用于最外层的对象,描述这个对象的id,以便支持一个对象可以针对不同导入导出做出不同处理

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic13.png?at=refs%2Fheads%2Fmaster)


### 2.5  Demo使用@DbfTarget

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic14.png?at=refs%2Fheads%2Fmaster)

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic15.png?at=refs%2Fheads%2Fmaster)


得到文件target.dbf，可以看到第一个字段叫sXm，导出的文件同样可以导入

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic16.png?at=refs%2Fheads%2Fmaster)

如果使用@DbfTarget注解对某些字段做处理，需要对@Dbf的属性name做这样的赋值

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic17.png?at=refs%2Fheads%2Fmaster)

当@DbfTarget的value是target2时，stuName导出的字段就叫sXm

当@DbfTarget的value是target1时，stuName导出的字段就叫sName

注意点：

①如果@Dbf的name中不带下划线 _  这个字段正常导出

②@Dbf的name中带下划线 _ 但是不包含value值，这个字段不会被导出

        
比如

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic18.png?at=refs%2Fheads%2Fmaster)

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic19.png?at=refs%2Fheads%2Fmaster)

name中不包含target2，所以stuName字段就不导出

### 2.6  @DbfEntity
标记是不是实体类,标记是否继续穿透

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic20.png?at=refs%2Fheads%2Fmaster)


### 2.7  Demo使用@DbfEntity

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic21.png?at=refs%2Fheads%2Fmaster)

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic22.png?at=refs%2Fheads%2Fmaster)

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic23.png?at=refs%2Fheads%2Fmaster)



得到entity.dbf

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic24.png?at=refs%2Fheads%2Fmaster)

①因为@DbfEntity定义了id="target1"，所以StudentEntity的stuName导出名字叫sName

②因为@DbfEntity定义了name = "stu"，而且show = true，所以StudentEntity的每个字段导出名字前面都带了stu_

### 2.8  导入参数设置DbfImportParams

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic25.png?at=refs%2Fheads%2Fmaster)

先通过importFields needCheckOrder校验模板是否符合要求，模板不合法直接抛出异常

后通过needVerify verifyGroup校验数据，分为成功的list和失败的list分别返回

### 2.9  Demo使用DbfImportParams

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic26.png?at=refs%2Fheads%2Fmaster)

先导出2条数据，一条姓名长度1，另外一条姓名长度2

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic27.png?at=refs%2Fheads%2Fmaster)

导入测试1：

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic28.png?at=refs%2Fheads%2Fmaster)

由于导入的模板中的不包含importFields中的"学生名字"，所以抛出异常

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic29.png?at=refs%2Fheads%2Fmaster)


导入测试2：

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic30.png?at=refs%2Fheads%2Fmaster)

由于导入模板中有3列，校验只有2列，所以抛出异常

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic31.png?at=refs%2Fheads%2Fmaster)

如果数量一致，才会去判断列名是否相等

导入测试3：

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic32.png?at=refs%2Fheads%2Fmaster)

StuName @Size设置的groups和校验的groups都是AddGroup.class，所以会验证stuName的长度，一条校验成功，一条校验失败，并且存在校验失败标记verfiyFail为true

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic33.png?at=refs%2Fheads%2Fmaster)


### 2.10  导入方法的返回值
①DbfImportUtil.importDbf        返回导入成功的list

②DbfImportUtil.importDbfMore   返回DbfImportResult对象

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic34.png?at=refs%2Fheads%2Fmaster)


拿到failList和fieldList，可以用来导出失败的数据


### 2.11  Demo 导出（导入失败的数据）

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic35.png?at=refs%2Fheads%2Fmaster)

先导出3条数据，第一条数据姓名长度2，第二三条数据姓名长度3

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic36.png?at=refs%2Fheads%2Fmaster)

开始导入：

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic37.png?at=refs%2Fheads%2Fmaster)

因为校验了姓名长度，所以有1条成功数据，2条失败数据。拿到失败数据failList和列集合fieldList，调用DbfExportUtil的exportFailDbf方法导出失败的2列数据。导出会新增一列message，用于记录校验失败的原因

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic38.png?at=refs%2Fheads%2Fmaster)

得到失败数据文件zkzFail.dbf

![Image text](http://172.172.1.11:7990/projects/EAS/repos/easy-dbf/raw/img-folder/pic39.png?at=refs%2Fheads%2Fmaster)

