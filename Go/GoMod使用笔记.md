# Go Mod 初体验

**go modules** 是golang 1.11新加的特性，现在go版本都迭代到1.14了，感觉快要把go path给淘汰掉了，是时候使用起mod了。



> Go modules是go team在解决包依赖管理方面的一次勇敢尝试，无论如何，对Go语言来说都是一个好事。



### 使用Modules的条件是什么？

---

1. golang版本大于等于1.11
2. 设置**GO111MODULE**



#### GO111MODULE

**GO111MODULE**有三个值：

+ off : go项目将不会支持module功能，项目依赖包寻找依然按照最初的go path模式来查找。
+ on :go项目只支持module功能，项目依赖不会按照go path模式来查找。
+ auto :默认值，go命令行会智能区分依赖包查询模式：
  1. 当前项目中含有go.mod文件会开启module模式
  2. 当前项目在gopath/src下且项目中不含有go.mod文件会使用gopath模式

查看当前GO11MODULE是否开启：

```echo GO111MODULE```

更改GO111MODULE设置：

```linux|macos系统方法 ：export GO111MODULE=on```

```windows系统方法：set GO111MODULE=on```

> 当modules功能启用时，依赖包的存放位置就会变更为$GOPATH/pkg，允许同一个package多个版本并存，且多个项目可以共享缓存的module。



#### go mod

golang提供了**go mod**命令来管理包

gomod命令：

|    命令    | 说明                 |
| :------: | :----------------- |
| download | 下载依赖包              |
|   edit   | 这个可以直接编辑edit       |
|  graph   | 打印模块所有依赖           |
|   init   | 在当前目录初始化mod        |
|   tidy   | 拉取缺少的模块            |
|  vendor  | 将依赖复制到项目中vendor目录里 |
|  verify  | 验证依赖是否正确           |
|   why    | 解释为什么需要依赖          |

这里的命令在下文实例中我都会一一展示用法



# 项目实例使用

### 创建一个新项目

