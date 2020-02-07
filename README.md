# Try-Action
github actions的一些简单尝试。

## 一、发送邮件
这是一个很有用的功能，能用来做每日天气、定时提醒、发送运行结果等，结合各种api可以实现许多有趣的功能。

虽然nodejs和python都有send-email库，但是用action实现岂不更美滋滋。

推荐[Send mail Github Action](https://github.com/marketplace/actions/send-email)。

例如，发送Build结果到邮箱：
```yml
- name: Send email
  uses: dawidd6/action-send-mail@v1.3.0
  with:
    # SMTP server address
    server_address: 
    # SMTP server port
    server_port: 
    # Authenticate as this user to SMTP server
    username: 
    # Authenticate with this password to SMTP server
    password: 
    # Subject of mail message
    subject: 
    # Body of mail message (might be a filename to read from)
    body: 
    # Recipients mail addresses (separated with comma)
    to: 
    # Full name of mail sender
    from: 
    # Content-Type HTTP header (MIME type)
    content_type: # optional, default is text/plain
```
![](https://cdn.jsdelivr.net/gh/growvv/img/images/20200205160432.jpg)

![](https://cdn.jsdelivr.net/gh/growvv/img/images/20200120155454.jpg)

## 
