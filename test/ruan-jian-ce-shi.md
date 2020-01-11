# 软件测试

## 测试的16种类型 

1. 功能测试

菜单、工具栏、快捷键、下拉框、按钮、单选按钮、复选按钮、切换、连接、触发键

2. 界面测试

登录界面、总界面、输入界面（增删改查）、处理界面、输出界面、报表界面、提示界面

3. 易错测试

数据长度、数据类型、非法此操作

4. 接口测试

接口测试也叫业务流程测试（包括共嗯那个模块之间、模块与模块之间、子系统之间）

内部接口：例如：导入、导出（通俗的讲是接口就是调用）

外部接口：

5. 性能测试（tps吞吐量、响应速度、cpu占用率、内存占用率）

平均吞吐量：单位时间内处理事务个数

平均响应速度：做一个事务处理所用时间

例如：界面操作效率测试；报表输出及查询效率测试

6. 负载测试（压力测试、强度测试、容量测试）

压力测试即就是最大用户测试（针对B/S而言）

容量测试即就是最大数据量测试

7. 并发测试

指多个用户在同一时间对同一条数据的删除或者修改等处理

8. 稳定测试

例如：1小时触发600条信息，那么8个、10个等发信息的条数测试

9. 恢复测试

突然断电（系统触发正常启动；数据包要在断电的地方继续进行处理）

10. 配置测试

最低配合：

推荐配置：大多数用户所用的配置

11. 安装测试

安装过程；卸载过程；

12. 文档测试

交给用户的文档。例如：系统帮助、用户使用手册、用户安装手册

13. 可用性测试（靠经验）

14. 初始化测试

是指系统刚刚安装完成后，在数据位空的情况下，如果被调用的模块为空，点击调用模块的时候，是否进行容错的测试。

15. 数据完整性

是指当主表的某一条件信息被删除后，和这一条相关的从表的信息都应该被删除

如果某些数据的主键是由数据库本身而实现的，可以不用删除，如果有些主表是由程序员写的代码而实现，则要进行数据完整性测试。

16. 种测试类型归类

1、此软件能做什么？

针对数据进行”功能、接口、容错、界面、权限、初始化、数据完整性测试“

2、软件做的怎么样？

性能、负载、恢复、稳定性、并发、系统安全

3、软件在什么环境条件下做？

配置、安装、文档、可用性
