### liquibase在SpringBoot项目中的使用

#### 简介
Liquibase 是一个用于跟踪、管理和应用数据库变化的开源的数据库重构工具。它将所有数据库的变化（包括结构和数据）都保存在 changelog文件中，便于版本控制，它的目标是提供一种数据库类型无关的解决方案，通过执行 schema 类型的文件来达到迁移。

官方文档：
(<https://docs.liquibase.com/tools-integrations/springboot/using-springboot-with-maven.html>)
#### 使用效果展示
```
2020-07-21 11:07:22.398  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : SELECT COUNT(*) FROM hzcx.DATABASECHANGELOGLOCK
2020-07-21 11:07:22.430  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : CREATE TABLE hzcx.DATABASECHANGELOGLOCK (ID INT NOT NULL, `LOCKED` BIT(1) NOT NULL, LOCKGRANTED datetime NULL, LOCKEDBY VARCHAR(255) NULL, CONSTRAINT PK_DATABASECHANGELOGLOCK PRIMARY KEY (ID))
2020-07-21 11:07:22.452  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : SELECT COUNT(*) FROM hzcx.DATABASECHANGELOGLOCK
2020-07-21 11:07:22.461  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : DELETE FROM hzcx.DATABASECHANGELOGLOCK
2020-07-21 11:07:22.462  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO hzcx.DATABASECHANGELOGLOCK (ID, `LOCKED`) VALUES (1, 0)
2020-07-21 11:07:22.464  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : SELECT `LOCKED` FROM hzcx.DATABASECHANGELOGLOCK WHERE ID=1
2020-07-21 11:07:22.472  INFO 11157 --- [           main] l.lockservice.StandardLockService        : Successfully acquired change log lock
2020-07-21 11:07:22.488  INFO 11157 --- [           main] l.c.StandardChangeLogHistoryService      : Creating database history table with name: hzcx.DATABASECHANGELOG
2020-07-21 11:07:22.490  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : CREATE TABLE hzcx.DATABASECHANGELOG (ID VARCHAR(255) NOT NULL, AUTHOR VARCHAR(255) NOT NULL, FILENAME VARCHAR(255) NOT NULL, DATEEXECUTED datetime NOT NULL, ORDEREXECUTED INT NOT NULL, EXECTYPE VARCHAR(10) NOT NULL, MD5SUM VARCHAR(35) NULL, `DESCRIPTION` VARCHAR(255) NULL, COMMENTS VARCHAR(255) NULL, TAG VARCHAR(255) NULL, LIQUIBASE VARCHAR(20) NULL, CONTEXTS VARCHAR(255) NULL, LABELS VARCHAR(255) NULL, DEPLOYMENT_ID VARCHAR(10) NULL)
2020-07-21 11:07:22.510  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : SELECT COUNT(*) FROM hzcx.DATABASECHANGELOG
2020-07-21 11:07:22.512  INFO 11157 --- [           main] l.c.StandardChangeLogHistoryService      : Reading from hzcx.DATABASECHANGELOG
2020-07-21 11:07:22.513  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : SELECT * FROM hzcx.DATABASECHANGELOG ORDER BY DATEEXECUTED ASC, ORDEREXECUTED ASC
2020-07-21 11:07:22.514  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : SELECT COUNT(*) FROM hzcx.DATABASECHANGELOGLOCK
2020-07-21 11:07:22.532  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : DROP TABLE IF EXISTS `ums_member_user_relation`
2020-07-21 11:07:22.533  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : CREATE TABLE `ums_member_user_relation`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `user_id` int(11) DEFAULT NULL COMMENT '邀请人',
  `member_id` int(11) DEFAULT NULL COMMENT '被邀请人',
  `create_time` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`id`) USING BTREE,
  KEY `user_relation_idx1_user_id` (`user_id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='会员用户关系表'
2020-07-21 11:07:22.547  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : Custom SQL executed
2020-07-21 11:07:22.548  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : ChangeSet classpath:/db/changelog/hzcx_change.sql::ums_member_user_relation::hzc ran successfully in 26ms
2020-07-21 11:07:22.549  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : SELECT MAX(ORDEREXECUTED) FROM hzcx.DATABASECHANGELOG
2020-07-21 11:07:23.590  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO hzcx.DATABASECHANGELOG (ID, AUTHOR, FILENAME, DATEEXECUTED, ORDEREXECUTED, MD5SUM, `DESCRIPTION`, COMMENTS, EXECTYPE, CONTEXTS, LABELS, LIQUIBASE, DEPLOYMENT_ID) VALUES ('ums_member_user_relation', 'hzc', 'classpath:/db/changelog/hzcx_change.sql', NOW(), 1, '8:551f63d0a4cfada560aee75487a867b4', 'sql', '', 'EXECUTED', NULL, NULL, '3.8.0', '5300842515')
2020-07-21 11:07:23.596  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : DROP TABLE IF EXISTS `ums_integration_change_history`
2020-07-21 11:07:23.597  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : CREATE TABLE `ums_integration_change_history`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `user_id` int(11) NOT NULL COMMENT '会员ID',
  `change_type` char(1) DEFAULT '0' COMMENT '改变类型：0->增加；1->减少',
  `change_count` int(11) DEFAULT '0' COMMENT '积分改变的值',
  `change_value` int(11) DEFAULT '0' COMMENT '积分改变的值（正负计数）',
  `integral` int(11) NOT NULL DEFAULT '0' COMMENT '剩余积分',
  `source_type` varchar(100) DEFAULT NULL COMMENT  '积分赠送场景字典表：（donate_type_sign->签到；donate_type_shop->商城消费；donate_type_admin_obtain->管理员修改，手动增加；donate_type_admin_lose->管理员修改，手动减少）',
  `operate_man` varchar(100) DEFAULT NULL COMMENT '管理员修改，记录积分操作人',
  `operate_note` varchar(100) DEFAULT NULL COMMENT '操作备注',
  `create_time` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`) USING BTREE,
  KEY `integration_change_history_user_id` (`user_id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='积分变化历史记录表'
2020-07-21 11:07:23.616  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : Custom SQL executed
2020-07-21 11:07:23.617  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : ChangeSet classpath:/db/changelog/hzcx_change.sql::ums_integration_change_history::hzc ran successfully in 23ms
2020-07-21 11:07:23.618  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO hzcx.DATABASECHANGELOG (ID, AUTHOR, FILENAME, DATEEXECUTED, ORDEREXECUTED, MD5SUM, `DESCRIPTION`, COMMENTS, EXECTYPE, CONTEXTS, LABELS, LIQUIBASE, DEPLOYMENT_ID) VALUES ('ums_integration_change_history', 'hzc', 'classpath:/db/changelog/hzcx_change.sql', NOW(), 2, '8:8c5d60ebc3707410b833c9a617d60b05', 'sql', '', 'EXECUTED', NULL, NULL, '3.8.0', '5300842515')
2020-07-21 11:07:23.622  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : DROP TABLE IF EXISTS `ums_member_integration_statistic`
2020-07-21 11:07:23.623  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : CREATE TABLE `ums_member_integration_statistic`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `user_integration_count` int(11) DEFAULT '0' COMMENT '当前可用总积分',
  `obtain_integration_count` int(11) DEFAULT '0' COMMENT '累计获得总积分',
  `lose_integration_count` int(11) DEFAULT '0' COMMENT '累计消耗总积分',
  `add_date` date DEFAULT NULL COMMENT '添加日期',
  `create_time` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `add_date`(`add_date`) USING BTREE,
  INDEX `create_time`(`create_time`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='积分统计历史记录表'
2020-07-21 11:07:23.640  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : Custom SQL executed
2020-07-21 11:07:23.641  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : ChangeSet classpath:/db/changelog/hzcx_change.sql::ums_member_integration_statistic::hzc ran successfully in 21ms
2020-07-21 11:07:23.642  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO hzcx.DATABASECHANGELOG (ID, AUTHOR, FILENAME, DATEEXECUTED, ORDEREXECUTED, MD5SUM, `DESCRIPTION`, COMMENTS, EXECTYPE, CONTEXTS, LABELS, LIQUIBASE, DEPLOYMENT_ID) VALUES ('ums_member_integration_statistic', 'hzc', 'classpath:/db/changelog/hzcx_change.sql', NOW(), 3, '8:db5cb62c1bfbfd577432eeb39d53e3e9', 'sql', '', 'EXECUTED', NULL, NULL, '3.8.0', '5300842515')
2020-07-21 11:07:23.646  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : DROP TABLE IF EXISTS `ums_member_integral_task`
2020-07-21 11:07:23.647  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : CREATE TABLE `ums_member_integral_task`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `name` varchar(100) DEFAULT NULL COMMENT '任务名称',
  `task_type` char(1) DEFAULT '1' COMMENT '任务类型：0->新手任务；1->日常任务',
  `task_type_title` varchar(100) DEFAULT NULL COMMENT '任务类型说明：（例如 日常任务：每个任务，每个用户每天只能完成一次，第二天可以继续完成）',
  `donate_type` varchar(100) DEFAULT NULL COMMENT '任务编码（积分赠送场景字典表子集）',
  `illustrate` varchar(255) DEFAULT NULL COMMENT '任务内容',
  `sort` int(11) DEFAULT '1' COMMENT '排序值',
  `publish_status` char(1) DEFAULT '1' COMMENT '上架状态：0->下架；1->上架',
  `donate_integration` int(11) NOT NULL DEFAULT '0' COMMENT '赠送积分',
  `period_frequency` varchar(100) DEFAULT NULL COMMENT '周期频次',
  `del_flag` char(1) DEFAULT '0',
  `operate_man` varchar(100) DEFAULT NULL COMMENT '操作人',
  `create_time` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT = '积分任务管理'
2020-07-21 11:07:23.691  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : Custom SQL executed
2020-07-21 11:07:23.692  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : ChangeSet classpath:/db/changelog/hzcx_change.sql::ums_member_integral_task_1::hzc ran successfully in 48ms
2020-07-21 11:07:23.694  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO hzcx.DATABASECHANGELOG (ID, AUTHOR, FILENAME, DATEEXECUTED, ORDEREXECUTED, MD5SUM, `DESCRIPTION`, COMMENTS, EXECTYPE, CONTEXTS, LABELS, LIQUIBASE, DEPLOYMENT_ID) VALUES ('ums_member_integral_task_1', 'hzc', 'classpath:/db/changelog/hzcx_change.sql', NOW(), 4, '8:b8ca34ff365214871201b7fdb9ed552a', 'sql', '', 'EXECUTED', NULL, NULL, '3.8.0', '5300842515')
2020-07-21 11:07:23.696  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : BEGIN
2020-07-21 11:07:23.697  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO `hzcx`.`ums_member_integral_task`(`id`, `name`, `task_type`, `task_type_title`, `donate_type`, `illustrate`, `sort`, `publish_status`, `donate_integration`, `period_frequency`, `del_flag`, `operate_man`, `create_time`, `update_time`) VALUES (1, '每日签到', '1', '日常任务', 'donate_type_sign', NULL, 1, '1', 10, 'sign_once_a_day', '0', 'zhangjinkui', '2020-06-16 13:29:20', '2020-06-16 13:30:58')
2020-07-21 11:07:23.698  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : COMMIT
2020-07-21 11:07:23.699  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : Custom SQL executed
2020-07-21 11:07:23.700  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : ChangeSet classpath:/db/changelog/hzcx_change.sql::ums_member_integral_task_2::hzc ran successfully in 4ms
2020-07-21 11:07:23.701  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO hzcx.DATABASECHANGELOG (ID, AUTHOR, FILENAME, DATEEXECUTED, ORDEREXECUTED, MD5SUM, `DESCRIPTION`, COMMENTS, EXECTYPE, CONTEXTS, LABELS, LIQUIBASE, DEPLOYMENT_ID) VALUES ('ums_member_integral_task_2', 'hzc', 'classpath:/db/changelog/hzcx_change.sql', NOW(), 5, '8:0f420a0c1676efcc0e37c537c4b4bca4', 'sql', '', 'EXECUTED', NULL, NULL, '3.8.0', '5300842515')
2020-07-21 11:07:23.703  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : DROP TABLE IF EXISTS `ums_member_task_finish`
2020-07-21 11:07:23.704  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : CREATE TABLE `ums_member_task_finish`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `task_id` int(11) NOT NULL DEFAULT '0' COMMENT '任务ID',
  `user_id` int(11) NOT NULL DEFAULT '0' COMMENT '会员ID',
  `status` char(1) NOT NULL DEFAULT '1' COMMENT '是否有效：0->否，1->是',
  `add_time` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '添加时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT = '用户任务完成记录表'
2020-07-21 11:07:23.719  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : Custom SQL executed
2020-07-21 11:07:23.720  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : ChangeSet classpath:/db/changelog/hzcx_change.sql::ums_member_task_finish::hzc ran successfully in 18ms
2020-07-21 11:07:23.722  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO hzcx.DATABASECHANGELOG (ID, AUTHOR, FILENAME, DATEEXECUTED, ORDEREXECUTED, MD5SUM, `DESCRIPTION`, COMMENTS, EXECTYPE, CONTEXTS, LABELS, LIQUIBASE, DEPLOYMENT_ID) VALUES ('ums_member_task_finish', 'hzc', 'classpath:/db/changelog/hzcx_change.sql', NOW(), 6, '8:ca0772b881529e6cb7e2a6ba3dd6f641', 'sql', '', 'EXECUTED', NULL, NULL, '3.8.0', '5300842515')
2020-07-21 11:07:23.725  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : DROP TABLE IF EXISTS `ums_member_sign`
2020-07-21 11:07:23.726  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : CREATE TABLE `ums_member_sign`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `user_id` int(11) NOT NULL DEFAULT '0' COMMENT '会员ID',
  `title` varchar(255) DEFAULT NULL COMMENT '签到说明',
  `number` int(11) NOT NULL DEFAULT '0' COMMENT '获得积分',
  `add_time` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '添加时间',
  PRIMARY KEY (`id`) USING BTREE,
  KEY `sign_user_id` (`user_id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT = '签到记录表'
2020-07-21 11:07:23.745  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : Custom SQL executed
2020-07-21 11:07:23.746  INFO 11157 --- [           main] liquibase.changelog.ChangeSet            : ChangeSet classpath:/db/changelog/hzcx_change.sql::ums_member_sign::hzc ran successfully in 22ms
2020-07-21 11:07:23.748  INFO 11157 --- [           main] liquibase.executor.jvm.JdbcExecutor      : INSERT INTO hzcx.DATABASECHANGELOG (ID, AUTHOR, FILENAME, DATEEXECUTED, ORDEREXECUTED, MD5SUM, `DESCRIPTION`, COMMENTS, EXECTYPE, CONTEXTS, LABELS, LIQUIBASE, DEPLOYMENT_ID) VALUES ('ums_member_sign', 'hzc', 'classpath:/db/changelog/hzcx_change.sql', NOW(), 7, '8:3ac00b4561f1b94eb46aed505f69e47a', 'sql', '', 'EXECUTED', NULL, NULL, '3.8.0', '5300842515')
2020-07-21 11:07:23.751  INFO 11157 --- [           main] l.lockservice.StandardLockService        : Successfully released change log lock
2020-07-21 11:07:23.856  INFO 11157 --- [           main] .s.s.UserDetailsServiceAutoConfiguration : 

```

#### spring boot简单集成liquibase

##### 添加依赖

因为 Spring Boot 已经内置支持整合 Liquibase，我们只需要在项目工程中引入 Liquibase 的依赖进行配置即可。

```
		<dependency>
			<groupId>org.liquibase</groupId>
			<artifactId>liquibase-core</artifactId>
			<version>3.8.0</version>
		</dependency>
```


![image-20200721111319069](/Users/jinkui/Library/Application Support/typora-user-images/image-20200721111319069.png)

##### 添加配置

```
spring:
  liquibase:
    enabled: true
    drop-first: false
    change-log: classpath:/db/changelog/hzcx_change.sql
```

![image-20200721112403488](/Users/jinkui/Library/Application Support/typora-user-images/image-20200721112403488.png)

#### changelog 编写讲解

##### SQL 格式的 changelogs 文件

SQL 格式的 `changelog` 文件使用 SQL 注释作为元数据，为确保这个文件被 Liquibase 识别为 `changelog`文件，，SQL 文件必须以以下注释开头：

```
--liquibase formatted sql
```



##### changeset 变更



SQL 格式的 `changelog` 文件中在变更的 SQL 前需要加上以下注释，表示为一个 `changeset`变更集：

```
--changeset hzc:ums_member_user_relation
```

变更集 `changeset` 是通过 `author + id` 的方式来保证唯一性

![image-20200721114342806](/Users/jinkui/Library/Application Support/typora-user-images/image-20200721114342806.png)

注意：如果使用工程已配置的 `datasource` 数据源，则 `liquibase.url、liquibase.user` 和 `liquibase.password`这个三个参数可不配置，目标数据源即为工程已配置好的数据源。因为 Spring 会自动为 liquibase 获取可用的数据源，详情可查看这个类：`org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration`。



#### 观察变化



![image-20200721113032620](/Users/jinkui/Library/Application Support/typora-user-images/image-20200721113032620.png)


从数据库中可以看到变化



![image-20200721113110909](/Users/jinkui/Library/Application Support/typora-user-images/image-20200721113110909.png)



其中`DATABASECHANGELOG` 和 `DATABASECHANGELOGLOCK` 表是 Liquibase 自动生成的。



#### Liquibase 最佳实践

##### 一个变更集只设置一次更改

尽可能地避免对一个变更集进行多次更改，以避免自动提交 SQL 语句而可能使数据库处于非预期状态。 如 `--changeset zhangjk:ums_member_user_relation` 变更集，只新建一张 `ums_member_user_relation` 表，后面不再修改该变更集，如果需要变更，可以新增一条变更集。

#####  变更集的 ID

选择适合您的方法。有的人是使用从 1 开始的序列号, 并且在更改日志中是唯一的，也有些人选择一个描述性的名称（例如：`ums_member_user_relation`）