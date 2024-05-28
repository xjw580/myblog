1. 创建JavaFX启动类

   ```java
   package club.xiaojiawei;
   
   import javafx.application.Application;
   import javafx.fxml.FXMLLoader;
   import javafx.stage.Stage;
   import org.springframework.boot.builder.SpringApplicationBuilder;
   import org.springframework.context.ConfigurableApplicationContext;
   import org.springframework.stereotype.Component;
   
   @Component
   public class JavaFXApplication extends Application {
   
       @Override
       public void start(Stage stage) throws Exception {
   //        启动SpringBoot
           ConfigurableApplicationContext springContext = new SpringApplicationBuilder(ScriptApplication.class).headless(false).run();
   //        AutowireCapableBeanFactory接口可以集成其他框架，捆绑并填充并不由Spring管理生命周期并已存在的实例。autowireBean()方法实现属性的依赖注入。
           springContext.getAutowireCapableBeanFactory().autowireBean(this);
           FXMLLoader fxmlLoader = new FXMLLoader(getClass().getResource("javaFX.fxml"));
   //        注入依赖项
           fxmlLoader.setControllerFactory(springContext::getBean);
       }
   }
   ```

   

2. 创建SpringBoot启动类

   ```java
   package club.xiaojiawei;
   
   import javafx.application.Application;
   import lombok.extern.slf4j.Slf4j;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.boot.web.servlet.ServletComponentScan;
   import org.springframework.scheduling.annotation.EnableScheduling;
   
   /**
    * @author 肖嘉威
    * @date 2022/11/24 13:34
    */
   @SpringBootApplication
   @EnableScheduling
   @ServletComponentScan
   @Slf4j
   public class ScriptApplication{
   
       public static void main(String[] args) {
           Application.launch(JavaFXApplication.class, args);
       }
   
   }
   ```

   

3. 这样就能在JavaFX控制器中实现依赖注入了

4. 之后再创建Stage时如果还需要在控制器中实现依赖注入还需要继续设置

   ```java
   fxmlLoader.setControllerFactory(context::getBean);
   ```

   

