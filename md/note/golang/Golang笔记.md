### 打包

#### 简单打包

-o参数指定打出的包的名字，-ldflags="-H=windowsgui"取消黑窗口

```cmd
go build -ldflags="-H=windowsgui" -o name.exe
```

#### 带图标

1. **安装 rsrc 工具：**

   ```cmd
   go install github.com/akavel/rsrc
   ```

2. **创建图标文件（例如 favicon.ico）：**

3. **使用 rsrc 工具嵌入图标资源：**

   ```cmd
   rsrc -ico favicon.ico -o rsrc.syso
   ```

4. **编译可执行文件并指定名称：**

   ```cmd
   go build -ldflags="-H=windowsgui" -o name.exe
   ```



### GUI

#### [walk库](https://github.com/lxn/walk)

> 多嘴提一句，这库半成品都算不上

1. **安装walk**

   ```cmd
   go get github.com/lxn/walk
   ```

2. **安装rsrc**

   ```
   go install github.com/akavel/rsrc
   ```

3. **编写gui.manifest文件**

   ```xml
   <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
   <assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0" xmlns:asmv3="urn:schemas-microsoft-com:asm.v3">
       <assemblyIdentity version="1.0.0.0" processorArchitecture="*" name="SomeFunkyNameHere" type="win32"/>
       <dependency>
           <dependentAssembly>
               <assemblyIdentity type="win32" name="Microsoft.Windows.Common-Controls" version="6.0.0.0" processorArchitecture="*" publicKeyToken="6595b64144ccf1df" language="*"/>
           </dependentAssembly>
       </dependency>
       <asmv3:application>
           <asmv3:windowsSettings xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">
               <dpiAware>true</dpiAware>
           </asmv3:windowsSettings>
       </asmv3:application>
   </assembly>
   ```

   

4. **生成rsrc.syso**

   ```cmd
   rsrc -manifest gui.manifest -o rsrc.syso
   ```

5. **编写HelloWorld**

   ```go
   import (
   	"github.com/lxn/walk"
   	. "github.com/lxn/walk/declarative"
   	"strings"
   )
   
   func main() {
   	var inTE, outTE *walk.TextEdit
   
   	MainWindow{
   		Title:  "SCREAMO",
   		Size:   Size{600, 400},
   		Layout: VBox{},
   		Children: []Widget{
   			HSplitter{
   				Children: []Widget{
   					TextEdit{AssignTo: &inTE},
   					TextEdit{AssignTo: &outTE, ReadOnly: true},
   				},
   			},
   			Composite{
   				MaxSize: Size{Height: 50},
   				Layout:  HBox{},
   				Children: []Widget{
   					PushButton{
   						Text: "SCREAM",
   						OnClicked: func() {
   							outTE.SetText(strings.ToUpper(inTE.Text()))
   						},
   					},
   				},
   			},
   		},
   	}.Run()
   }
   ```

6. **如果报错TTM_ADDTOOL failed**

   > 修改Golang IDE Run/Debug Configurations，将Run kind改成Directory



### 编译成DLL

> main方法必须，需要导出的方法添加注释

```go
package main

import (
	"C"
	"github.com/go-toast/toast"
)

//export Notice
func Notice(appID, title, msg *C.char) {
	notification := toast.Notification{
		AppID:   C.GoString(appID),
		Title:   C.GoString(title),
		Message: C.GoString(msg),
	}

	err := notification.Push()
	if err != nil {
		panic(err)
	}
}

func main() {}
```

```cmd
go build -ldflags "-w -s" -buildmode=c-shared -o libnotice.dll notice.go
```

