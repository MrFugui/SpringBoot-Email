## 啦啦啦啦啦，富贵同学又开始开坑了，出了个免费的专栏，主要给大家从0基础开始用springBoot集成第三方的插件或者功能，如果这篇专栏能办到你，一定不要忘了点一个赞哦！！欢迎大家收藏分享
![在这里插入图片描述](https://img-blog.csdnimg.cn/385dc942abfc4a019d845055328814c1.png#pic_center)

## 还是老规矩，第一步，导jar包

```sql
	   <!--Email-->
        <dependency>
            <groupId>com.sun.mail</groupId>
            <artifactId>javax.mail</artifactId>
            <version>1.6.2</version>
        </dependency>
        <!--Email-->
```
## 第二步，申请email授权密码
这里以163邮箱举例：
![在这里插入图片描述](https://img-blog.csdnimg.cn/0d02528d8cc74c488285499ec425f12f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5o6J5aS05Y-R55qE546L5a-M6LS1,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/ed204d73f524492b94483411fb15616e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5o6J5aS05Y-R55qE546L5a-M6LS1,size_19,color_FFFFFF,t_70,g_se,x_16)
## 第三步，编写yml文件

```sql
spring：
  mail: 
    host: smtp.163.com  #固定写死的 163邮箱
    username: masiyi163163@163.com #刚刚生成授权码的邮箱
    password: NOTZSJISFIOOWDLNY #刚刚生成的授权码
    default-encoding: UTF-8
```
## 第四步，编写工具类

```sql

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.messaging.MessagingException;
import org.springframework.stereotype.Component;

import javax.mail.internet.MimeMessage;
import java.io.File;

/**
 * @author MaSiyi
 * @version 1.0.0 2021/11/20
 * @since JDK 1.8.0
 */
@Component
public class EmailUtil {

    @Autowired
    private JavaMailSender javaMailSender;

    @Value("${spring.mail.username}")
    private String from;

    /**
     * 简单文本邮件
     * @param to 接收者邮件
     * @param subject 邮件主题
     * @param content 邮件内容
     */
    public void sendSimpleMail(String to, String subject, String content){

        SimpleMailMessage message = new SimpleMailMessage();
        message.setTo(to);
        message.setSubject(subject);
        message.setText(content);
        message.setFrom(from);

        javaMailSender.send(message);
    }

    /**
     * 附件邮件
     * @param to 接收者邮件
     * @param subject 邮件主题
     * @param content HTML内容
     * @param filePath 附件路径
     * @throws MessagingException
     */
    public void sendAttachmentsMail(String to, String subject, String content,
                                    String filePath) throws MessagingException {


        try {
            MimeMessage mimeMessage = javaMailSender.createMimeMessage();
            MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content, true);
            helper.setFrom(from);

            FileSystemResource file = new FileSystemResource(new File(filePath));
            String fileName = file.getFilename();
            helper.addAttachment(fileName, file);

            javaMailSender.send(mimeMessage);
        } catch (javax.mail.MessagingException e) {
            e.printStackTrace();
        }
    }

}

```
## 第五步，编写controller类

```sql

import com.wangfugui.apprentice.common.util.EmailUtil;
import com.wangfugui.apprentice.common.util.ResponseUtils;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author MaSiyi
 * @version 1.0.0 2021/11/20
 * @since JDK 1.8.0
 */
@Api(tags = "邮件服务")
@RestController
@RequestMapping("/email")
public class EmailController {
    @Autowired
    private EmailUtil emailUtil;

    @GetMapping("/sendSimpleMail")
    @ApiOperation("发送简单邮件")
    public ResponseUtils sendSimpleMail(String email,String subject,String content) {
        emailUtil.sendSimpleMail(email,subject,content);
        return ResponseUtils.success();
    }
    @GetMapping("/sendAttachmentsMail")
    @ApiOperation("发送附件邮件")
    public ResponseUtils sendAttachmentsMail(String email,String subject,String content,String filePath) {
        emailUtil.sendAttachmentsMail(email, subject, content, filePath);
        return ResponseUtils.success();
    }
}

```
## 第六步，测试
![在这里插入图片描述](https://img-blog.csdnimg.cn/8fcfcd8fcad746579c1262b32508cf53.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5o6J5aS05Y-R55qE546L5a-M6LS1,size_20,color_FFFFFF,t_70,g_se,x_16)
查看邮箱
![在这里插入图片描述](https://img-blog.csdnimg.cn/ec4422ff5e3e4c2faca9412cc2b609ff.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5o6J5aS05Y-R55qE546L5a-M6LS1,size_20,color_FFFFFF,t_70,g_se,x_16)

成功！！
好了，就是这么的简单，完整代码请移至[SpringBoot+Email](https://gitee.com/WangFuGui-Ma/spring-boot-email)查看
![在这里插入图片描述](https://img-blog.csdnimg.cn/a866736dfb41420f8d8a8484d1e9abb7.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5o6J5aS05Y-R55qE546L5a-M6LS1,size_10,color_FFFFFF,t_70,g_se,x_16#pic_center)
