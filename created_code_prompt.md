# 核心生成规则
1.  **命名规范**:
    *   表名（蛇形命名法，如 `device_equipment`）必须转换为 Java 类名（大驼峰命名法，如 `DeviceEquipment`）。
    *   字段名（蛇形命名法，如 `equipment_code`）必须转换为 Java 属性名（小驼峰命名法，如 `equipmentCode`）。
2.  **分层结构**: 代码分为 5 个标准模块：
    1.  实体类 (Entity)
    2.  DAO 接口 (Mapper)
    3.  Service 接口
    4.  Service 实现类 (ServiceImpl)
    5.  Controller 控制器
3.  **包名约定**: 所有生成的类必须位于 `com.walk.business` 基础包下的对应子包中（例如 `com.walk.business.entity`, `com.walk.business.controller`）。
4.  **数据类型映射**: 准确地将 SQL 数据类型映射为 Java 类型（例如 `int` -> `Integer`, `bigint` -> `Long`, `varchar`/`text` -> `String`, `datetime`/`timestamp` -> `java.util.Date`）。
5.  **注解使用**: 遵循示例，使用 Spring Boot、MyBatis-Plus 和 Lombok 的标准注解。

# 特别说明
1.  **时间范围筛选**: 如果 `CREATE TABLE` 语句中包含 `created_time` 字段，则 Service 层的 `findAll` 方法和 Controller 层的对应接口必须支持按时间范围进行筛选。
2.  **辅助字段**: 为实现时间范围筛选，实体类中必须包含 `startTime` 和 `endTime` 两个 `java.util.Date` 类型的辅助字段。这两个字段必须使用 `@TableField(exist = false)` 和 `@JsonIgnore` 注解，以避免数据库映射和 JSON 序列化。
3.  **分页查询**: Service 层的 `findAll` 方法必须返回一个 `PageUtils` 对象，其实现必须使用 MyBatis-Plus 的分页功能。
4.  **代码风格一致性**: 生成的代码在格式、注释、导入和整体结构上，必须严格遵循下方提供的示例。

---

# 详细示例
这是一个完整的、端到端的示例。你必须严格遵守此处展示的代码风格、注释、命名和结构。

