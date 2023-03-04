---
title: "【Gorm】创建连接"
date: 2023-03-04T18:55:11+08:00
draft: false
categories: ["后端"]
tags: ["数据库","gorm"]
card: false
weight: 0
---
### 源码
```golang
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"gorm.io/gorm/schema"
	"time"
)

var DB *gorm.DB

func init() {
	username := "root"     //账号
	password := "12345678" //密码
	host := "127.0.0.1"    //地址
	port := 3306           //端口
	dbname := "gorm_study" //数据库名
	//可能会遇到tcp连接的问题：https://github.com/Masterminds/glide/issues/999
	connectString := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?charset=utf8&parseTime=true&loc=Local", username, password, host, port, dbname)
	db, err := gorm.Open(mysql.New(mysql.Config{
		DSN: connectString,
	}), &gorm.Config{
		SkipDefaultTransaction: false, //跳过默认事务
		NamingStrategy: schema.NamingStrategy{
			TablePrefix:   "gva_", //表名前缀
			SingularTable: true,   //单数表名
		},
		DisableForeignKeyConstraintWhenMigrating: true, //不建立物理外键
	})
	if err != nil {
		panic("数据库连接失败：" + err.Error())
	} else {
		fmt.Println("数据库连接成功")
		sqlDb, _ := db.DB()
		sqlDb.SetMaxIdleConns(10)               //设置连接池最大空闲连接数
		sqlDb.SetMaxOpenConns(20)               //设置连接池最大连接数
		sqlDb.SetConnMaxLifetime(1 * time.Hour) //设置连接最大生存周期
		DB = db
	}
}
func main() {
	fmt.Println("main is running...")
	//model.go自动迁移数据库表
	//DB.AutoMigrate(&User{})
}
```
### 仓库文件
[Grom创建连接](https://github.com/supuwoerc/gorm_study/blob/main/%E8%BF%9E%E6%8E%A5.go)