1. 在gopath目录外边新建一个项目，并在目录中使用go mod init初始化生成go.mod文件

   ![新建项目](http://static.cocofan.cn/github/05_16/gomod_new.jpg "新建项目")

2. 这个时候go.mod文件中内容是

   ```Go
   module github.com/xiaka53/demo

   go 1.13
   ```

3. 新建一个demo.go，写入一下代码：

   ```go
   package main

   import (
   	"net/http"
   	"github.com/labstack/echo"
   )

   func main() {
     e := echo.New()
     e.GET("/", func(c echo.Context) error {
       return c.String(http.StatusOK, "Hello,World!")
     })
     e.Logger.Fatal(e.Start(":8080"))
   }
   ```

   执行go run demo.go

   ```shell
   go: finding github.com/labstack/echo v3.3.10+incompatible
   go: downloading github.com/labstack/echo v3.3.10+incompatible
   go: extracting github.com/labstack/echo v3.3.10+incompatible
   package main
   go: finding github.com/labstack/gommon v0.3.0
   go: finding golang.org/x/crypto latest
   go: downloading github.com/labstack/gommon v0.3.0
   go: downloading golang.org/x/crypto v0.0.0-20200429183012-4b2356b1ed79
   go: extracting github.com/labstack/gommon v0.3.0
   go: extracting golang.org/x/crypto v0.0.0-20200429183012-4b2356b1ed79
   go: downloading golang.org/x/net v0.0.0-20190404232315-eb5bcb51f2a3
   go: downloading github.com/mattn/go-colorable v0.1.2
   go: downloading github.com/mattn/go-isatty v0.0.9
   go: downloading github.com/valyala/fasttemplate v1.0.1
   go: extracting github.com/mattn/go-colorable v0.1.2
   go: extracting github.com/mattn/go-isatty v0.0.9
   go: extracting github.com/valyala/fasttemplate v1.0.1
   go: downloading github.com/valyala/bytebufferpool v1.0.0
   go: extracting github.com/valyala/bytebufferpool v1.0.0
   go: extracting golang.org/x/net v0.0.0-20190404232315-eb5bcb51f2a3
   go: downloading golang.org/x/text v0.3.0
   go: extracting golang.org/x/text v0.3.0
   go: finding github.com/mattn/go-colorable v0.1.2
   go: finding github.com/mattn/go-isatty v0.0.9
   go: finding github.com/valyala/fasttemplate v1.0.1
   go: finding github.com/valyala/bytebufferpool v1.0.0
   go: finding golang.org/x/net v0.0.0-20190404232315-eb5bcb51f2a3
   go: finding golang.org/x/text v0.3.0

      ____    __
     / __/___/ /  ___
    / _// __/ _ \/ _ \
   /___/\__/_//_/\___/ v3.3.10-dev
   High performance, minimalist Go web framework
   https://echo.labstack.com
   ____________________________________O/_______
                                       O\
   ⇨ http server started on [::]:8080
   ```

   这里解释一下*go run* 和*go build*都会发现go mod进行自动查找依赖进行下载

4. 查看一下go.mod文件

   ```go
   module github.xiaka53/demo

   go 1.13

   require (
   	github.com/labstack/echo v3.3.10+incompatible // indirect
   	github.com/labstack/gommon v0.3.0 // indirect
   	golang.org/x/crypto v0.0.0-20200429183012-4b2356b1ed79 // indirect
   )
   ```

   go module安装package的原则是先拉最新的release tag，若无tag则拉最新的commit。

5. go会自动生成一个go.sum的文件来记录依赖图：

   ```go
   github.com/davecgh/go-spew v1.1.0/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
   github.com/labstack/echo v3.3.10+incompatible h1:pGRcYk231ExFAyoAjAfD85kQzRJCRI8bbnE7CX5OEgg=
   github.com/labstack/echo v3.3.10+incompatible/go.mod h1:0INS7j/VjnFxD4E2wkz67b8cVwCLbBmJyDaka6Cmk1s=
   github.com/labstack/gommon v0.3.0 h1:JEeO0bvc78PKdyHxloTKiF8BD5iGrH8T6MSeGvSgob0=
   github.com/labstack/gommon v0.3.0/go.mod h1:MULnywXg0yavhxWKc+lOruYdAhDwPK9wf0OL7NoOu+k=
   github.com/mattn/go-colorable v0.1.2 h1:/bC9yWikZXAL9uJdulbSfyVNIR3n3trXl+v8+1sx8mU=
   github.com/mattn/go-colorable v0.1.2/go.mod h1:U0ppj6V5qS13XJ6of8GYAs25YV2eR4EVcfRqFIhoBtE=
   github.com/mattn/go-isatty v0.0.8/go.mod h1:Iq45c/XA43vh69/j3iqttzPXn0bhXyGjM0Hdxcsrc5s=
   github.com/mattn/go-isatty v0.0.9 h1:d5US/mDsogSGW37IV293h//ZFaeajb69h+EHFsv2xGg=
   github.com/mattn/go-isatty v0.0.9/go.mod h1:YNRxwqDuOph6SZLI9vUUz6OYw3QyUt7WiY2yME+cCiQ=
   github.com/pmezard/go-difflib v1.0.0/go.mod h1:iKH77koFhYxTK1pcRnkKkqfTogsbg7gZNVY4sRDYZ/4=
   ...此处省略
   ```

   此时在执行go build和go run就会略过检查并安装依赖的步骤。

6. 如果有包更新了怎么办

   * go list -m -u all （检查可以升级的package）
   * go get -u 包名   （升级后会将新的依赖版本更新到go.mod）
   * go get -u   (升级所有依赖)
   * go get -u=patch  (将会升级到最新的版本)
   * go get 包名@版本号  （将包升级到指定版本）
   * go get  （如果有版本的更改，go.mod也会更改）

### 升级现有项目

1. 在已有项目的根目录下输入 go mod init  <包名> 来初始化项目

   如果不设置包名的话，将会默认包名为项目文件名

2. 执行go run  或者是 go build就可以进行对依赖包的下载



### 踩坑记录

一、 本地私有包mod引入

​	如果私有包不香发不到网上，就需要进行手动添加require，然后replace进行替换，将私有包指向本地module的绝对路径或者相对路径。（注意⚠️：私有包内也需要有go.mod文件，否则会报错：error loading module requirements）

​	例如(把本地一个名为demo的包引入) 修改go.mod文件：

```go
require(
	demo v0.0.0
)

replace demo v0.0.0 => ./demo
```

二、mod进行以来下载时出错

​	错误信息：

```
Get https://sum.golang.org/lookup/xxxxxx: dial tcp 216.58.200.49:443: i/o timeout
```

​	因为golang.org的包很多都被墙了，所以在这里就需要设置代理了。

​	解决方法：

1.   更改go的代理

     ```
     go env -w GOPROXY=https://goproxy.cn,direct
     ```

2.   设置GOPRIVATE来跳过私有库，比如gitee

     ```
     go env -w GOPRIVATE=*.gitlab.com,*.gitee.com
     ```

3.   关闭包的有效性验证

     ```
     go env -w GOSUMDB=off
     ```

三、 在依赖都下载完的情况下，goland编辑器没有包提示

​	这个问题辗转了我很久，因为goland编辑器不识别从gopath更改为gomod的gomod项目，所以需要把依赖包down到vendor文件夹中，执行方法：

​	go mod vendor

​	一般情况下都会搞定这个问题。又一次发现执行完之后依然不行，然后检查vendor中依赖包并不够，然后进行download所有包

​	go mod download

