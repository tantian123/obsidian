在我们现在使用的SpringBoot的版本中，底层发送邮件的技术都是使用一个叫 Jakarta Mail 的，它可有实现SMTP、POP、IMAP等基本的邮件发送和接收协议。

1.依赖和配置

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

```
application.properties设置基本配置
### smtp服务器主机（163的）
spring.mail.host=smtp.163.com
### 登录服务器邮箱账号
spring.mail.username=antladdie
### 登录服务器邮箱授权码（不是邮箱密码，这个是我们开通SMTP、POP时得到的授权码）
spring.mail.password=xxxxxxxxxxxxxxx
```


“设置”->“账户”->“POP3/IMAP/SMTP/Exchange/CardDAV/CalDAV服务”即可获得授权码

2.使用，简单邮件和复杂邮件

我们在业务里直接注入JavaMailSenderImpl后并调用send方法。其中简单的邮件我们可以通过**SimpleMailMessage**来发送，对于复杂的带有附件的我们可以借助**MimeMessageHelper**来构建**MimeMessage**发送邮件。


[SpringBoot集成邮件发送 - 蚂蚁小哥 - 博客园 (cnblogs.com)](https://www.cnblogs.com/antLaddie/p/15614345.html)




```java
public class EmailService {  
    @Autowired  
    JavaMailSenderImpl javaMailSender;  
  
    public void send(){  
        SimpleMailMessage message=new SimpleMailMessage();  
        message.setFrom("1151206092@qq.com");  
        message.setTo("1754624970@qq.com");  
        message.setText("test tt");  
        message.setSentDate(new Date());  
        javaMailSender.send(message);  
    }  
  
    public void send2() throws MessagingException {  
        MimeMessage mimeMessage = javaMailSender.createMimeMessage();  
        MimeMessageHelper messageHelper = new MimeMessageHelper(mimeMessage, true);  
        messageHelper.setFrom("1151206092@qq.com");  
        messageHelper.setTo("1754624970@qq.com");  
  
  
        FileSystemResource png = new FileSystemResource(new File("F:\\Capture001.png"));  
        messageHelper.addAttachment(Objects.requireNonNull(png.getFilename()), png);  
        messageHelper.setText("<h2 style='color:#f00;'>apex<img src='cid:p01' alt='' style='width:200px;height:50px;'></h2>", true);  
  
        javaMailSender.send(mimeMessage);  
    }  
  
  
}
```


