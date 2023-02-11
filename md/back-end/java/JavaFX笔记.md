## 一、架构图

![architecture](https://zergqueen.gitee.io/images/factorio/%E6%B7%B1%E5%BA%A6%E6%88%AA%E5%9B%BE_%E9%80%89%E6%8B%A9%E5%8C%BA%E5%9F%9F_20230203125846.png)

在**JavaFX**中，**Stage**是应用程序窗口，其中包含称为**Scene**的空间。 **Scene**包含界面的组件，如`Button`，`Text`，…或容器。



JavaFX主要包含两种写法：

1. 纯Java代码
2. Fxml + Css + Java controller（本笔记按照这种方式记录）



##  二、常用方法

### Stage stage

- 设置窗口标题

  ```java
  stage.setTitle("Hello!");
  ```

- 设置窗口图标

  ```java
  stage.getIcons().add(new Image(Objects.requireNonNull(HelloApplication.class.getResourceAsStream("images/archlinux.png"))));
  ```

- 窗口最小化

  ```java
  stage.setIconified(true);
  ```

- 窗口最大化

  ```java
  stage.setMaximized(true);
  ```

- 关闭窗口

  ```java
  stage.close();
  ```

- 是否允许调整窗口大小，false表示不能

  ```java
  stage.setResizable(false);
  ```

- 添加监听器

  1. 高度和宽度监听器

     ```java
     stage.heightProperty().addListener((observable, oldValue, newValue) -> {
         System.out.println("oldValue:" + oldValue + "," + "newValue:" + newValue);
     });
     stage.widthProperty().addListener((observable, oldValue, newValue) -> {
         System.out.println("oldValue:" + oldValue + "," + "newValue:" + newValue);
     });
     ```

  2. 坐标监听器

     ```java
     stage.xProperty().addListener((observable, oldValue, newValue) -> {
     	System.out.println("oldValue:" + oldValue + "," + "newValue:" + newValue);
     });
     stage.yProperty().addListener((observable, oldValue, newValue) -> {
     	System.out.println("oldValue:" + oldValue + "," + "newValue:" + newValue);
     });
     ```

- 窗口全屏（设置了scene才会生效）

  ```java
  stage.setFullScreen(true);
  ```

- 设置窗口透明度（范围：0~1）

  ```java
  stage.setOpacity(0.8);
  ```

- 设置窗口层级置顶（Linux上不生效）

  ```java
  stage.setAlwaysOnTop(true);
  ```

- 设置窗口大小和位置

  ```java
  stage.setX(100);
  stage.setY(100);
  stage.setHeight(100);
  stage.setWidth(100);
  ```

- 设置窗口初始样式（必须在窗口可见之前调用）

  1. DECORATED （默认，白色背景，带有最小化/最大化/关闭等有操作系统平台装饰）

     ```java
     stage.initStyle(StageStyle.DECORATED);
     ```

  2. TRANSPARENT （透明背景，没有操作系统平台装饰，需要平台支持，否则降级为装饰样式）

     ```java
     stage.initStyle(StageStyle.TRANSPARENT);
     ```

  3. UNIFIED （有操作系统平台装饰，消除装饰和内容之间的边框，内容背景和边框背景一致，需要平台支持，否则降级为装饰样式）

     ```java
     stage.initStyle(StageStyle.UNIFIED);
     ```

  4. UTILITY（白色背景，只有关闭操作系统平台装饰）

     ```java
     stage.initStyle(StageStyle.UTILITY);
     ```

  5. UNDECORATED（白色背景，没有操作系统平台装饰）

     ```java
     stage.initStyle(StageStyle.UNDECORATED);
     ```

- 设置初始化窗口模式（必须在窗口可见之前调用，主stage不能设置）

  1. 模态窗口（stage窗口的拥有者窗口在stage窗口关闭前无法操作，仅对stage窗口可见前就已经可见的窗口生效）

     ```java
     stage.initModality(Modality.WINDOW_MODAL);A
     ```

  2. 应用窗口（其他所有窗口在stage窗口关闭前都无法操作，仅对stage窗口可见前就已经可见的窗口生效）

     ```java
     primaryStage.setTitle("demo");
     Scene scene = new Scene(new Group(), 400, 600);
     primaryStage.setScene(scene);
     Stage stage = new Stage();
     stage.setScene(new Scene(new Group()));
     stage.initModality(Modality.APPLICATION_MODAL);
     primaryStage.show();
     stage.show();
     ```
     
     

### Platform

- JavaFX 应用程序终止

  ```java
  Platform.exit();
  ```

- 表示系统是否支持StageStyle.UNFIED

  ```java
  Platform.isSupported(UNIFIED_WINDOW)
  ```

- 在某些时候在 JavaFX 应用程序线程上运行指定的 Runnable，单线程

  > [多线程在这](####JavaFX多线程)

  ```java
  Platform.runLater(() -> {
      stage.setHeight(500);
  });
  ```

- 关闭所有窗口程序是否跟着关闭，默认为true

  ```java
  Platform.setImplicitExit(false);
  ```

  

### Node node

- 指定坐标是否有节点（只检测node左上角）

  ```java
  node.contains(0， 0)
  ```

- 设置位置大小

  ```java
  node.setLayoutX();
  node.setLayoutY();
  node.setPrefHeight();
  node.setPrefWidth();
  ```

- 添加监听器

  1. 自子向父传递

     ```java
     node.addEventHandler(MouseEvent.MOUSE_CLICKED, event -> {
         if (MouseButton.PRIMARY == event.getButton()){
             System.out.println("是左键");
         }
     });
     ```

  2. 自父向子传递

     ```java
     node.addEventFilter(MouseEvent.MOUSE_CLICKED, event -> {
         if (MouseButton.PRIMARY == event.getButton()){
             System.out.println("是左键");
         }
     });
     ```

  3. addEventFilter的简单写法

     ```java
     node.setOnMouseClicked(event -> {});
     node.setOnKeyPressed(event -> {});
     ```

- 设置节点方向

  ```java
  node.setNodeOrientation(NodeOrientation.INHERIT);
  ```
  
- 获取bound

  - getLayoutBounds()

    > 以自身为基准，除去边框
  
  - getBoundsInLocal()
  
    > 以自身为基准，包含边框
  
  - getBoundsInParent()
  
    > 以父节点为基准，包含边框
  
- 添加用户数据

  1. 只能设置一个，后设置的会覆盖前设置的

     ```java
     node.setUserData();
     node.getUserData();
     ```

  2. 能设置多个，类似map

     ```java
     node.getProperties()
     ```

  

### Parent parent

> Parent继承了Node

- 获取子节点

  ```java
  parent.getChildren()
  ```

- 添加、删除子节点

  ```java
  parent.getChildren().add();
  parent.getChildren().addAll();
  parent.getChildren().remove();
  ```

- 监听器

  1. 节点数量变化监听器

     ```java
     group.getChildren().addListener((ListChangeListener<Node>) c -> {
     });
     ```

     

### Screen

- 获取主屏幕信息

  ```java
  Screen.getPrimary() 
  ```

- 获取所有屏幕信息

  ```java
  Screen.getScreens()
  ```



### 剪切板

```java
Clipboard systemClipboard = Clipboard.getSystemClipboard();
KeyCodeCombination keyCodeCombination = new KeyCodeCombination(KeyCode.V, KeyCodeCombination.CONTROL_ANY);
scene.getAccelerators().put(keyCodeCombination, () -> {
//            获取剪切板内容
    System.out.println(systemClipboard.getString());
    List<File> files = systemClipboard.getFiles();
    for (File file : files) {
        System.out.println(file.getAbsolutePath());
    }
    System.out.println(systemClipboard.getImage());
});
//        放入内容到剪切板
ClipboardContent clipboardContent = new ClipboardContent();
clipboardContent.putString("hello");
systemClipboard.setContent(clipboardContent);
//        清空剪切板
systemClipboard.clear();
```



### 文件窗口

```java
public class FileComponentController {

    @FXML
    protected void select(){
        FileChooser fileChooser = new FileChooser();
        fileChooser.setTitle("选择单个文件");
        fileChooser.setInitialDirectory(new File("/home/zerg/Downloads/chrome"));
        fileChooser.getExtensionFilters().addAll(
                new FileChooser.ExtensionFilter("图片", "*.jpg", "*.png"),
                new FileChooser.ExtensionFilter("视频", "*.mkv", "*.mp4")
        );
        File chooseFile = fileChooser.showOpenDialog(new Stage());
        System.out.println(chooseFile == null? "" : chooseFile.getAbsolutePath());
    }
    @FXML
    protected void multipleSelect(){
        FileChooser fileChooser = new FileChooser();
        fileChooser.setTitle("选择多个文件");
        fileChooser.setInitialDirectory(new File("/home/zerg/Downloads/chrome"));
        fileChooser.getExtensionFilters().addAll(
                new FileChooser.ExtensionFilter("图片", "*.jpg", "*.png"),
                new FileChooser.ExtensionFilter("视频", "*.mkv", "*.mp4")
        );
        List<File> chooseFiles = fileChooser.showOpenMultipleDialog(new Stage());
        System.out.println(chooseFiles == null? "" : chooseFiles.size());
    }

    @FXML
    protected void saveFile(){
        FileChooser fileChooser = new FileChooser();
        fileChooser.setTitle("保存文件");
        fileChooser.setInitialDirectory(new File("/home/zerg/Desktop"));
        File file = fileChooser.showSaveDialog(new Stage());
        if (file == null){
            return;
        }
        try(FileWriter fileWriter = new FileWriter(file)) {
            fileWriter.write("你好");
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    @FXML
    protected void dirsSelect(){
        DirectoryChooser directoryChooser = new DirectoryChooser();
        directoryChooser.setTitle("目录选择");
        File file = directoryChooser.showDialog(new Stage());
        System.out.println(file == null? "" : file.getAbsolutePath());
    }
}
```



### 监听器

> 所有带property的属性都可以添加监听器

1. **文本框文字改变监听器**

   ```java
   password.textProperty().addListener((observable, oldValue, newValue) -> {
   	System.out.println(observable.getValue());
   });
   ```

2. **文本框选择文字监听器**

   ```java
   password.selectedTextProperty().addListener((observable, oldValue, newValue) -> {
   	System.out.println(observable.getValue());
   });
   ```

3. **集合监听器**

   ```java
   //重要!!!重写call方法可以监听property内部值的更新，call方法返回的Observable数组里的property的值的改变可以被监听到
   SimpleListProperty<SimpleStringProperty> list = new SimpleListProperty<>(FXCollections.observableArrayList(param -> {
       System.out.println("call");
       return new Observable[]{param};
   }));
   list.addListener((ListChangeListener<SimpleStringProperty>) c -> {
       System.out.println("c:" + c);
       while (c.next()){
           System.out.println("wasUpdated:" +c.wasUpdated());
       }
   });
   list.addListener((observable, oldValue, newValue) -> {
       System.out.println("newValue:" + newValue);
   });
   SimpleStringProperty s1 = new SimpleStringProperty("hello");
   list.add(s1);
   System.out.println();
   s1.set("world");
   System.out.println();
   list.remove(0);
   ```

4. **键盘监听器**

   > 判断是否是指定键

   ```java
   KeyCode.A.getName().equals(event.getCode().getName());
   event.getCharacter//用于获取中文状态时的输入
   ```

   > 作用在输入框的监听器

   ```java
   setOnKeyTyped();
   ```

   > 某些无法获取焦点的控件可以通过监听鼠标点击然后强制获得焦点来监听键盘

   ```java
   requestFocus();
   ```

5. **鼠标监听器**

   ```java
   scene.setOnMouseClicked(event -> {
   //            alt建是否按下
       System.out.println(event.isAltDown());
   //             点击次数
       System.out.println(event.getClickCount());
   //            右键是否按下
       System.out.println(event.isSecondaryButtonDown());
   });
   ```

6. **鼠标拖拽监听器**

   - MouseDragOver

     > 在node内按住左键且在node内移动时触发，**需要提前调用startFullDrag()**

   - MouseDragReleased

     > 在node内按住左键且释放左键时触发，**需要提前调用startFullDrag()**

   - MouseDragEntered

     > 在node内按住左键第一次移动时时触发，**需要提前调用startFullDrag()**

   - MouseDragExited

     > 在node内按住左键然后离开node或在node内释放左键时触发，**需要提前调用startFullDrag()**

   - MouseDragged

     > 在node内按住左键，然后只要移动且左键没释放就触发

   ```java
   @FXML
   private Button dragButton;
   //在目标内开始拖拽时调用，调用一次。如果调用了event.setDragDetect(true)则不需要拖拽
   @FXML
   protected void onDragDetected(MouseEvent event){
       System.out.println("onDragDetected");
    //使用此节点作为手势源启动完整的按下-拖动-释放手势
       dragButton.startFullDrag();
   }
   
   /**
    * 配合startFullDrag()才能生效,onMouseDragReleased、onMouseDragEntered、onMouseDragExited也一样
    * @param event
    */
   @FXML
   protected void onMouseDragOver(MouseEvent event){
       System.out.println("onMouseDragOver");
   }
   ```

7. **拖拽监听器**

   - OnDragDetected

     > 定义在检测到拖动手势时要调用的函数。这是开始拖放操作的正确位置。

   - OnDragDone

     > 当此节点被拖拽后调用

   - OnDragDropped

     > 当拖拽东西到此节点里释放，**需要提前设置传输模式**，event.acceptTransferModes(TransferMode.COPY_OR_MOVE);

   - OnDragEntered

     > 当拖拽东西到此节点里时触发，此后在节点内移动不会再次触发，除非移出节点外又进来

   - OnDragExited

     > 当拖拽东西到此节点里释放或离开此节点时触发

   - OnDragOver

     > 当拖拽东西到此节点里移动触发

8. **鼠标监听范围**

   > 将范围由实体部分提升至占用部分，圆的占用部分为外接矩形

   ```java
   node.setPickOnBounds(true)
   ```

9. **阻值冒泡**

   ```java
   node.setMouseTransparent(true);
   ```




### 绑定

> 对Property进行绑定，返回一个Binding

- 单向绑定

  > 注意：集合的单向绑定相当于双向绑定

  ```java
  property1.bind(property2)
  ```

- 双向绑定

  ```java
  property1.bindBiderectional(property2)
  ```

- 自绑定

  ```java
  SimpleIntegerProperty simpleIntegerProperty = new SimpleIntegerProperty(10);
  IntegerBinding integerBinding = Bindings.createIntegerBinding(() -> {
      if (simpleIntegerProperty.get() == 1) {
          return 250;
      } else if (simpleIntegerProperty.get() == 2) {
          return 260;
      }
      return 0;
  }, simpleIntegerProperty);
  integerBinding.addListener((observable, oldValue, newValue) -> {
      System.out.println("newValue:" + newValue);
  });
  simpleIntegerProperty.set(1);
  ```

- 嵌套绑定

  ```java
  Student student = new Student();
  Address address = new Address();
  address.setCity("changsha");
  address.setProvince("hunan");
  student.setAddress(address);
  student.setName("lisi");
  SimpleObjectProperty<Student> objectProperty = new SimpleObjectProperty<>(student);
  StringBinding stringBinding = Bindings.selectString(objectProperty, "address", "province");
  stringBinding.addListener((observable, oldValue, newValue) -> {
      System.out.println("newValue:" + newValue);
  });
  address.setProvince("beijing");
  ```

  ```java
  public class Student {
      private String name;
  
      private final SimpleObjectProperty<Address> address = new SimpleObjectProperty<>();
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public Address getAddress() {
          return address.get();
      }
  
      public void setAddress(Address address) {
          this.address.set(address);
      }
  //	必须    
      public SimpleObjectProperty<Address> addressProperty(){
          return address;
      }
  }
  ```

  ```java
  public class Address {
      private final SimpleStringProperty province = new SimpleStringProperty();
  
      private String city;
  
      public String getProvince() {
          return province.get();
      }
  
      public void setProvince(String province) {
          this.province.set(province);
      }
  
      public String getCity() {
          return city;
      }
  
      public void setCity(String city) {
          this.city = city;
      }
  //	必须
      public SimpleStringProperty provinceProperty(){
          return province;
      }
  }
  ```

- 绑定计算

  > 相当于vue里的计算属性
  >
  > 当a后者b发生改变时，c的值会发生改变。

  ```java
  SimpleIntegerProperty a = new SimpleIntegerProperty(1);
  SimpleIntegerProperty b = new SimpleIntegerProperty(1);
  NumberBinding c = a.add(b);//还有subtract()、divide()、multiply()、negate()、greaterThan()、lessThan()等方法
  c.addListener((observable, oldValue, newValue) -> {
      System.out.println("newValue:" + newValue);
  });
  ```

- 自定义绑定器Binding

  ```java
  public class MyIntegerBinding extends IntegerBinding{
  
      private final SimpleIntegerProperty simpleIntegerProperty = new SimpleIntegerProperty();
  
      public MyIntegerBinding(int value) {
          simpleIntegerProperty.set(value);
      }
  
      @Override
      protected int computeValue() {
          return simpleIntegerProperty.get() + 25;
      }
  }
  
  MyIntegerBinding myIntegerBinding = new MyIntegerBinding(19);
  myIntegerBinding.addListener((observable, oldValue, newValue) -> {});
  new SimpleIntegerProperty().bind(myIntegerBinding);
  ```




### 自定义转换器

> 转换器里的toString方法返回的值会经由cellFactory的updteTtem方法进行加工改造然后呈现到界面上

```xml
<ChoiceBox layoutX="40" layoutY="30" BorderPane.alignment="TOP_RIGHT" fx:id="choice">
    <converter>
        <StudentConverter/>
    </converter>
    <Student fx:value="zerg"/>
    <Student fx:value="queen"/>
    <Student fx:value="jack"/>
</ChoiceBox>
```

```java
public class StudentConverter extends StringConverter<Student> {
    @Override
    public String toString(Student object) {
        return object == null? "" : object.getSimpleName();
    }

    @Override
    public Student fromString(String string) {
        Student student = new Student();
        student.setName(string);
        return student;
    }
}
```



### 加工器cellFactory（Callback）

> 将由转换器返回的值加工改造呈现到界面

```xml
<ComboBox>
    <cellFactory>
        <MyCallback/>
    </cellFactory>
    <items>
        <FXCollections fx:factory="observableArrayList">
            <String fx:value="李四"/>
        </FXCollections>
    </items>
</ComboBox>
```

```java
public class MyCallback<P, T> implements Callback<ListView<P>, ListCell<String>> {

    @Override
    public ListCell<String> call(ListView<P> param) {
        return new ListCell<>(){
//                通过setGraphic方法将加工改造后的东西呈现到界面            
            @Override
            protected void updateItem(String item, boolean empty) {
                this.setGraphic(new Button(item));
                super.updateItem(item, empty);
            }
        };
    }
}
```

![cellfactory](https://zergqueen.gitee.io/images/myblog/javafx/cellfactory.png)



### ListCell

四种特殊自带样式，[在这里](####ListView)

> 自定义ListCell，可以通过重写updateItem、startEdit、commitEdit、cancelEdit等方法实现。



### 其他

#### 设置占位符

> 当没有其他东西时显示

```xml
<ComboBox>
    <placeholder>
        没有东西呢！
    </placeholder>
</ComboBox>
```

#### managed、visible、opacity对比

> managed为false表示此控件不受父类组件控制了，其占用的空间也会让出来，然后也看不见了。visble和opacity都不会腾出空间



#### 创建新光标

> Cursor.cursor()需要一个绝对路径

```java
Cursor.cursor(Objects.requireNonNull(getClass().getResource("images/archlinux.png")).toExternalForm())
```



#### 打开网址

> 用默认浏览器打开网址（getHostServices()是Application类的实例方法）

```java
getHostServices().showDocument("xiaojiawei.club");
```



#### 添加快捷键

1. 绑定到按键（Linux上无法生效）

   ```java
   Button button = new Button();
   button.setOnAction(event -> System.out.println("hello"));
   scene.addMnemonic(new Mnemonic(button , new KeyCodeCombination(KeyCode.K, KeyCombination.CONTROL_DOWN, KeyCombination.SHIFT_DOWN)));
   ```

2. 不绑定到按键（推荐）

   ```java
   scene.getAccelerators().put(new KeyCodeCombination(KeyCode.K, KeyCombination.CONTROL_DOWN), () -> {
   	System.out.println("hello");
   });
   ```

3. 绑定到按键的第二种写法

   ```java
   scene.addMnemonic(new Mnemonic(node, KeyCombination.valueOf("ctrl+alt+k")));
   ```



#### 提示信息

> ToolTip，类似Html里的title

```java
Tooltip tooltip = new Tooltip("hello");
tooltip.setShowDelay(Duration.millis(500));
control.setTooltip(tooltip);
```

```xml
<Button text="hello">
    <tooltip>
        <Tooltip autoFix="false" hideOnEscape="false" autoHide="true" x="100" text="你好" prefHeight="100" prefWidth="100" anchorX="-50" anchorY="-50" anchorLocation="CONTENT_BOTTOM_LEFT">
                        <graphic>
                            <ImageView>
                                <Image url="@../images/archlinux.png"/>
                            </ImageView>
                        </graphic>
                    </Tooltip>
    </tooltip>
</Button>
```



#### 图片操作

1. 监听器

   > 监听器有errorProperty、exceptionProperty、progressProperty

2. 形状操作

   ```xml
   <ImageView fitHeight="160" fitWidth="250" fx:id="image">
       <!--剪切-->
       <clip>
           <Rectangle arcWidth="20" arcHeight="20" width="${image.fitWidth}" height="${image.fitHeight}" />
       </clip>
       <cursor>
           <ImageCursor fx:constant="MOVE"/>
       </cursor>
       <!--smooth：缩放是是否降低质量， preserveRatio是否保持比例，backgroundLoading是否后台加载-->
       <Image  url="@../images/gaomu.jpg" smooth="false" preserveRatio="true" backgroundLoading="true" />
       <!--minX和minY相当于坐标，height和width是大小-->
       <viewport>
           <Rectangle2D height="300" width="600" minX="400" minY="400"/>
       </viewport>
   </ImageView>
   ```

3. 操作图片像素

   - 获取图片像素

     ```java
     Image image = new Image(Objects.requireNonNull(getClass().getResource("images/gaomu.jpg")).toExternalForm());
     PixelReader pixelReader = image.getPixelReader();
     byte[] bytes = new byte[10 * 10 * 4];
     pixelReader.getPixels(100, 100, 10, 10, PixelFormat.getByteBgraPreInstance(), bytes, 0, 10 * 4);
     for (int i = 0; i < bytes.length; i += 4) {
         System.out.println("R:" + (bytes[i] & 0xff));
         System.out.println("G:" + (bytes[i + 1] & 0xff));
         System.out.println("B:" + (bytes[i + 2] & 0xff));
         System.out.println("O:" + (bytes[i + 3] & 0xff));
     }	
     
     int[] ints = new int[10 * 10];
     pixelReader.getPixels(100, 100, 10, 10, PixelFormat.getIntArgbPreInstance(), ints, 0, 10);
     for (int i = 0; i < ints.length; i++) {
         System.out.println("R:" + (ints[i] >> 24 & 0xff));
         System.out.println("G:" + (ints[i] >> 16 & 0xff));
         System.out.println("B:" + (ints[i] >> 8 & 0xff));
         System.out.println("O:" + (ints[i] & 0xff));
     }
     ```

   - 写入图片像素

     ```java
     WritableImage writableImage = new WritableImage(10, 10);
     PixelWriter pixelWriter = writableImage.getPixelWriter();
     pixelWriter.setArgb(5, 5, 0);
     ```




#### 设置文本框背景提示信息

> 相当于Html里的placeholder

```java
textInputControl.setPromptText("请输入六位密码");	
```



#### 事件

- 事件调用

  > 激发node的与event相关的事件

  ```java
  Event.fireEvent(node, event);
  ```

- 事件消费

  > 被消费的事件无法被传递，阻止冒泡这种

  ```java
  event.consume();
  ```

  



#### JavaFX多线程

> 某些敏感操作只有使用JavaFX的多线程才能完成
>
> [单线程在这](##Platform)

```java
ScheduledService<Integer> scheduledService = new ScheduledService<>() {

    private int count;

    @Override
    protected Task<Integer> createTask() {
        return new Task<>() {
            @Override
            protected void updateValue(Integer value) {
                if (value == 2){
                    cancel();
                    //不能在call方法里关闭stage
                    stage.close();
                }
            }

            @Override
            protected Integer call() {
                System.out.println(count++);
                return count;
            }
        };
    }
};
scheduledService.setPeriod(Duration.seconds(1));
scheduledService.start();
```



#### 设置展示排版

```java
labeled.setContentDisplay(ContentDisplay.RIGHT);
```



#### PropertyChangeSupport

> 和SimpleStringProperty，SimpleIntegerProperty等类似，但这是以map的方式存储，且能存储旧值和新值



#### 三目运算When

```java
SimpleBooleanProperty a = new SimpleBooleanProperty(true);
When when = new When(a);
NumberBinding otherwise = when.then(10).otherwise(20);
System.out.println(otherwise.getValue());
```



#### 创建永久置顶的窗口

> javafx的stage.setAlwaysOnTop()方法没有用

添加swing依赖

```xml
<dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-swing</artifactId>
    <version>17.0.2</version>
</dependency>
```

需要在**module-info.java**文件里导包

```java
requires javafx.swing;
```

工具包

```java
import javafx.application.Platform;
import javafx.embed.swing.JFXPanel;
import javafx.scene.Scene;
import javafx.stage.Stage;

import javax.swing.*;

/**
 * @author 肖嘉威
 * @email xjw580@qq.com
 * @date 2023/2/8 下午10:42
 */
public class TopStageUtil {

    /**
     * 创建永久置顶的窗口
     * @param title
     * @param scene
     * @param width
     * @param height
     */
    public static void createTopWindow(String title, Scene scene, int width, int height){
        SwingUtilities.invokeLater(() -> initAndShowGUI(title, scene, width, height));
        new Stage().setAlwaysOnTop();
    }

    private static void initAndShowGUI(String title, Scene scene, int width, int height) {
        JFrame frame = new JFrame(title);
        frame.setSize(width, height);
        final JFXPanel fxPanel = new JFXPanel();
        frame.add(fxPanel);
        frame.setAlwaysOnTop(true);
        frame.setVisible(true);
        Platform.runLater(() -> initFX(fxPanel, scene));
    }

    private static void initFX(JFXPanel fxPanel, Scene scene) {
        fxPanel.setScene(scene);
    }
}
```



#### 设置窗口圆角

```java
scene.setFill(Paint.valueOf("#FFFFFF00"));
stage.initStyle(StageStyle.TRANSPARENT);
```

```xml
<AnchorPane style="-fx-background-color: #366ACE;-fx-background-radius: 30"></AnchorPane>
```



## 三、Controller

1. @FXML

   > 使用@FXML对成员变量和成员方法标记，以供FXML文件里通过fx:id使用使用@FXML对成员变量和成员方法标记，以供FXML文件里通过fx:id使用

2. Initializable

   > 实现Initializable接口，重写initialize方法，initialize方法会在FXML文件里的根元素完全处理后调用



## 四、FXML

### 布局

#### AnchorPane

> 绝对布局



#### FlowPane

> 它在一行上排列连续的子组件，并且如果当前行填满了以后，则自动将子组件向下推到下一行。
>
> 有两种取向方式：垂直和水平，需要设置orientation属性，默认为HORIZONTAL

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.FlowPane?>
<FlowPane xmlns="http://javafx.com/javafx"
          xmlns:fx="http://javafx.com/fxml"
          hgap="40" vgap="20"
          prefHeight="400.0" prefWidth="400.0">
    <padding>
        <Insets bottom="20.0" left="20.0" right="20.0" top="20.0"/>
    </padding>
    <Button >button1</Button>
    <Button >button2</Button>
    <Label >hello</Label>
    <Label >world</Label>
    <Label >你好</Label>
</FlowPane>
```

![flowopane](https://zergqueen.gitee.io/images/factorio/flowpane.png)



#### TextFlow

> 和FlowPane差不多，但对文字更友好



#### TilePane

> 和FlowPane类似，但更加整齐

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.scene.control.*?>
<?import javafx.scene.layout.TilePane?>
<TilePane xmlns="http://javafx.com/javafx"
          xmlns:fx="http://javafx.com/fxml"
          hgap="30"
          vgap="20"
          prefHeight="400.0" prefWidth="600.0">
    <Button text="b1" prefWidth="100" prefHeight="50"/>
    <Button text="b2"/>
    <Button text="b3"/>
    <Button text="b4"/>
    <Button text="b5"/>
    <Button text="b6"/>
    <Button text="b7"/>
    <Button text="b8"/>
</TilePane>
```

![tilepane](https://zergqueen.gitee.io/images/factorio/tilepane.png)



#### HBox

> 将JavaFX子节点放在水平行中。 新的子节点附加到右侧的末尾。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.HBox?>
<HBox xmlns="http://javafx.com/javafx"
      xmlns:fx="http://javafx.com/fxml"
      prefHeight="400.0" prefWidth="600.0">
    <spacing>
        20
    </spacing>
    <padding>
        <Insets bottom="20.0" left="20.0" right="20.0" top="20.0"/>
    </padding>
    <Label>hello</Label>
    <Label>world</Label>
    <Label>你好</Label>
    <Label>世界</Label>
    <TextField maxWidth="80">1</TextField>
    <Label>hello</Label>
    <Label>world</Label>
    <Label>你好</Label>
    <Label>世界</Label>
</HBox>
```

![hbox](https://zergqueen.gitee.io/images/factorio/hbox.png)



#### VBox

> 将子节点堆叠在垂直列中

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.Label?>
<?import javafx.scene.layout.VBox?>

<?import javafx.scene.control.Button?>
<?import javafx.scene.control.PasswordField?>
<?import javafx.scene.control.TextArea?>
<?import javafx.scene.text.Text?>
<VBox alignment="CENTER" spacing="20.0" xmlns:fx="http://javafx.com/fxml"
      xmlns="http://javafx.com/javafx/17"
      fx:controller="club.xiaojiawei.controller.HelloController" stylesheets="@css/hello-view.css">
    <padding>
        <Insets bottom="20.0" left="20.0" right="20.0" top="20.0"/>
    </padding>
    <Text>Welcome</Text>
    <TextArea>
        d
    </TextArea>
    <PasswordField fx:id="password" promptText="hello">
    </PasswordField>
    <Label fx:id="welcomeText" />
    <Button text="Hello!" onMouseClicked="#onHelloButtonClick" onKeyPressed="#onKeyPressed" fx:id="button"/>
</VBox>
```

![vbox](https://zergqueen.gitee.io/images/factorio/vbox.png)



#### BorderPane

> 顶部，底部，左，右或中心区域中的子节点。每个区域只能有一个节点。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.scene.control.Button?>
<?import javafx.scene.layout.BorderPane?>


<BorderPane  prefHeight="400.0" prefWidth="400.0" xmlns="http://javafx.com/javafx/19" xmlns:fx="http://javafx.com/fxml/1">
    <top>
        <Button mnemonicParsing="false" text="top" BorderPane.alignment="CENTER" />
    </top>
    <left>
        <Button mnemonicParsing="false" text="left" BorderPane.alignment="CENTER" />
    </left>
    <right>
        <Button mnemonicParsing="false" text="right" BorderPane.alignment="CENTER" />
    </right>
    <center>
        <Button mnemonicParsing="false" text="center" BorderPane.alignment="CENTER" />
    </center>
    <bottom>
        <Button mnemonicParsing="false" text="bottom" BorderPane.alignment="CENTER" />
    </bottom>
</BorderPane>

```

![borderpane](https://zergqueen.gitee.io/images/factorio/borderpane.png)



#### GridPane

> 可以在行，列或单元格级别指定约束。
>
> rowConstraints和columnConstraints是用来设置行列边距的，也可以用vgap和hgap设置行列边距

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.Label?>
<?import javafx.scene.control.PasswordField?>
<?import javafx.scene.control.TextField?>
<?import javafx.scene.layout.ColumnConstraints?>
<?import javafx.scene.layout.GridPane?>
<?import javafx.scene.layout.RowConstraints?>
<?import javafx.scene.text.Text?>

<GridPane maxHeight="-Infinity" maxWidth="-Infinity" minHeight="-Infinity" minWidth="-Infinity" prefHeight="400.0" prefWidth="600.0" xmlns:fx="http://javafx.com/fxml/1" xmlns="http://javafx.com/javafx/19">
    <columnConstraints>
        <ColumnConstraints hgrow="SOMETIMES" minWidth="10.0" prefWidth="100.0" />
        <ColumnConstraints hgrow="SOMETIMES" minWidth="10.0" prefWidth="100.0" />
    </columnConstraints>
    <rowConstraints>
        <RowConstraints minHeight="10.0" prefHeight="30.0" vgrow="SOMETIMES" />
        <RowConstraints minHeight="10.0" prefHeight="30.0" vgrow="SOMETIMES" />
        <RowConstraints minHeight="10.0" prefHeight="30.0" vgrow="SOMETIMES" />
    </rowConstraints>
    <children>
        <Text strokeType="OUTSIDE" strokeWidth="0.0" text="welcome" GridPane.columnSpan="2" textAlignment="CENTER">
            <GridPane.margin>
                <Insets left="250" />
            </GridPane.margin>
        </Text>
        <Label text="account:" GridPane.rowIndex="1" />
        <Label text="password:" GridPane.rowIndex="2" />
        <TextField GridPane.columnIndex="1" GridPane.rowIndex="1" />
        <PasswordField GridPane.columnIndex="1" GridPane.rowIndex="2" />
    </children>
</GridPane>
```

![gridpane](https://zergqueen.gitee.io/images/factorio/gridpane.png)



#### ScrollPane

> 滚动窗口提供UI元素的可滚动视图。
> 我们使用可滚动面板，当需要显示有限的空间大内容。可滚动窗格视口，其将显示内容的一部分，并且在必要时提供滚动条。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import java.lang.*?>
<?import java.util.*?>
<?import javafx.scene.*?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.image.ImageView?>
<?import javafx.scene.image.Image?>
<ScrollPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            prefHeight="400.0" prefWidth="600.0">
    <padding>
        <Insets bottom="20.0" left="20.0" right="20.0" top="20.0"/>
    </padding>
    <ImageView>
        <Image url="@images/background.png"/>
    </ImageView>
</ScrollPane>
```

![scrollpane](https://zergqueen.gitee.io/images/factorio/scrollpane.png)



#### TitledPane

> 标题窗格是具有标题的面板，窗格可以打开和关闭。我们可以添加节点(如UI控件或图像)和一组元素到窗格。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import java.lang.*?>
<?import java.util.*?>
<?import javafx.scene.*?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<!--collapsible 标题是否折叠-->
<!--animated展开收起是否有动画-->
<!--expanded初始时默认展开还是收起-->
<!--nodeOrientation表示方向，从右到左还是从左到右-->
<TitledPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            text="inner"
            collapsible="false"
            animated="false"
            expanded="false"
            nodeOrientation="RIGHT_TO_LEFT"
            prefHeight="400.0" prefWidth="600.0">
    <Label>world!</Label>
</TitledPane>
```

![titledpane](https://zergqueen.gitee.io/images/factorio/titledpane.png)



#### Accordion

> 可以使用手风琴(**accordion**)控件对标题窗格进行分组。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.scene.control.*?>
<!--expandedPane设置启动时打开的TitlePane-->
<Accordion maxHeight="-Infinity" maxWidth="-Infinity" minHeight="-Infinity" minWidth="-Infinity" prefHeight="400.0" prefWidth="600.0" xmlns:fx="http://javafx.com/fxml/1" xmlns="http://javafx.com/javafx/19">
    <panes>
        <TitledPane animated="false" text="untitled 1">
            <Button layoutX="239.0" layoutY="160.0" mnemonicParsing="false" text="Button1"/>
        </TitledPane>
        <TitledPane animated="false" text="untitled 2">
            <Button layoutX="239.0" layoutY="160.0" mnemonicParsing="false" text="Button2"/>
        </TitledPane>
        <TitledPane animated="false" text="untitled 3">
            <Button layoutX="251.0" layoutY="178.0" mnemonicParsing="false" text="Button3"/>
        </TitledPane>
    </panes>
</Accordion>
```

![accordion](https://zergqueen.gitee.io/images/factorio/accordion.png)



#### SplitPane

> 分割布局

```xml
<SplitPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
           orientation="VERTICAL"
           dividerPositions="0.2,0.8,1"
            prefHeight="400.0" prefWidth="600.0">
    <HBox style="-fx-background-color: #FFFFCC">
        <Label text="javafx_study1"/>
    </HBox>
    <HBox style="-fx-background-color: #CCFFFF">
        <Label text="javafx_study2"/>
    </HBox>
    <HBox style="-fx-background-color: #FFCCCC">
        <Label text="javafx_study3"/>
    </HBox>
</SplitPane>
```

![splitpane](https://zergqueen.gitee.io/images/myblog/javafx/splitpane.png)



#### DialogPane

> 弹出框

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.scene.control.ButtonType?>
<?import javafx.scene.control.DialogPane?>
<?import javafx.scene.image.Image?>
<?import javafx.scene.image.ImageView?>
<?import javafx.scene.text.Text?>
<DialogPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            prefWidth="600.0">
        <graphic>
            <ImageView fitWidth="50" fitHeight="50">
                <Image url="@images/archlinux.png"/>
            </ImageView>
        </graphic>
        <headerText>
            360安全卫士
        </headerText>
        <contentText>
            是否不卸载此软件吗？
        </contentText>
        <expandableContent>
            <Text>放弃吧，你卸不掉的</Text>
        </expandableContent>
        <ButtonType buttonData="NO" text="否"/>
        <ButtonType buttonData="YES" text="是" />
</DialogPane>
```

![dialogpane](https://zergqueen.gitee.io/images/factorio/dialogpane.png)



#### Alert

> 是Dialog的子类

```java
Alert alert = new Alert(Alert.AlertType.CONFIRMATION);
//        添加button
alert.getButtonTypes().add(ButtonType.CLOSE);
alert.setHeaderText("炉石传说");
alert.setContentText("是否卸载");
Button ok = (Button) alert.getDialogPane().lookupButton(ButtonType.OK);
Button cancel = (Button) alert.getDialogPane().lookupButton(ButtonType.CANCEL);
ok.setOnAction(event -> System.out.println("ok"));
cancel.setOnAction(event -> System.out.println("cancel"));
alert.show();
```

![alert](https://zergqueen.gitee.io/images/myblog/javafx/alert.png)



### 控件

#### `Button`

#### `TextField`

#### `PasswordField`

#### `Lable`

> 标签

```xml
<AnchorPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            prefHeight="400.0" prefWidth="600.0">
    <Label text="豆瓣" contentDisplay="RIGHT" nodeOrientation="RIGHT_TO_LEFT">
        <graphic>
            <ImageView fitHeight="25" fitWidth="25">
                <Image url="@../images/douban.png"/>
            </ImageView>
        </graphic>
    </Label>
</AnchorPane>
```

![label](https://zergqueen.gitee.io/images/myblog/javafx/label.png)



#### `Separator`

> 分隔符



#### `HyperLink`

> 有超链接的样式文本，除此之外没有特殊的，点击不会跳转

```xml
<Hyperlink>baidu.com</Hyperlink>
```



#### `MenuItem`

> 菜单栏系列，包括**MenuBar、Menu、MenuItem、SeparatorMenuItem、ToggleGroup、RadioMenuItem、CheckMenuItem、CustomMenuItem、MenuButton、SplitMenuButton、ContextMenu**

```xml
<?xml version="1.0" encoding="UTF-8"?>


<?import javafx.scene.control.*?>
<?import javafx.scene.image.Image?>
<?import javafx.scene.image.ImageView?>
<?import javafx.scene.input.KeyCodeCombination?>
<?import javafx.scene.layout.AnchorPane?>
<AnchorPane prefHeight="400.0" prefWidth="400.0" xmlns="http://javafx.com/javafx/19"
            fx:id="anchorPane"
            fx:controller="club.xiaojiawei.controller.component.MenuController"
            xmlns:fx="http://javafx.com/fxml/1">

    <MenuBar prefWidth="${anchorPane.width}">
        <Menu mnemonicParsing="false" text="File">
            <!--items标签可以省略-->
            <Menu mnemonicParsing="false" text="New">
                <MenuItem mnemonicParsing="false" text="Project">
                    <graphic>
                        <ImageView fitHeight="15" fitWidth="15">
                            <Image url="@../images/douban.png"/>
                        </ImageView>
                    </graphic>
                </MenuItem>
                <MenuItem mnemonicParsing="false" text="Module"/>
            </Menu>
            <MenuItem mnemonicParsing="false" text="Open"/>
            <Menu mnemonicParsing="false" text="OpenRecent">
                <!--onMenuValidation只响应快捷键，点击事件不响应-->
                <MenuItem mnemonicParsing="false" text="Manage Projects" onMenuValidation="#onMenu" onAction="#onAction">
                    <accelerator>
                        <!--按ctrl+H-->
                        <KeyCodeCombination alt="UP" code="H" control="DOWN" meta="UP"
                                            shift="UP" shortcut="UP"/>
                    </accelerator>
                </MenuItem>
            </Menu>
            <!--选择框-->
            <CheckMenuItem text="Power Save Mode" disable="true"/>
        </Menu>

        <fx:define>
            <ToggleGroup fx:id="toggleGroup2"/>
        </fx:define>

        <Menu mnemonicParsing="false" text="Edit">
            <MenuItem mnemonicParsing="false" text="Undo typing"/>
            <MenuItem mnemonicParsing="false" text="Redo"/>
            <!--分割线-->
            <SeparatorMenuItem/>
            <MenuItem mnemonicParsing="false" text="Cut"/>
            <SeparatorMenuItem/>
            <!--多选一-->
            <RadioMenuItem text="apple" toggleGroup="$toggleGroup2" selected="true"/>
            <RadioMenuItem text="watermelon" toggleGroup="$toggleGroup2"/>
            <RadioMenuItem text="pear" toggleGroup="$toggleGroup2"/>
        </Menu>

        <Menu mnemonicParsing="false" text="View">
            <MenuItem mnemonicParsing="false" text="Tool Windows"/>
            <!--自定义-->
            <CustomMenuItem>
                <content>
                    <Button>click
                    </Button>
                </content>
            </CustomMenuItem>
        </Menu>
    </MenuBar>
    <!--SplitMenuButton和MenuButton差不多-->
    <SplitMenuButton text="SplitMenuButton"  layoutY="100" onContextMenuRequested="#onContextMenu">
        <!--items标签不能省略-->
        <items>
            <MenuItem text="MenuItem1"/>
            <MenuItem text="MenuItem2"/>
            <MenuItem text="MenuItem3"/>
        </items>
    </SplitMenuButton>
    <!--onContextMenuRequested是监听在此节点上的右键点击-->
    <MenuButton layoutX="200" layoutY="100"  text="MenuButton" onContextMenuRequested="#onContextMenu">
        <!--items标签不能省略-->
        <items>
            <MenuItem text="MenuItem1"/>
            <MenuItem text="MenuItem2"/>
            <MenuItem text="MenuItem3"/>
        </items>
    </MenuButton>
    <!--右键点击出现contextMenu-->
    <Button text="contextMenu" layoutX="200" layoutY="150" onContextMenuRequested="#onContextMenu">
        <contextMenu>
            <ContextMenu>
                <items>
                    <MenuItem text="MenuItem1"/>
                    <MenuItem text="MenuItem2"/>
                    <MenuItem text="MenuItem3"/>
                </items>
            </ContextMenu>
        </contextMenu>
    </Button>
</AnchorPane>
```

![menu](https://zergqueen.gitee.io/images/factorio/menu.png)



#### `Tap`

> TapPane和Tap
>
> TapPane能监听selectTapId的变化

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import java.lang.*?>
<?import java.util.*?>
<?import javafx.scene.*?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<?import javafx.scene.image.ImageView?>
<?import javafx.scene.image.Image?>
<AnchorPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            fx:controller="club.xiaojiawei.controller.component.TapApplication"
            fx:id="anchorpane"
            prefHeight="400.0" prefWidth="600.0">
    <!--side设置tab的位置-->
    <!--rotateGraphic设置图片是否跟随旋转-->
    <!--tabClosingPolicy设置旗下的子tab是否允许关闭-->
    <TabPane prefWidth="${anchorpane.width}" side="RIGHT" rotateGraphic="true" tabClosingPolicy="UNAVAILABLE">
        <Tab text="狗贼">
            <graphic>
                <ImageView fitWidth="20" fitHeight="20">
                    <Image url="@../images/archlinux.png"/>
                </ImageView>
            </graphic>
            <Label>欢迎来到只会叠加挨打的狗贼直播间</Label>
        </Tab>
        <Tab text="异灵术" closable="false" onSelectionChanged="#onSelectionChanged">
            <Label>欢迎来到贴彩笔的戴便直播间</Label>
        </Tab>
        <Tab text="神棍" onClosed="#onClose" onCloseRequest="#onCloseRequest">
            <fx:include source="menu.fxml"/>
        </Tab>
    </TabPane>
</AnchorPane>
```

```java
package club.xiaojiawei.controller.component;

import javafx.event.Event;
import javafx.fxml.FXML;

/**
 * @author 肖嘉威
 * @date 2023/2/4 下午9:35
 */
public class TapApplication {

    @FXML
    protected void onClose(){
        System.out.println("onClose");
    }

    @FXML
    protected void onCloseRequest(Event event){
        System.out.println("onCloseRequest");
//        阻止tab关闭
        event.consume();
    }

    @FXML
    protected void onSelectionChanged(){
        System.out.println("onSelectionChanged");

    }
}
```

![tab](https://zergqueen.gitee.io/images/factorio/tab.png)



#### `CheckBox`和`RadioButton`

> 多选和单选

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.AnchorPane?>
<?import javafx.scene.layout.HBox?>
<?import javafx.scene.layout.VBox?>
<AnchorPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            prefHeight="400.0" prefWidth="600.0">
    <fx:define>
        <ToggleGroup fx:id="toggleGroup"/>
        <Insets top="20" left="20" right="20" bottom="20" fx:id="padding"/>
    </fx:define>
    <VBox>
        <HBox spacing="20" padding="$padding">
            <RadioButton toggleGroup="$toggleGroup" text="五月天"/>
            <RadioButton toggleGroup="$toggleGroup" text="孙燕姿" selected="true"/>
            <RadioButton toggleGroup="$toggleGroup" text="朴树" />
        </HBox>
        <HBox spacing="20" padding="$padding">
            <!--indeterminate设置不确定状态，一次性的，只有初始化时有-->
            <CheckBox text="白夜行" indeterminate="true"/>
            <CheckBox text="太空漫游" selected="true"/>
            <!--allowIndeterminate设置不确定状态，可重用-->
            <CheckBox text="惨败" allowIndeterminate="true"/>
        </HBox>
    </VBox>
</AnchorPane>
```

![rationbuttonandcheckbox](https://zergqueen.gitee.io/images/factorio/rationbuttonandcheckbox.png)



#### `TextArea`

> 文本框

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import java.lang.*?>
<?import java.util.*?>
<?import javafx.scene.*?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.text.Font?>
<AnchorPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            padding="$padding"
            prefHeight="400.0" prefWidth="400.0">
    <fx:define>
        <Insets top="20" left="20" right="20" bottom="20" fx:id="padding"/>
    </fx:define>
    <VBox padding="$padding">
        <!--wrapText是否自动换行-->
        <!--prefColumnCount和prefRowCount设置几行几列-->
        <!--nodeOrientation设置文本方向-->
        <TextArea wrapText="true" prefRowCount="3" prefColumnCount="10" nodeOrientation="LEFT_TO_RIGHT" >
            <font>
                <Font size="20"/>
            </font>
        </TextArea>
    </VBox>
</AnchorPane>
```

![textarea](https://zergqueen.gitee.io/images/factorio/textarea.png)

设置过滤器

```java
TextArea textArea = new TextArea();
textArea.setTextFormatter(new TextFormatter<>(change -> {
    if (change.getText() != null && change.getText().matches("[0-9]*")){
        return change;
    }
    return null;
}));
```



#### `ButtonBar`

> 统一旗下的Button样式

```xml
<ButtonBar>
    <buttons>
        <Button text="No" ButtonBar.buttonData="APPLY"/>
        <Button text="Yes" ButtonBar.buttonData="YES"/>
    </buttons>
</ButtonBar>
```



#### `ChoiceBox`

> 下拉列表，两种写法
>
> ChoiceBox只能检测到子元素的引用改变，而子元素内部属性的改变监听不到。
>
> 所以如果要修改的话有两种方法不会出问题
>
> 1. 将原来的子元素删除然后再添加
>
>    ```java
>    Student student = choice.getItems().get(1);
>    student.setSimpleName("kate");
>    choice.getItems().set(1, student);
>    ```
>
> 2. 对象属性使用SimpleStringProperty、SimpleIntegerProperty等类型代替8中基本类型和String，[看这里](###监听器)

```xml
<!--通过value设置默认选择-->
<ChoiceBox value="male" >
    <items>
        <FXCollections fx:factory="observableArrayList">
            <String fx:value="male" />
            <String fx:value="female" />
            <String fx:value="unknown" />
        </FXCollections>
    </items>
</ChoiceBox>

<!--通过value设置默认选择-->
<ChoiceBox>
    <Student fx:factory="createStudent"/>
    <Student fx:constant="GOOD_STUDENT"/>
    <String fx:value="male" />
    <Separator />
    <String fx:value="female" />
    <Separator />
    <String fx:value="unknown" />
</ChoiceBox>
```

![choicebox](https://zergqueen.gitee.io/images/myblog/javafx/choicebox.png)



#### `ComboBox`

> 和ChoiceBox大致相同，但功能更加丰富。

```xml
<AnchorPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            fx:controller="club.xiaojiawei.controller.demo.ComboBoxController"
            prefHeight="400.0" prefWidth="600.0">
    <!--editable表示是否可编辑-->
    <!--visibleRowCount表示一次显示几行-->
    <ComboBox editable="true" fx:id="comboBox" promptText="请输入你的爱好" visibleRowCount="2">
        <!--当没有其他内容是显示placeholder里的内容-->
        <placeholder>
            <Label>空空如也</Label>
        </placeholder>
        <items>
            <FXCollections fx:factory="observableArrayList">
                <String fx:value="good"/>
                <String fx:value="bad"/>
                <String fx:value="goods"/>
            </FXCollections>
        </items>
    </ComboBox>
</AnchorPane>
```

![comboboxdemo](https://zergqueen.gitee.io/images/myblog/javafx/comboboxdemo.png)



#### `ColorPicker`和`DatePicker`

> 颜色选择器和日期选择器



#### `Pagination`

> 分页器

```xml
<!--pageCount总页数，currentPageIndex当前页的索引，maxPageIndicatorCount显示几个，bullet自带的样式类-->
<Pagination pageCount="10" currentPageIndex="3" maxPageIndicatorCount="4" style="-fx-background-color: #CCCCFF;-fx-pref-width: 200;-fx-pref-height: 200">
    <pageFactory>
        <PaginationCallback/>
    </pageFactory>
</Pagination>
```

```java
public class PaginationCallback implements Callback<Integer, Node> {
    @Override
    public Node call(Integer param) {
        System.out.println(param);
        VBox vBox = new VBox();
        vBox.setSpacing(20);
        vBox.setAlignment(Pos.CENTER);
        switch (param){
            case 0 -> {
                vBox.getChildren().add(new Label("孙燕姿"));
                vBox.getChildren().add(new Label("朴树"));
                vBox.getChildren().add(new Label("五月天"));
            }
            case 1 -> {
                vBox.getChildren().add(new Label("许嵩"));
                vBox.getChildren().add(new Label("FIR"));
                vBox.getChildren().add(new Label("梁咏琪"));
            }
            default -> {
                vBox.getChildren().add(new Label("逃跑计划"));
                vBox.getChildren().add(new Label("蔡依林"));
                vBox.getChildren().add(new Label("梁咏琪"));
            }
        }
        return vBox;
    }
}
```

![pagination](https://zergqueen.gitee.io/images/myblog/javafx/pagination.png)



#### `Slider`

> 滑块

```xml
<Label text="0" fx:id="label">
    <graphic >
        <!--showTickLabels显示数字，showTickMarks显示刻度，majorTickUnit设置刻度间隔值-->
        <Slider showTickLabels="true" showTickMarks="true" majorTickUnit="20" value="10" fx:id="slider" prefWidth="300"/>
    </graphic>
</Label>
```

```java
public class TestDemoController implements Initializable {

    @FXML
    private Slider slider;

    @FXML
    private Label label;

    @Override
    public void initialize(URL url, ResourceBundle resourceBundle) {
//        设置刻度数值转换器
        slider.setLabelFormatter(new StringConverter<>() {
            @Override
            public String toString(Double object) {
                return object.intValue() + "￥";
            }

            @Override
            public Double fromString(String string) {
                return null;
            }
        });
//        绑定
        label.textProperty().bindBidirectional(slider.valueProperty(), new StringConverter<>() {
            @Override
            public String toString(Number object) {
                return object.intValue() + "";
            }

            @Override
            public Number fromString(String string) {
                return null;
            }
        });
//        监听值改变
        slider.valueProperty().addListener((observable, oldValue, newValue) -> {
            System.out.println("newValue:" + newValue);
        });
    }
}
```

![slider](https://zergqueen.gitee.io/images/myblog/javafx/slider.png)



#### `ProgressBar`和`ProgressIndicator`

> 进度指示器

```xml
<!--progress进度-->
<ProgressBar progress="0.2" prefWidth="200" />
<ProgressIndicator maxWidth="30"/>
```

![progressbar](https://zergqueen.gitee.io/images/myblog/javafx/progressbar.png)



#### `ScrollBar`

> 滚动条

```xml
<!--unitIncrement每次增加或减少的值-->
<ScrollBar unitIncrement="10" maxWidth="150" />
```



#### `ListView`

> 列表显示
>
> 设置成可编辑状态时需要传入cellFactory
>
> 当listView里的对象的属性发生改变时，可以通过**listView.refresh()**方法刷新

```xml
<AnchorPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            prefHeight="400.0" prefWidth="600.0">
    <ListView prefHeight="100">
        <HBox><Label text="hello"/></HBox>
        <HBox><Label text="hello"/></HBox>
        <HBox><Label text="hello"/></HBox>
        <HBox><Label text="hello"/></HBox>
        <HBox><Label text="hello"/></HBox>
        <HBox><Label text="hello"/></HBox>
    </ListView>
</AnchorPane>
```

![listview](https://zergqueen.gitee.io/images/myblog/javafx/listview.png)

使其可编辑，四种自带的ListCell使用，[ListCell介绍点这](###ListCell)

```java
ObservableList<String> list = FXCollections.observableArrayList();
list.add("hello");
list.add("world");
ListView<String> objectListView = new ListView<>(list);
objectListView.setEditable(true);

//方法一：直接编辑的方式，TextFieldListCell.forListView()将允许ListView可编辑
objectListView.setCellFactory(TextFieldListCell.forListView(new StringConverter<>() {
    @Override
    public String toString(String object) {
        return object + 250;
    }

    @Override
    public String fromString(String string) {
        return string + 360;
    }
}));
//方法二：下拉列表选择的方法
//        ObservableList<String> objects = FXCollections.observableArrayList();
//        for (String s : list) {
//            objects.add(s);
//        }
//        objectListView.setCellFactory(ComboBoxListCell.forListView(new StringConverter<String>() {
//            @Override
//            public String toString(String object) {
//                return object;
//            }
//
//            @Override
//            public String fromString(String string) {
//                return string + "||";
//            }
//        }, objects));
//方法三：ChoiceBoxListCell.forListView()和方法而差不多
//方法四：多选框
//        objectListView.setCellFactory(CheckBoxListCell.forListView(param -> new SimpleBooleanProperty(param.startsWith("h")), new StringConverter<String>() {
//            @Override
//            public String toString(String object) {
//                return null;
//            }
//
//            @Override
//            public String fromString(String string) {
//                return null;
//            }
//        }));

//如果还要添加提交监听的话，不要用setOnEditCommit()方法（会导致编辑失败），用下面这种方式
objectListView.addEventHandler(ListView.EditEvent.ANY, event -> {
    if (event.getEventType().getName().equals("EDIT_COMMIT")){
        System.out.println(11111);
    }
});
```



### 特性

#### 表达式绑定

> 当TextField的text发生改变时Label的text也会跟着改变

```xml
<TextField fx:id="textField"/>
<Label text="${textField.text}"/>
```

<center>常量和运算符表</center>

| **常量/运算符**    | **描述**                                              |
| ------------------ | ----------------------------------------------------- |
| "string" 'string'  | 字符串常量                                            |
| true false         | 布尔常量                                              |
| null               | 表示空值的常量                                        |
| 50.0 3e5 42        | 数值常数                                              |
| - (unary operator) | 一元减号运算符，应用于数字                            |
| ! (unary operator) | 布尔值的一元否定                                      |
| + - * / %          | 数字二元运算符                                        |
| && \|\|            | 布尔二元运算符                                        |
| > >= < <= == !=    | 比较的二元运算符。<br/>两个参数的类型都必须是“可比较” |



#### 添加快捷键

```xml
<accelerator>
    <!--按ctrl+H-->
    <KeyCodeCombination alt="UP" code="H" control="DOWN" meta="UP"
                        shift="UP" shortcut="UP"/>
</accelerator>
```



#### 创建对象

> 使用**\<fx:define\>\</fx:define\>**，标签里面就是要创建的对象，设置好id，就可以被引用了

```xml
<fx:define>
    <ToggleGroup fx:id="toggleGroup1"/>
    <ToggleGroup fx:id="toggleGroup2"/>
</fx:define>
```



#### FXML监听器

- onAction

  > 监听节点上鼠标点击事件

- onMenuValidation

  > 监听快捷键事件

- onContextMenuRequested

  > 监听节点上右键点击事件



#### FX:系列

##### 属性

- **fx:id**

  > 设置控件的id，如果controler里有对应的变量，则将该控件的对象赋值过去

  ```xml
  <Button fx:id="button1" />
  ```

  ```java
  public class Controller{
      @FXML
      private Button button1;    
  }
  ```

- **fx:constant**

  > 获取该控件类的常量成为该控件，常量类型和控件类型可以不一样

  ```xml
  <Student fx:constant="GOOD_STUDENT"/>
  ```

  ```java
  public class Student {
      public static final String GOOD_STUDENT = "李四";
  }
  ```

- **fx:value**

  > 调用该控件类的静态valueOf方法，并将返回值变成该控件，返回值类型和控件类型可以不一样

  ```xml
  <Student fx:value="李四"/>
  ```

  ```java
  public class Student {
      public void setName(String name) {
          this.name = name;
      }
      public static Student valueOf(String name){
          Student student = new Student();
          student.setName(name);
          return student;
      }
  }
  ```

  

- **fx:factory**

  > 调用该控件类的指定静态方法，并将返回值变成该控件，返回值类型和控件类型可以不一样

  ```xml
  <Student fx:factory="createStudent"/>
  ```

  ```java
  public class Student {
  	public void setName(String name) {
          this.name = name;
      }
      public static Student createStudent(){
          Student student = new Student();
          student.setName("李四");
          return student;
      }
  }
  ```

  

- **fx:controller**

  > 绑定controller类，一个FXML文件只能绑定一次，且只能在根节点上绑定

  ```xml
  <AnchorPane fx:controller="club.xiaojiawei.controller.component.TextAreaController"></AnchorPane>
  ```

  ```java
  package club.xiaojiawei.controller.component;
  public class TextAreaController{
  }
  ```

  

##### 控件

- **`fx:define`**

  > 创建对象，以下示例创建了一个Insets对象，其他控件可以通过引用fx:id来使用它

  ```xml
  <fx:define>
      <Insets top="20" left="20" right="20" bottom="20" fx:id="padding"/>
  </fx:define>
  ```

  

- **`x:copy`**

  > 创建现有元素的副本，源类型必须定义一个复制构造函数，用于从源值构造副本。
  >
  > 目前，没有任何 JavaFX 平台类提供这样的复制构造函数，因此此元素主要供应用程序开发人员使用。这可能会在未来的版本中改变。

  

- **`fx:include`**

  > 引用其他FXML文件

  ```xml
  <Tab text="神棍" onClosed="#onClose" onCloseRequest="#onCloseRequest">
      <fx:include source="menu.fxml"/>
  </Tab>
  ```

  ![fxinclude](https://zergqueen.gitee.io/images/myblog/javafx/fxinclude.png)

  

- **`fx:script`**

  > 允许使用脚本语言嵌入FXML中，但我没有成功

  

- **`fx:reference`**

  > 对现有元素的新引用

  ```xml
  <fx:define>
      <ImageView fx:id="image">
          <Image  url="@../images/archlinux.png"/>
      </ImageView>
  </fx:define>
  <fx:reference source="image"/>
  ```



#### 省略控件

> 当父控件类上有这个注解@DefaultProperty("items")时，表示可以省略括号里的控件

```xml
<ChoiceBox layoutX="40" layoutY="30" BorderPane.alignment="TOP_RIGHT" fx:id="choice">
    <Student fx:value="zerg"/>
    <Student fx:value="queen"/>
    <Student fx:value="jack"/>
</ChoiceBox>
<ComboBox editable="true">
    <items>
        <FXCollections fx:factory="observableArrayList">
            <String fx:value="good"/>
            <String fx:value="bad"/>
        </FXCollections>
    </items>
</ComboBox>
```



## 五、Demo

### Login界面

![logindemo](https://zergqueen.gitee.io/images/myblog/javafx/logindemo.png)

- FXML

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  
  <?import javafx.scene.control.*?>
  <?import javafx.scene.Group?>
  <?import javafx.scene.layout.*?>
  <?import javafx.scene.text.Font?>
  <?import javafx.scene.text.Text?>
  <BorderPane xmlns="http://javafx.com/javafx"
              xmlns:fx="http://javafx.com/fxml"
              fx:controller="club.xiaojiawei.controller.demo.LoginController"
              prefHeight="600.0" prefWidth="400.0">
      <background>
          <Background>
              <fills>
                  <BackgroundFill fill="beige"/>
              </fills>
          </Background>
      </background>
      <center>
          <Group BorderPane.alignment="CENTER">
              <GridPane vgap="20" hgap="20" >
                  <BorderPane GridPane.columnSpan="2">
                      <center>
                          <Text fill="forestgreen">
                              <font>
                                  <Font size="30">
                                  </Font>
                              </font>
                              豆瓣
                          </Text>
                      </center>
                  </BorderPane>
                  <Label GridPane.rowIndex="3" GridPane.columnIndex="0">
                      账号：
                  </Label>
                  <TextField fx:id="account" GridPane.rowIndex="3" GridPane.columnIndex="1" promptText="请输入邮箱或手机号" prefWidth="150"/>
                  <Label GridPane.rowIndex="4" GridPane.columnIndex="0">
                      密码：
                  </Label>
                  <PasswordField fx:id="password" GridPane.rowIndex="4" GridPane.columnIndex="1" promptText="请输入8位密码"/>
                  <Text GridPane.rowIndex="5" GridPane.columnIndex="1" textAlignment="CENTER" fx:id="tip"/>
                  <BorderPane GridPane.rowIndex="6" GridPane.columnSpan="2">
                      <center>
                          <Button onAction="#onLoginButtonCLick">
                              登录
                          </Button>
                      </center>
                  </BorderPane>
              </GridPane>
          </Group>
      </center>
  
  </BorderPane>
  ```

- Controller

  ```java
  package club.xiaojiawei.controller.demo;
  
  import club.xiaojiawei.LoginApplication;
  import javafx.fxml.FXML;
  import javafx.scene.Group;
  import javafx.scene.Scene;
  import javafx.scene.control.PasswordField;
  import javafx.scene.control.TextField;
  import javafx.scene.paint.Paint;
  import javafx.scene.text.Text;
  import javafx.stage.Stage;
  
  /**
   * @author 肖嘉威
   * @date 2023/2/4 下午2:36
   */
  public class LoginController {
  
      @FXML
      private TextField account;
  
      @FXML
      private PasswordField password;
  
      @FXML
      private Text tip;
  
      @FXML
      protected void onLoginButtonCLick(){
          if ("root".equals(account.getText()) && "00000000".equals(password.getText())){
              tip.setText("登录成功");
              tip.setFill(Paint.valueOf("#339933"));
              System.out.println("登录成功");
              LoginApplication.stage.close();
              Stage stage = new Stage();
              stage.setScene(new Scene(new Group(), 600, 600));
              stage.setTitle("主界面");
              stage.show();
          }else {
              account.setText("");
              password.setText("");
              tip.setText("账号或密码错误");
              tip.setFill(Paint.valueOf("#CC3333"));
              System.out.println("登录失败");
          }
      }
  }
  ```

- Application

  ```java
  package club.xiaojiawei;
  
  import javafx.application.Application;
  import javafx.fxml.FXMLLoader;
  import javafx.scene.Scene;
  import javafx.scene.image.Image;
  import javafx.stage.Stage;
  
  import java.util.Objects;
  
  /**
   * @author 肖嘉威
   * @date 2023/2/4 下午2:38
   */
  public class LoginApplication extends Application {
  
      public static Stage stage;
  
      @Override
      public void start(Stage primaryStage) throws Exception {
          FXMLLoader fxmlLoader = new FXMLLoader(LayoutApplication.class.getResource("demo/login.fxml"));
          Scene scene = new Scene(fxmlLoader.load(), 400, 600);
          primaryStage.setMinWidth(300);
          primaryStage.setMinHeight(400);
          primaryStage.getIcons().add(new Image(Objects.requireNonNull(getClass().getResourceAsStream("images/douban.png"))));
          primaryStage.setTitle("豆瓣登录");
          primaryStage.setScene(scene);
          primaryStage.show();
          stage = primaryStage;
      }
  
      public static void main(String[] args) {
          LoginApplication.launch();
      }
  
  }
  ```



### 鼠标拖拽

- FXML

  ```xml
  <AnchorPane xmlns="http://javafx.com/javafx"
              xmlns:fx="http://javafx.com/fxml"
              fx:controller="club.xiaojiawei.controller.demo.DragDemoController"
              prefHeight="400.0" prefWidth="600.0">
      <ImageView fitWidth="100" preserveRatio="true" onMouseDragged="#onMouseDragged" onMousePressed="#onMousePressed" fx:id="imageView">
          <cursor>
              <ImageCursor fx:constant="MOVE"/>
          </cursor>
          <Image url="@../images/people.jpg"/>
      </ImageView>
  </AnchorPane>
  ```

- Controller

  ```java
  public class DragDemoController {
      @FXML
      private ImageView imageView;
  
      private double x, y;
      @FXML
      protected void onMousePressed(MouseEvent event){
          x = event.getX();
          y = event.getY();
      }
      @FXML
      protected void onMouseDragged(MouseEvent event){
          imageView.setLayoutX(event.getSceneX() - x);
          imageView.setLayoutY(event.getSceneY() - y);
      }
  }
  ```



### 拖拽文件

![dragfile](https://zergqueen.gitee.io/images/myblog/javafx/dragfile.png)

- FXML

  ```xml
  <BorderPane xmlns="http://javafx.com/javafx"
              xmlns:fx="http://javafx.com/fxml"
              fx:controller="club.xiaojiawei.controller.demo.DragFileController"
              stylesheets="@../css/dragFile.css"
              prefHeight="400.0" prefWidth="600.0">
      <center>
          <Label styleClass="drag_label"  fx:id="label" text="拖拽文件至此" contentDisplay="TOP"  onDragOver="#onDragOver" onDragDropped="#onDragDropped" onDragEntered="#onDragEntered" onDragExited="#onDragExited">
              <graphic>
                  <ImageView>
                      <Image  url="@../images/add.png"/>
                  </ImageView>
              </graphic>
          </Label>
      </center>
      <top>
          <ImageView fitWidth="100" fitHeight="100" onDragDetected="#onDragDetected" fx:id="imageView" onDragDone="#onDragDone">
              <Image url="@../images/douban.png"/>
          </ImageView>
      </top>
  </BorderPane>
  ```

- Controller

  ```java
  public class DragFileController {
  
      @FXML
      protected void onDragOver(DragEvent event){
          if (event.getGestureSource() != label) {
  //            设置传输模式
              event.acceptTransferModes(TransferMode.ANY);
          }
      }
  
      @FXML
      private Label label;
  
      @FXML
      protected void onDragDropped(DragEvent event){
          Dragboard db = event.getDragboard();
          boolean success = false;
          if (db.hasFiles()) {
              StringBuilder stringBuilder = new StringBuilder();
              for (File file : db.getFiles()) {
                  stringBuilder.append(file.getName());
                  stringBuilder.append("\n");
              }
              label.setText(stringBuilder.toString());
              success = true;
          }else if (db.hasString() && db.getString().matches("^https|http.*")){
              Image image = new Image(db.getString());
              ImageView imageView1 = new ImageView(image);
              imageView1.setFitWidth(220);
              imageView1.setPreserveRatio(true);
              label.setGraphic(imageView1);
          }
  //        表示已经完成传输
          event.setDropCompleted(success);
      }
  
      @FXML
      private ImageView imageView;
      @FXML
      protected void onDragDetected(MouseEvent event){
          Dragboard dragboard = imageView.startDragAndDrop(TransferMode.COPY_OR_MOVE);
          ClipboardContent clipboardContent = new ClipboardContent();
          clipboardContent.putFiles(List.of(new File("/home/zerg/Desktop/javafx笔记.md")));
          WritableImage writableImage = new WritableImage(100, 100);
  //        截图
          imageView.snapshot(new SnapshotParameters(), writableImage);
          clipboardContent.putImage(writableImage);
          dragboard.setContent(clipboardContent);
      }
  
      @FXML
      protected void onDragDone(DragEvent event){
          if (event.getTransferMode() == TransferMode.COPY || event.getTransferMode() == TransferMode.MOVE){
              imageView.setImage(null);
          }
      }
  
      @FXML
      protected void onDragEntered(){
          label.getStyleClass().add("focus_drag");
      }
  
      @FXML
      protected void onDragExited(){
          label.getStyleClass().remove("focus_drag");
      }
  }
  ```

- CSS

  ```css
  .drag_label{
      -fx-font-size: 18;
      -fx-border-width: 3;
      -fx-border-insets: -90;
      -fx-border-radius: 15;
      -fx-cursor: hand;
      -fx-border-color: gray;
  }
  .focus_drag{
      -fx-border-color: #EB7171!important;
  }
  ```




### 拖拽排序

![dragsort](https://zergqueen.gitee.io/images/myblog/javafx/dragsort.png)

- Application

```java
public class ListCellApplication extends Application {

    @Override
    public void start(Stage primaryStage) throws Exception {
//        FXMLLoader fxmlLoader = new FXMLLoader(getClass().getResource("/club/xiaojiawei/.fxml"));
        AnchorPane anchorPane = new AnchorPane();
        ListView<String> listView = new ListView<>();
        anchorPane.getChildren().add(listView);
        for (int i = 0; i < 4; i++) {
            listView.getItems().add("cat" + (4 - i));
        }
        listView.setEditable(true);
        listView.setCellFactory(param -> {
            ListCell<String> cell = new ListCell<>() {

                @Override
                public void startEdit() {
                    super.startEdit();
                    System.out.println("startEdit");
                    if (this.getGraphic() instanceof HBox) {
                        HBox hBox = (HBox) this.getGraphic();
                        ObservableList<Node> children = hBox.getChildren();
                        for (int i = 0; i < children.size(); i++) {
                            if (children.get(i) instanceof Text) {
                                TextField textField = new TextField(((Text) children.get(i)).getText());
                                children.set(i, textField);
                                ListCell<String> listCell = this;
                                textField.setOnKeyPressed(event -> {
                                    if (event.getCode() == KeyCode.ENTER) {
                                        listCell.commitEdit(textField.getText());
                                        textField.setOnKeyPressed(null);
                                    }
                                });
                            }
                        }
                    }

                }

                @Override
                public void commitEdit(String newValue) {
                    super.commitEdit(newValue);
                    System.out.println("commitEdit");
                }

                @Override
                public void cancelEdit() {
                    super.cancelEdit();
                    System.out.println("cancelEdit");
                }

                @Override
                protected void updateItem(String item, boolean empty) {
                    super.updateItem(item, empty);
                    if (!empty) {
                        HBox hBox = new HBox();
                        hBox.setSpacing(20);
                        ObservableList<Node> children = hBox.getChildren();
                        ImageView imageView = new ImageView(new Image(getClass().getResource("images/douban.png").toExternalForm()));
                        imageView.setFitHeight(25);
                        imageView.setFitWidth(25);
                        children.add(imageView);
                        children.add(new Button("hello"));
                        Text text = new Text(item);
                        children.add(text);
                        hBox.hoverProperty().addListener((observable, oldValue, newValue) -> {
                            if (newValue) {
                                param.getSelectionModel().select(param.getItems().indexOf(item));
                            }
                        });
                        hBox.setOnMousePressed(event -> hBox.setCursor(Cursor.MOVE));
                        hBox.setOnMouseReleased(event -> hBox.setCursor(Cursor.DEFAULT));

                        hBox.setOnDragDetected(event -> {
                            Dragboard dragboard = hBox.startDragAndDrop(TransferMode.COPY_OR_MOVE);
                            ClipboardContent clipboardContent = new ClipboardContent();
                            clipboardContent.putImage(hBox.snapshot(new SnapshotParameters(), new WritableImage((int) hBox.getWidth(), (int) hBox.getHeight())));
                            clipboardContent.putString(item);
                            dragboard.setContent(clipboardContent);

                        });

                        hBox.setOnDragOver(event -> event.acceptTransferModes(TransferMode.ANY));
                        hBox.setOnDragDropped(event -> {
                            Dragboard dragboard = event.getDragboard();
                            ObservableList<String> items = param.getItems();
                            int startIndex = items.indexOf(dragboard.getString());
                            int endIndex = items.indexOf(item);
                            items.remove(startIndex);
                            items.add(endIndex, dragboard.getString());
                            event.setDropCompleted(true);
                        });
                        this.setGraphic(hBox);
                    }
                }
            };
            return cell;
        });

        Scene scene = new Scene(anchorPane, 400, 600);
        primaryStage.setTitle("demo");
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    public static void main(String[] args) {
        Application.launch();
    }
}
```

## 五、CSS

## 六、打包
