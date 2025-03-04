实现 CI/CD 环境下的 SSH 免密登录，主要涉及以下几个步骤：

**1. 生成 SSH 密钥对 (在 CI/CD 系统或安全的环境中生成)**

位置:  密钥对的生成应在安全的、可控的环境中进行，通常在 CI/CD 系统自身或者一个安全的管理机器上。不要在开发人员的本地机器上生成用于生产环境的密钥，因为私钥的安全性至关重要。

命令: 使用 ssh-keygen 命令生成密钥对。与普通 SSH 免密登录类似，但需要考虑密钥的存放和管理。
```
ssh-keygen -t rsa -b 4096 -N "" -f ./id_rsa_deploy
```

-t rsa: 指定密钥类型为 RSA (也可以选择 ed25519，更安全高效)。
-b 4096: 指定密钥长度为 4096 位 (RSA 密钥推荐 4096 位或以上，提高安全性)。
-N "": 设置为空密码短语 (passphrase)。在 CI/CD 自动化场景中，通常不设置 passphrase，因为自动化脚本无法交互式输入 passphrase。但请权衡安全性，如果你的 CI/CD 系统可以安全地管理 passphrase，设置 passphrase 会更安全。 如果选择不使用 passphrase，务必更加小心地保护私钥。
-f ./id_rsa_deploy: 指定私钥文件名为 id_rsa_deploy，并保存在当前目录。你可以自定义文件名和路径。
执行该命令后，会生成两个文件：

id_rsa_deploy (私钥): 极其重要，必须妥善保管，不要泄露。 这是 CI/CD 系统用来进行 SSH 认证的凭证。
id_rsa_deploy.pub (公钥): 需要部署到目标服务器。

**2.  在 CI/CD 系统中安全存储私钥**

不要硬编码在代码中:  绝对不要将私钥直接硬编码在 CI/CD 配置文件、脚本或者源代码仓库中。这是非常不安全的行为。

使用 CI/CD 系统的 Secrets 管理功能:  绝大多数现代 CI/CD 系统 (例如 Jenkins, GitLab CI, GitHub Actions, Azure DevOps Pipelines, CircleCI 等) 都提供了安全的 Secrets 或 Credentials 管理功能。你应该使用这些功能来存储私钥。

环境变量: 某些系统允许将 Secrets 设置为环境变量，在 CI/CD 任务运行时注入。但环境变量有时可能存在泄露风险 (例如日志输出)。
Secret Files: 更安全的方式是将私钥作为 Secret 文件上传到 CI/CD 系统，在任务运行时，CI/CD 系统会将私钥文件安全地挂载到任务运行环境中。
示例 (以 GitLab CI 为例):

在 GitLab CI 中，你可以在 "Settings" -> "CI/CD" -> "Variables" 中添加一个类型为 "File" 的变量，变量名为 DEPLOY_PRIVATE_KEY，并将 id_rsa_deploy 私钥文件的内容粘贴到 "Value" 字段中。

**3. 分发公钥到目标部署机器**

公钥需要被添加到所有目标部署机器的 ~/.ssh/authorized_keys 文件中。有多种方式可以实现：

手动复制 (适用于少量服务器，初始化配置):  最简单的方式是手动 SSH 登录到每台目标服务器，创建 .ssh 目录 (如果不存在)，并将公钥内容追加到 ~/.ssh/authorized_keys 文件中，并设置正确的权限 (chmod 700 ~/.ssh, chmod 600 ~/.ssh/authorized_keys)。

**4. 在 CI/CD Pipeline 中使用私钥进行 SSH 连接**

在 CI/CD 管道的部署任务中，需要使用之前存储在 Secrets 中的私钥进行 SSH 连接。具体的实现方式取决于你使用的 CI/CD 系统和部署工具。

脚本方式 (例如 Shell 脚本):  在 CI/CD 任务中执行 Shell 脚本，使用 ssh 命令进行连接。

示例 (GitLab CI .gitlab-ci.yml 文件):

```YAML

deploy_job:
  stage: deploy
  image: alpine/ssh  # 包含 ssh 命令的 Docker 镜像
  variables:
    DEPLOY_SERVER_IP: "your_deploy_server_ip"
    DEPLOY_USER: "deploy_user"
    DEPLOY_PATH: "/path/to/deploy/directory"
  script:
    - mkdir -p ~/.ssh
    - echo "$DEPLOY_PRIVATE_KEY" > ~/.ssh/id_rsa  # 将 Secret 变量写入私钥文件
    - chmod 600 ~/.ssh/id_rsa
    - ssh -o StrictHostKeyChecking=no -i ~/.ssh/id_rsa $DEPLOY_USER@$DEPLOY_SERVER_IP "
        cd $DEPLOY_PATH &&
        git pull origin main &&
        # 执行部署命令，例如重启服务等
        echo 'Deployment successful!'
      "
  environment:
    name: production
```