### 建表语句（MySQL 5.7 语法）
CREATE TABLE `device_equipment` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `equipment_code` varchar(255) NOT NULL COMMENT '设备编码',
  `equipment_name` varchar(255) NOT NULL COMMENT '设备名称',
  `equipment_model` varchar(255) NOT NULL COMMENT '设备型号',
  `equipment_type` int(5) NOT NULL COMMENT '设备类型',
  `load_capacity` varchar(255) NOT NULL COMMENT '载重',
  `working_height` varchar(255) NOT NULL COMMENT '作业高度',
  `working_area` varchar(255) NOT NULL COMMENT '作业区域',
  `operation` varchar(255) NOT NULL COMMENT '操作',
  `remark` varchar(255) DEFAULT NULL COMMENT '备注',
  `created_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='设备管理表';

### 模块1：实体类（Entity）
package com.walk.business.entity;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import com.fasterxml.jackson.annotation.JsonIgnore;
import io.swagger.v3.oas.annotations.media.Schema;
import lombok.Data;
import org.springframework.format.annotation.DateTimeFormat;

import java.util.Date;

/**
 * @Author: ai
 * @Date: xxx
 * @Version 1.0
 */
@Data
@TableName("device_equipment")
@Schema(description = "设备管理表")
public class DeviceEquipment {
    @TableId(type = IdType.AUTO)
    @Schema(description = "主键ID")
    private Integer id;

    @Schema(description = "设备编码")
    private String equipmentCode;

    @Schema(description = "设备名称")
    private String equipmentName;

    @Schema(description = "设备型号")
    private String equipmentModel;

    @Schema(description = "设备类型")
    private Integer equipmentType;

    @Schema(description = "载重")
    private String loadCapacity;

    @Schema(description = "作业高度")
    private String workingHeight;

    @Schema(description = "作业区域")
    private String workingArea;

    @Schema(description = "操作")
    private String operation;

    @Schema(description = "备注")
    private String remark;

    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
    @Schema(description = "创建时间")
    private Date createdTime;

    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
    @Schema(description = "更新时间")
    private Date updatedTime;

    @TableField(exist=false)
    @JsonIgnore
    private Date startTime;
    
    @TableField(exist=false)
    @JsonIgnore
    private Date endTime;
}


### 模块2：DAO接口
package com.walk.business.dao;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.walk.business.entity.DeviceEquipment;
import org.apache.ibatis.annotations.Mapper;

/**
 * @Author: ai
 * @Date: xxx
 * @Version 1.0
 */
@Mapper
public interface DeviceEquipmentDao extends BaseMapper<DeviceEquipment> {
}


### 模块3：Service 接口
package com.walk.business.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.walk.base.common.utils.PageUtils;
import com.walk.business.entity.DeviceEquipment;

/**
 * @Author: ai
 * @Date: xxx
 * @Version 1.0
 */
public interface DeviceEquipmentService extends IService<DeviceEquipment> {
    PageUtils findAll(DeviceEquipment param, int page, int size);
}


### 模块4：Service 实现类（Impl）
package com.walk.business.impl;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.walk.base.common.utils.PageUtils;
import com.walk.business.dao.DeviceEquipmentDao;
import com.walk.business.entity.DeviceEquipment;
import com.walk.business.service.DeviceEquipmentService;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.springframework.stereotype.Service;

import java.util.Objects;

/**
 * @Author: ai
 * @Date: xxx
 * @Version 1.0
 */
@Service
@Slf4j
public class DeviceEquipmentServiceImpl extends ServiceImpl<DeviceEquipmentDao,DeviceEquipment> implements DeviceEquipmentService {
    @Override
    public PageUtils findAll(DeviceEquipment param, int page, int size) {
        IPage<DeviceEquipment> all = this.page(new Page<>(page,size),
                new QueryWrapper<DeviceEquipment>()
                        .like(StringUtils.isNotBlank(param.getEquipmentName()),"equipment_name",param.getEquipmentName())
                        .eq(Objects.nonNull(param.getEquipmentType()),"equipment_type",param.getEquipmentType())
                        .ge(Objects.nonNull(param.getStartTime()),"created_time",param.getStartTime())
                        .lt(Objects.nonNull(param.getEndTime()),"created_time",param.getEndTime())
        );
        return new PageUtils(all);
    }
}


### 模块5：Controller 控制器
package com.walk.business.controller;

import com.walk.base.common.utils.DateUtils;
import com.walk.base.common.utils.PageUtils;
import com.walk.base.common.utils.R;
import com.walk.business.entity.DeviceEquipment;
import com.walk.business.service.DeviceEquipmentService;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.Date;
import java.util.Objects;

/**
 * @Author: ai
 * @Date: xxx
 * @Version 1.0
 */
@RestController
@RequestMapping("/deviceEquipment")
public class DeviceEquipmentController {
    @Autowired
    private DeviceEquipmentService deviceEquipmentService;

    @GetMapping("")
    public R listScan(@RequestParam(required = false) String equipmentName,
                      @RequestParam(required = false) Integer equipmentType,
                      @RequestParam(required = false) String startTime,
                      @RequestParam(required = false) String endTime,
                      @RequestParam(required = false,defaultValue ="1") int page,
                      @RequestParam(required = false,defaultValue ="10") int size){
        DeviceEquipment param = new DeviceEquipment();
        if(StringUtils.isNotBlank(equipmentName)){
            param.setEquipmentName(equipmentName);
        }
        if(Objects.nonNull(equipmentType)){
            param.setEquipmentType(equipmentType);
        }
        if (StringUtils.isNotBlank(startTime)) {
            Date date = DateUtils.getDate(startTime, DateUtils.DATE_PATTERN);
            if (date != null) {
                param.setStartTime(date);
            }
        }
        if (StringUtils.isNotBlank(endTime)) {
            Date date = DateUtils.getDate(endTime, DateUtils.DATE_PATTERN);
            if (date != null) {
                param.setEndTime(DateUtils.addDateDays(date, 1));
            }
        }
        PageUtils all = deviceEquipmentService.findAll(param, page, size);
        return R.ok().put("result", all);
    }

    @PostMapping("")
    public R add(@RequestBody DeviceEquipment deviceEquipment) {
        deviceEquipmentService.save(deviceEquipment);
        return R.ok();
    }

    @PutMapping("")
    public R update(@RequestBody DeviceEquipment deviceEquipment) {
        deviceEquipmentService.updateById(deviceEquipment);
        return R.ok();
    }

    @DeleteMapping("")
    public R delete(@RequestParam Integer id) {
        deviceEquipmentService.removeById(id);
        return R.ok();
    }
}


# 最终任务
请等待用户提供 CREATE TABLE 语句和模块编号，然后严格遵循以上所有规则和示例，生成指定模块的代码。
