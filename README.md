# Gorose ORM

[![GoDoc](https://godoc.org/github.com/gohouse/gorose?status.svg)](https://godoc.org/github.com/gohouse/gorose)
[![Go Report Card](https://goreportcard.com/badge/github.com/gohouse/gorose)](https://goreportcard.com/report/github.com/gohouse/gorose)
[![Gitter](https://badges.gitter.im/gohouse/gorose.svg)](https://gitter.im/gorose/wechat)
<a target="_blank" href="https://jq.qq.com/?_wv=1027&k=5JJOG9E">
<img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="gorose-orm" title="gorose-orm"></a>

### What is Gorose?

Gorose, a mini database ORM for golang, which inspired by the famous php framwork laravel's eloquent. It will be friendly for php developers and python or ruby developers.  
Currently provides five major database drivers:   
- **mysql** : <https://github.com/go-sql-driver/mysql>  
- **sqlite3** : <https://github.com/mattn/go-sqlite3> (待完善)  
- **postgres** : <https://github.com/lib/pq> (待完善)  
- **oracle** : <https://github.com/mattn/go-oci8> (待完善)  
- **mssql** : <https://github.com/denisenkom/go-mssqldb> (待完善)  

### Quick Preview(概览)

```go
type users struct {
	Name string
	Age int `orm:"age"`
}

// select * from users where id=1 limit 1
var user users      // 单条数据
var users []users   // 多条数据
// struct模式
db.Table(&user).Select()
db.Table(&users).Where("id",1).Limit(10).Select()
// 也可以使用非struct兼容模式
db.Table("users").Where("id",1).First()

// select id as uid,name,age from users where id=1 order by id desc limit 10
db.Table(&user).Where("id",1).Fields("id as uid,name,age").Order("id desc").Limit(10).Get()

// query string
db.Query("select * from user limit 10")
db.Execute("update users set name='fizzday' where id=?", 1)
```

### Features(特点)

- Chain Operation (链式操作)  
- Connection Pool (连接池)  
- struct/string compatible (兼容支持struct和string)  
- read/write separation cluster (读写分离集群)  
- 大量数据分片处理  
- 一键事务  
- 扩展开发更友好(非侵入式 自由添加配置解析器, 驱动解析器)  

### Installation

- standard:  
```go
go get -u github.com/gohouse/gorose
```

### Base Usage
```go
package main

import (
	"github.com/gohouse/gorose"
	_ "github.com/gohouse/gorose/driver/mysql"
	"fmt"
)

type Users struct {
	Name string
	Age int `orm:"age"`
}
// DB Config.(Recommend to use configuration file to import)
var dbConfig1 = &gorose.DbConfigSingle {
	Driver:          "mysql",   // 驱动: mysql/sqlite/oracle/mssql/postgres
	EnableQueryLog:  true,      // 是否开启sql日志
	SetMaxOpenConns: 0,         // (连接池)最大打开的连接数，默认值为0表示不限制
	SetMaxIdleConns: 0,         // (连接池)闲置的连接数, 默认-1
	Prefix:          "",        // 表前缀
	Dsn:             "root:root@tcp(localhost:3306)/test?charset=utf8", // 数据库链接
}

func main() {
	connection, err := gorose.Open(dbConfig1)
	if err != nil {
		fmt.Println(err)
		return
	}

	db := connection.NewSession()
	// 查询一条数据
    var user Users
	err2 := db.Table(&user).Select()
	if err2 != nil {
		fmt.Println(err2)
		return
	}
	fmt.Println(db.LastSql)
	fmt.Println(user)
	// 查询多条数据
    var users []Users
	err3 := db.Table(&users).Limit(3).Select()
	if err3 != nil {
		fmt.Println(err3)
		return
	}
	fmt.Println(db.LastSql)
	fmt.Println(users)
}

```
For more usage, please read the Documentation.

### Contribution

- [Issues](https://github.com/gohouse/gorose/issues)
- [Pull requests](https://github.com/gohouse/gorose/pulls)

### Contributors

- `fizzday` : Initiator(发起人)  
- `wuyumin` : pursuing the open source standard(推行开源标准规划)  
- `holdno`  : official website builder(官方网站搭建)  
- `LazyNeo` : bug fix and improve source code(bug修复和改进源码)  
- `dmhome`  : improve source code(改进源码) 

### 更新说明
- 目录调整: 更加符合协作开发的目录  
- 架构调整: 开放式架构, 驱动, 解析器, 构造器, 全部分离, 自由定义  
- 支持struct  
- 读写分离集群支持  
 
### release notes

> 1.0.0

- 全新开发式自由架构,自由扩展驱动,配置文件, struct支持, 读写分离集群支持

> 0.9.2  

- new connection pack for supporting multi connection

> 0.9.1  

- replace the insert result lastInsertId with rowsAffected as default

> 0.9.0  

- new seperate db instance

> 0.8.2  

- improve config format, new config format support file config like json/toml etc.

> 0.8.1

- improve multi connection and nulti transation

> 0.8.0  

- add connection pool  
- adjust dir for open source standard  
- add glide version control  
- translate for english and chinese docment  

### License

MIT
