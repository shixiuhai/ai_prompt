# 示例
## 建表语句（MySQL 5.7 语法）
CREATE TABLE `tbl_user_info` (
  `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `username` VARCHAR(50) NOT NULL COMMENT '用户名',
  `password` VARCHAR(100) NOT NULL COMMENT '密码',
  `phone` VARCHAR(20) DEFAULT NULL COMMENT '手机号',
  `status` TINYINT(1) DEFAULT 1 COMMENT '状态（1正常，0禁用）',
  `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户信息表';

## 模块1：实体类（Entity）
请根据建表语句生成 Java 实体类，使用 Lombok 注解（如 @Data @TableName）并保留字段注释。

## 模块2：DAO接口
生成 MyBatis-Plus 的 Mapper 接口，继承 BaseMapper<Entity>。

## 模块3：Service 接口
生成对应的 Service 接口，定义基础的增删改查方法。

## 模块4：Service 实现类（Impl）
实现 Service 接口，继承 ServiceImpl<Mapper, Entity>，包含基础方法实现。

## 模块5：Controller 控制器
生成 RESTful 风格的 Controller 类，包含基本的 CRUD 接口（使用 @RestController、@RequestMapping、@GetMapping 等注解）。

# 最终任务
请根据以上示例结构，结合我提供的建表语句，为我生成 Java 后端的分层代码（Entity、DAO、Service、ServiceImpl、Controller），模块之间以 ## 模块名 分隔。
