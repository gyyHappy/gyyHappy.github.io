---
layout:     post
title:      yy-admin 开发文档
subtitle:   yy-admin 开发文档
date:       2020-7-20
author:     阿塬
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - Java
	- SpringBoot
	- Redis
---

# yy-admin 开发文档

## 第1章 - 项目介绍

yy-admin是一款轻量级的权限系统，主要包括用户管理，角色管理，菜单管理，操作日志，SQL监控，参数管理等功能。基于SpringBoot开发，使用JWT进行交互，使用Redis缓存用户信息，Swagger进行接口文档管理，全局异常统一处理等特点。

### 1.1、数据交互

![](https://github.com/gyyHappy/gyyHappy.github.io/blob/master/img/3.png?raw=true)

### 1.2、开发环境

#### 1.2.1 、软件需求

- JDK1.8+
- Maven3.0+
- MySQL8+

#### 1.2.2、下载源码

```
git clone https://github.com/gyyHappy/yy-admin.git
```

#### 1.2.3、开发工具

- IDEA： 左上角 File - new - Project from Version Control.

- 输入上面的地址：git clone https://github.com/gyyHappy/yy-admin.git

#### 1.2.3、创建数据库

- 创建一个yy-admin数据库，在项目文件中的sql文件夹中找到yy-admin文件导入数据库中

#### 1.2.4、修改配置文件

- 修改 application.yml 文件中数据库的账号和密码
- 运行 com.gyy.YyAdminAplication.java的main方法即可启动项目
- Swagger路径：http://localhost:8080/swagger/index.html
- Swagger注解路径：http://localhost:8080/swagger-ui.html

#### 1.2.5、前端部署

```
# 克隆项目 git clone https://github.com/gyyHappy/yy-admin-vue.git

# 安装依赖 npm install --registry=https://registry.npm.taobao.org

# 启动服务 npm run dev
```

- 登录的账号密码：admin/123456

### 1.3、项目源码

|        |               后端源码               |                 前端源码                 |
| ------ | :----------------------------------: | :--------------------------------------: |
| github | https://github.com/gyyHappy/yy-admin | https://github.com/gyyHappy/yy-admin-vue |

## 第2章  - MySQL

1、修改数据库的配置，在application.yml文件中

```yml
datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      url: jdbc:mysql://localhost:3306/yy-admin?serverTimezone=Asia/Shanghai
      username: root
      password: 123456
      driver-class-name: com.mysql.cj.jdbc.Driver
```

如果MySQL5，则使用 `com.mysql.jdbc.Driver`连接

2、执行sql/yy-admin.sql文件，创建表及初始化数据，在启动项目即可

## 第3章 Mybatis-Plus 使用

1、MP（Mybatis-Plus的简称，后面都用这个代替），代码生成器，能帮你生成从Controller到Mapper.xml，减少不必要的编码，在com.gyy.CodeGenerator.java就是代码生成器，代码如下：

```java
package com.gyy;

import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;
import com.baomidou.mybatisplus.core.toolkit.StringPool;
import com.baomidou.mybatisplus.core.toolkit.StringUtils;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.*;
import com.baomidou.mybatisplus.generator.config.po.TableInfo;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

// 演示例子，执行 main 方法控制台输入模块表名回车自动生成对应项目目录中
public class CodeGenerator {

    /**
     * <p>
     * 读取控制台内容
     * </p>
     */
    public static String scanner(String tip) {
        Scanner scanner = new Scanner(System.in);
        StringBuilder help = new StringBuilder();
        help.append("请输入" + tip + "：");
        System.out.println(help.toString());
        if (scanner.hasNext()) {
            String ipt = scanner.next();
            if (StringUtils.isNotEmpty(ipt)) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "！");
    }

    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath + "/src/main/java");
        gc.setAuthor("GYY");
        gc.setOpen(false);
        gc.setControllerName("Sys%sController");
        gc.setMapperName("Sys%sMapper");
        gc.setXmlName("Sys%sMapper");
        gc.setServiceImplName("Sys%sServiceImpl");
        gc.setServiceName("Sys%sService");
        gc.setEntityName("Sys%sEntity");
        gc.setSwagger2(true); //实体属性 Swagger2 注解
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/yy-admin?useUnicode=true&useSSL=false&characterEncoding=utf8&serverTimezone=Asia/Shanghai");
        // dsc.setSchemaName("public");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("123456");
        mpg.setDataSource(dsc);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName(scanner("模块名"));
        pc.setParent("com.gyy.modules");
        mpg.setPackageInfo(pc);

        // 自定义配置
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                // to do nothing
            }
        };

        // 如果模板引擎是 freemarker
        String templatePath = "/templates/mapper.xml.ftl";
        // 如果模板引擎是 velocity
        // String templatePath = "/templates/mapper.xml.vm";

        // 自定义输出配置
        List<FileOutConfig> focList = new ArrayList<>();
        // 自定义配置会被优先输出
        focList.add(new FileOutConfig(templatePath) {
            @Override
            public String outputFile(TableInfo tableInfo) {
                // 自定义输出文件名 ， 如果你 Entity 设置了前后缀、此处注意 xml 的名称会跟着发生变化！！
                return projectPath + "/src/main/resources/mapper/" + pc.getModuleName()
                        + "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
            }
        });
        /*
        cfg.setFileCreate(new IFileCreate() {
            @Override
            public boolean isCreate(ConfigBuilder configBuilder, FileType fileType, String filePath) {
                // 判断自定义文件夹是否需要创建
                checkDir("调用默认方法创建的目录，自定义目录用");
                if (fileType == FileType.MAPPER) {
                    // 已经生成 mapper 文件判断存在，不想重新生成返回 false
                    return !new File(filePath).exists();
                }
                // 允许生成模板文件
                return true;
            }
        });
        */
        cfg.setFileOutConfigList(focList);
        mpg.setCfg(cfg);

        // 配置模板
        TemplateConfig templateConfig = new TemplateConfig();

        // 配置自定义输出模板
        //指定自定义模板路径，注意不要带上.ftl/.vm, 会根据使用的模板引擎自动识别
        // templateConfig.setEntity("templates/entity2.java");
        // templateConfig.setService();
        // templateConfig.setController();

        templateConfig.setXml(null);
        mpg.setTemplate(templateConfig);

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        //strategy.setSuperEntityClass("你自己的父类实体,没有就不用设置!");
        strategy.setEntityLombokModel(true);
        strategy.setRestControllerStyle(true);
        // 公共父类
        //strategy.setSuperControllerClass("你自己的父类控制器,没有就不用设置!");
        // 写于父类中的公共字段
        strategy.setSuperEntityColumns("id");
        strategy.setInclude(scanner("表名，多个英文逗号分割").split(","));
        strategy.setControllerMappingHyphenStyle(true);
        strategy.setTablePrefix(pc.getModuleName() + "_");
        mpg.setStrategy(strategy);
        mpg.setTemplateEngine(new FreemarkerTemplateEngine());
        mpg.execute();
    }

}
```

其中如下代码，就是设置文件生成的名字

```java
gc.setControllerName("Sys%sController");
```

还可以设置文件生成的路径，如

```java
pc.setParent("com.gyy.modules");
```

点击main方法运行，输入模块名称，如输入sys，则文件会生成在com.gyy.modules.sys文件夹下，接着输入表名，多张表的话则中间用英文`,`隔开。

## 第4章 - 后端源码分析

