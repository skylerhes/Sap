# Auto-SAP应用部署说明文档

## 概述

本项目是自动部署docker容器到SAP Cloud平台，适配企业版部署，企业版无需保活

### 前置要求
* GitHub 账户：需要有一个 GitHub 账户来创建仓库和设置工作流
* SAP Cloud 账户：需要有 SAP Cloud 有效账户,点此注册：https://www.sap.com

## 部署步骤

1. Fork本仓库

2. 在Actions菜单允许 `I understand my workflows, go ahead and enable them` 按钮

3. 在 GitHub 仓库中设置以下 secrets（Settings → Secrets and variables → Actions → New repository secret）：
- `EMAIL`: SAP(试用版或企业版)登录邮箱(必填)
- `PASSWORD`: SAP(试用版或企业版)登录密码(必填)

4. **设置Docker容器环境变量(也是在secrets里设置)**
   - 设置基础环境变量：
     - DOCKER_IMAGE(使用的docker镜像),默认使用n8n
     - MEMORY 设置内存大小，默认2048M
     - DISK  设置硬盘大小，默认3096M
     - RCLONE_CONF:rclone配置内容，可选，用来同步数据
     - ADMIN_PASSWORD:Code Server登陆密码

6. **开始部署**
* 试用版第二区域和企业版创建区域后,请一定要创建一个空间,名称随意,否则无法运行
* 在GitHub仓库的Actions页面找到"自动部署代理到SAP"工作流
* 点击"Run workflow"按钮
* 根据需要选择或填写以下参数：
   - type: 选择部署类型(Argo隧道CDN/ws直连/xhttp直连)默认Argo隧道
   - region: 选择部署区域（SG(free)和US(free)为试用版,其他为企业版，请选择和开设的平台对应,aws,gcp,azure）
   - app_name: (可选)指定应用名称,留空随机生成
* 点击绿色的"Run workflow"按钮开始部署

6. **获取节点信息**
* 点开运行的actions，点击`详细部署信息` 查看服务链接，访问域名


## 保活(选择其中一种即可)
### vps或NAT小鸡保活
- 推荐使用keep.sh在vps或nat小鸡上精准保活，下载keep.sh文件到本地或vps上，在开头添加必要的环境变量和保活url然后执行`bash keep.sh`即可
1. 下载文件到vps或本地
```bash
wget https://raw.githubusercontent.com/eooce/Auto-deploy-sap-and-keepalive/refs/heads/main/keep.sh && chmod +x keep.sh
```
2. 修改keep.sh开头4-8行中的变量和保活url
3. `bash keep.sh`运行即可


### Github Actions保活
* actions保活可能存在时间误差，建议根据前两天的情况进行适当调整`自动保活SAP.yml`里的cron时间


### Cloudflare workers保活
1. 登录你的Cloudflare账户 [Cloudflare Dashboard](https://dash.cloudflare.com)
2. 点击 `Workers and pages`创建一个workers，编辑代码，全选`_worker-keep.js`文件里的代码粘贴到workers中
3. 在开头添加登录email和登录密码(telegram通知配置可选)、项目URL和项目名称，右上角点击部署
4. 部署成功后返回到该worker设置中选择添加触发事件，添加cron触发器--cron表达式，设置为：`*/2 0 * * *` 保存，意思是北京时间早上8-9点每2分钟检查一次


## 注意事项

1. 确保所有必需的GitHub Secrets已正确配置
2. 多区域部署需先开通权限，确保US区域有内存
3. 试用版第二区域和企业版创建区域后,请一定要创建一个空间,名称随意,否则无法运行
4. 部署区域（SG(free)和US(free)为试用版,其他为企业版，请选择和开设的平台对应,aws,gcp,azure
