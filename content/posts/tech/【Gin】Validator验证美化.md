---
title: "【Gin】Validator验证美化"
date: 2023-03-11T23:04:08+08:00
draft: false
categories: ["后端"]
tags: ["Gin"]
card: false
weight: 0
---

> `Gin`默认自带的结构体字段校验会在校验失败时返回错误信息，但是这些信息对客户端用户并不友好，例如下面的必填校验

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
)

type User struct {
	Name     string `form:"name" binding:"required"`
	Password string `form:"password" binding:"required"`
}

func LoginHandler(c *gin.Context) {
	var user User
	if err := c.ShouldBind(&user); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{
			"message": err.Error(),
		})
	} else {
		c.JSON(http.StatusOK, gin.H{
			"message": "成功",
		})
	}
}
func main() {
	fmt.Println("美化gin的validator验证器")
	//_ = ValidatorInit("zh")
	g := gin.Default()
	g.POST("/login", LoginHandler)
	_ = g.Run(":3600")
}

```

> 当字段校验失败时，请求体内容为`error.Error()`

```go
{
    "message": "Key: 'User.Name' Error:Field validation for 'Name' failed on the 'required' tag\nKey: 'User.Password' Error:Field validation for 'Password' failed on the 'required' tag"
}
```

> 这种校验信息对客户端用户来说是无意义的，所以需要将这个信息翻译成适应客户端用户的语种，`validator`提供能语种翻译的能力，只需要按照场景来设置默认翻译语种即可，下面的代码中`Init`方法是根据传入的地区去注册翻译器，`Translate`是将验证产生的错误使用翻译器将错误放入结果切片中（错误可能不止一个）

```go
package main

import (
	"errors"
	"fmt"
	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/binding"
	"github.com/go-playground/locales/en"
	"github.com/go-playground/locales/zh"
	ut "github.com/go-playground/universal-translator"
	"github.com/go-playground/validator/v10"
	en_translations "github.com/go-playground/validator/v10/translations/en"
	zh_translations "github.com/go-playground/validator/v10/translations/zh"
)

var (
	Trans ut.Translator
)

func ValidatorInit(locale string) error {
	if validate, ok := binding.Validator.Engine().(*validator.Validate); ok {
		//国际化语言
		zhT := zh.New()
		enT := en.New()
		uni := ut.New(enT, zhT)
		var found bool
		Trans, found = uni.GetTranslator(locale)
		if !found {
			fmt.Println("未找到对应语种")
		}
		//注册翻译
		switch locale {
		case "zh":
			_ = zh_translations.RegisterDefaultTranslations(validate, Trans)
		case "en":
			_ = en_translations.RegisterDefaultTranslations(validate, Trans)
		default:
			_ = zh_translations.RegisterDefaultTranslations(validate, Trans)
		}
		return nil
	} else {
		return errors.New("类型断言失败")
	}
}
func Translate(err error) gin.H {
	var result = gin.H{}
	validationErrors := err.(validator.ValidationErrors)
	for _, v := range validationErrors {
		result[v.Field()] = v.Translate(Trans)
	}
	return result
}

```

> 在`main`方法中调用`Init`方法，为`Validator`注册对应的翻译器

```go
func main() {
	fmt.Println("美化gin的validator验证器")
	_ = ValidatorInit("zh")
	g := gin.Default()
	g.POST("/login", LoginHandler)
	_ = g.Run(":3600")
}
```

> 在校验失败时翻译对应的错误到响应之中

```go
func LoginHandler(c *gin.Context) {
	var user User
	if err := c.ShouldBind(&user); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{
			"message": Translate(err),
		})
	} else {
		c.JSON(http.StatusOK, gin.H{
			"message": "成功",
		})
	}
}
```

> 响应中的错误信息已经被自动翻译

```go
{
    "message": {
        "Name": "Name为必填字段",
        "Password": "Password为必填字段"
    }
}
```

代码：[https://github.com/supuwoerc/go-tips/tree/main/validators](https://github.com/supuwoerc/go-tips/tree/main/validators)

