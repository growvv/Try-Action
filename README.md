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
## 

## 二、使用构件持久化工作流程数据
**构件是指在工作流程运行过程中产生的文件或文件集**，构件用于在作业完成后保存数据。

虽然不是直接下载到仓库，但是能将数据保存到Artifact(构建)，也算解决一部分需求吧。

每个 GitHub 帐户可获得一定数量的免费记录和存储，具体取决于帐户所使用的产品。详见[说明](https://help.github.com/cn/github/setting-up-and-managing-billing-and-payments-on-github/managing-billing-for-github-actions)

您必须在工作流程运行时上传构件。 GitHub 提供两项可用于上传和下载构建构件的操作。 上传到工作流程运行的文件将使用 <code>.zip</code> 格式存档。 更多信息请参阅 [action/upload-artifact](https://github.com/marketplace/actions/upload-artifact) 和 [download-artifact](https://github.com/marketplace/actions/download-artifact) 操作。

### 1. 上传构建
这些是您可能想要上传的一些常见构建和测试输出构件：
- 日志文件和核心转储文件
- 测试结果、失败和屏幕截图
- 二进制或存档文件
- 压力测试性能输出和代码覆盖结果

注意两点：
1. 上传存档时，必须对其命名。
2. 上传文件后，您可以在同一工作流运行中的另一个作业中通过名称下载它。

### 2. 下载构建
有两种用途：
1. 读取其他job上传的构建，用于自身job
2. 通过页面下载

讲一下第二种：
在Actions，选中具体的job，下拉Artifacts就能看到你上传的构建，你可以选择下载或删除。
![](https://cdn.jsdelivr.net/gh/growvv/img/images/20200207180225.png)

### 3. 不同job之间共享数据
必须是在同一个工作流（workflow）中，不能跨工作流，而且需要用 <code>needs</code> 申明。

一个完整的例子如下：
```yml
name: Share data between jobs

on: [push]

jobs:
  job_1:
    name: Add 3 and 7
    runs-on: ubuntu-latest
    steps:
      - shell: bash
        run: |
          expr 3 + 7 > math-homework.txt
      - name: Upload math result for job 1
        uses: actions/upload-artifact@v1
        with:
          name: homework
          path: math-homework.txt

  job_2:
    name: Multiply by 9
    needs: job_1
    runs-on: windows-latest
    steps:
      - name: Download math result for job 1
        uses: actions/download-artifact@v1
        with:
          name: homework
      - shell: bash
        run: |
          value=`cat homework/math-homework.txt`
          expr $value \* 9 > homework/math-homework.txt
      - name: Upload math result for job 2
        uses: actions/upload-artifact@v1
        with:
          name: homework
          path: homework/math-homework.txt

  job_3:
    name: Display results
    needs: job_2
    runs-on: macOS-latest
    steps:
      - name: Download math result for job 2
        uses: actions/download-artifact@v1
        with:
          name: homework
      - name: Print the final result
        shell: bash
        run: |
          value=`cat homework/math-homework.txt`
          echo The result is $value
```

### 4. 有待改进
1. 还是买个云存储空间吧，利用其API来存储和检索这些构建。
2. 写个爬虫，将结果自动保存到云存储空间。

### 5. 参考链接
1. [GitHub帮助-使用构件持久化工作流程数据](https://help.github.com/cn/actions/automating-your-workflow-with-github-actions/persisting-workflow-data-using-artifacts)

## 
