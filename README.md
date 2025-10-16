# GS_SmartCare_for_KylinOS

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![KylinOS Support](https://img.shields.io/badge/KylinOS-V10--SP3-green.svg)](https://www.kylinos.cn/)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)

基于银河麒麟操作系统的智能运维管家工具，融合多模态诊断、时序预测、异构Multi-Agent协同等技术，实现故障超前预警、精准根因定位与自动化修复，为国产操作系统运维提供全链路智能解决方案。


## 项目介绍
在国产操作系统深化应用的背景下，传统运维面临多层级故障定位难、隐形风险滞后、人工成本高的痛点。GS_SmartCare_for_KylinOS（简称SmartCare）针对银河麒麟操作系统（Kylin-Server-V10-SP3），构建了“数据采集-监控预警-根因分析-自动化修复-安全验证”的闭环运维体系，核心能力包括：
- **实时监控与超前预警**：通过多尺度GRU模型（KylinPulse）预测未来5分钟系统指标，误差率≤0.65%，异常识别精确率达99.8%
- **全链路根因定位**：基于KPI树+拓扑图+Trace调用栈的“多维多因素根因分析链”，根因定位准确率较传统日志诊断提升42.9%
- **智能协同修复**：置信度驱动的异构多层Multi-Agent体系，修复方案生成准确率≥98.7%，支持自然语言交互（Shell指令转化率≥98.5%）
- **安全验证机制**：高安全低时延拟真沙盒（KylinSandbox Ⅱ型），脚本验证平均耗时6-10秒，较业界方案提速18%


## 核心特性
| 功能模块         | 关键能力                                                                 | 技术指标                          |
|------------------|--------------------------------------------------------------------------|-----------------------------------|
| 数据采集与可视化 | Telegraf/Logstash/BCC多源采集，Grafana仪表盘                              | 采样频率：1-10秒/次，日志日处理≥50万条 |
| 时序预警         | KylinPulse模型预测CPU/内存等指标趋势                                      | 预测误差≤0.65%，提前5分钟预警      |
| 根因分析         | 三位一体动态知识库（KPI树+拓扑图+Trace调用栈）                            | 定位准确率91.2%                   |
| 智能修复         | BossAgent调度ExpertAgents/SeniorAgent生成修复脚本                        | 方案准确率≥98.7%                  |
| 安全验证         | Docker沙盒预执行脚本，Lynis全系统漏洞扫描                                | 沙盒验证耗时6-10秒，扫描覆盖率100% |
| 自然语言交互     | 基于RAG知识库解析用户意图，转化为Shell指令                                | 指令转化率≥98.5%                  |


## 技术架构
SmartCare采用分层架构设计，覆盖“数据流-决策流-执行流-校验流”全链路，架构图如下：
```
┌───────────────── 界面层 ─────────────────┐
│ Grafana可视化 | Ant Design前端 | 自然语言交互窗口 │
├───────────────── 网关层 ─────────────────┤
│ Nginx（HTTP/SocketIO/SSE协议转发）        │
├───────────────── 业务服务层 ──────────────┤
│ 仪表盘 | 根因分析 | 告警预警 | 沙盒验证 | 漏洞扫描 │
├───────────────── 服务层 ─────────────────┤
│ Flask/FastAPI（接口服务） | LangChain（RAG+LLM调用） │
├───────────────── 数据处理层 ──────────────┤
│ Telegraf（指标采集） | Logstash（日志处理） | 数据清洗/对齐 │
├───────────────── 数据库层 ──────────────┤
│ InfluxDB（时序指标） | MySQL（日志数据） | Redis（缓存）   │
├───────────────── AI层 ─────────────────┤
│ BossAgent（决策中枢） | ExpertAgents（专项处理） | SeniorAgent（复杂修复） │
├───────────────── 基础设施层 ──────────────┤
│ Ansible（批量执行） | Docker（沙盒） | BCC（内核追踪）    │
└───────────────── 运行环境层 ──────────────┤
│ Kylin-Server-V10-SP3 | Python 3.10+ | Java 17        │
```


## 环境要求
### 硬件要求
| 角色     | CPU                | 内存 | 磁盘       | 网络       |
|----------|--------------------|------|------------|------------|
| 服务器端 | Intel Core i3-8100+ | 32GB | 系统盘240GB+数据盘4TB | 千兆以太网 |
| 客户端   | Intel Core i7-14650HX+ | 16GB | 2.7TB+     | 千兆以太网 |

### 软件要求
| 类别         | 依赖组件                          | 版本要求                  |
|--------------|-----------------------------------|---------------------------|
| 操作系统     | 银河麒麟服务器操作系统            | Kylin-Server-V10-SP3       |
| 开发语言     | Python/Java                       | Python 3.10+ / Java 17     |
| 数据库       | InfluxDB/MySQL/Redis              | InfluxDB 2.7.12 / MySQL 8.0+ |
| 数据采集     | Telegraf/Logstash/BCC             | Telegraf 1.30.3 / Logstash 9.0.3 |
| 容器与编排   | Docker/Ansible                    | Docker 20.10+ / Ansible 2.8.8 |
| 可视化       | Grafana                           | Grafana 9.0+               |
| 安全工具     | Lynis/ttyd                        | Lynis 3.1.5 / ttyd 1.7.4   |
| Python库     | fastapi/transformers/torch等      | 详见requirements.txt       |


## 安装部署
### 前置依赖安装
1. **配置YUM源**（银河麒麟系统）
   ```bash
   sudo tee /etc/yum.repos.d/kylin-adv.repo << EOF
   [ks10-adv-os]
   name=Kylin-Server-10-Adv-os
   baseurl=http://update.cs2c.com.cn:8080/NS/V10/V10SP3/x86_64/os/
   gpgcheck=0
   enabled=1
   EOF
   sudo yum clean all && sudo yum makecache
   ```

2. **安装基础工具**
   ```bash
   # 开发工具链
   sudo yum groupinstall "Development Tools" -y
   # 依赖库
   sudo yum install -y cmake3 git flex bison elfutils-libelf-devel clang llvm-devel zlib-devel luajit luajit-devel
   ```

### 核心组件部署
#### 1. 数据库部署（InfluxDB/MySQL/Redis）
- **InfluxDB 2.7.12**（时序指标存储）
  ```bash
  # 下载安装
  curl --location -O https://download.influxdata.com/influxdb/releases/influxdb2-2.7.12_linux_amd64.tar.gz
  tar xvzf influxdb2-2.7.12_linux_amd64.tar.gz
  sudo cp ./influxdb2-2.7.12/usr/bin/influxd /usr/local/bin/
  # 启动并配置（访问http://localhost:8086设置用户名/密码/组织/桶）
  influxd --http-bind-address :8086
  ```

- **MySQL 8.0**（日志数据存储）
  ```bash
  sudo yum install mysql-server -y
  sudo systemctl start mysqld && sudo systemctl enable mysqld
  # 初始化（设置root密码，创建kylin_2025数据库）
  mysql_secure_installation
  mysql -u root -p -e "CREATE DATABASE IF NOT EXISTS kylin_2025;"
  ```

- **Redis 5.0+**（缓存）
  ```bash
  sudo yum install redis -y
  sudo systemctl start redis && sudo systemctl enable redis
  ```

#### 2. 数据采集组件部署
- **Telegraf 1.30.3**（指标采集）
  ```bash
  wget https://dl.influxdata.com/telegraf/releases/telegraf-1.30.3_linux_amd64.tar.gz
  tar xvzf telegraf-1.30.3_linux_amd64.tar.gz
  sudo cp ./telegraf-1.30.3/usr/bin/telegraf /usr/local/bin/
  # 配置（对接InfluxDB，采集CPU/内存/磁盘等指标）
  sudo mkdir /etc/telegraf
  sudo cp ./telegraf-1.30.3/etc/telegraf/telegraf.conf /etc/telegraf/
  # 编辑配置文件，设置outputs.influxdb_v2的urls/token/organization/bucket
  sudo vim /etc/telegraf/telegraf.conf
  # 启动
  telegraf --config /etc/telegraf/telegraf.conf
  ```

- **Logstash 9.0.3**（日志采集）
  ```bash
  wget https://artifacts.elastic.co/downloads/logstash/logstash-9.0.3-x86_64.rpm
  sudo rpm -ivh logstash-9.0.3-x86_64.rpm
  # 配置（对接MySQL，处理系统日志/应用日志/安全日志）
  sudo cp docs/conf/logstash/* /etc/logstash/conf.d/
  # 编辑配置文件，修改jdbc连接信息（数据库地址/用户名/密码）
  sudo vim /etc/logstash/conf.d/applicationlog.conf
  # 启动
  sudo -u logstash /usr/share/logstash/bin/logstash --path.settings /etc/logstash -f /etc/logstash/conf.d
  ```

- **BCC 0.28.0**（内核调用栈采集）
  ```bash
  # 安装依赖
  sudo yum install -y cmake3 git flex bison elfutils-libelf-devel clang llvm-devel zlib-devel luajit luajit-devel
  # 下载编译
  git clone https://github.com/iovisor/bcc.git
  cd bcc && git checkout v0.28.0
  mkdir build && cd build
  cmake3 .. -DCMAKE_INSTALL_PREFIX=/usr -DPYTHON_CMD=python3.7 -DCMAKE_BUILD_TYPE=Release
  make -j$(nproc) && sudo make install
  # 配置采集脚本（磁盘IO/文件锁/网络阻塞等）
  sudo mkdir /opt/kylin_trace
  sudo cp docs/scripts/bcc/* /opt/kylin_trace/
  # 启动服务
  sudo cp docs/systemd/kylin_trace/* /etc/systemd/system/
  sudo systemctl daemon-reload && sudo systemctl enable --now kylin_io.service
  ```

#### 3. 核心服务部署
1. **克隆代码仓库**
   ```bash
   git clone https://github.com/your-username/GS_SmartCare_for_KylinOS.git
   cd GS_SmartCare_for_KylinOS
   ```

2. **安装Python依赖**
   ```bash
   pip install -r requirements.txt
   ```

3. **配置系统参数**
   ```bash
   # 复制配置模板
   cp config/config_template.yaml config/config.yaml
   # 编辑配置（数据库地址、LLM模型地址、Agent参数等）
   vim config/config.yaml
   ```

4. **启动服务**
   ```bash
   # 启动API服务（FastAPI）
   uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 4
   # 启动前端服务（Nginx）
   sudo cp docs/conf/nginx/smartcare.conf /etc/nginx/conf.d/
   sudo systemctl restart nginx
   # 启动Agent服务
   python app/agent/agent_manager.py
   ```

#### 4. 沙盒与漏洞扫描部署
- **Docker沙盒（KylinSandbox Ⅱ型）**
  ```bash
  # 安装Docker
  wget https://download.docker.com/linux/static/stable/x86_64/docker-20.10.0.tgz
  tar xzvf docker-20.10.0.tgz
  sudo cp docker/* /usr/bin/
  # 配置启动
  sudo tee /usr/lib/systemd/system/docker.service << EOF
  [Unit]
  Description=Docker Application Container Engine
  After=network-online.target firewalld.service
  [Service]
  Type=notify
  ExecStart=/usr/bin/dockerd
  Restart=on-failure
  [Install]
  WantedBy=multi-user.target
  EOF
  sudo systemctl daemon-reload && sudo systemctl start docker && sudo systemctl enable docker
  # 构建沙盒镜像
  docker build -t kylin-sandbox:v2 -f docs/docker/Dockerfile .
  ```

- **Lynis漏洞扫描**
  ```bash
  # 安装Lynis
  tar xvfz docs/tools/lynis-3.1.5.tar.gz
  sudo mv lynis /usr/local/
  sudo ln -s /usr/local/lynis/lynis /usr/local/bin/lynis
  # 配置定时扫描
  sudo crontab -e
  # 添加：0 2 * * * /usr/local/bin/lynis audit system --report-file /var/log/lynis_scan.log
  ```


## 快速使用
### 1. 访问系统
- **前端界面**：浏览器访问 `http://<服务器IP>:80`（默认账号/密码：admin/SmartCare@2025）
- **API文档**：访问 `http://<服务器IP>:8000/docs` 查看Swagger文档

### 2. 核心功能操作
#### （1）实时监控
1. 登录后进入「仪表盘」页面，查看CPU/内存/磁盘/网络等指标的实时趋势
2. 支持按“CPU核心”“磁盘分区”“网络接口”筛选指标，时间范围可选择“实时/1小时/24小时/7天”

#### （2）异常预警
1. 系统自动检测异常并触发告警（响应时间≤0.5秒），告警信息展示在「告警异常」页面
2. 点击“预警详情”可查看KylinPulse模型预测的未来指标趋势，提前5分钟干预潜在风险

#### （3）根因分析
1. 在「根因分析」页面，系统自动生成KPI树，标记异常节点
2. 点击异常节点，查看拓扑图异常依赖链及Trace调用栈，定位故障根本原因

#### （4）自动化修复
1. 告警触发后，系统自动生成修复方案（或在「AI聊天」窗口输入自然语言需求，如“修复CPU使用率过高问题”）
2. 方案经沙盒验证通过后，点击“执行修复”，系统通过Ansible在目标服务器执行脚本
3. 修复完成后生成报告，包含修复前后指标对比

#### （5）漏洞扫描
1. 进入「漏洞扫描」页面，点击“开始扫描”，Lynis工具将对全系统进行安全审计
2. 扫描完成后查看漏洞列表及修复建议，支持导出Markdown格式报告


## 目录结构
```
GS_SmartCare_for_KylinOS/
├── app/                      # 核心服务代码
│   ├── main.py               # API入口（FastAPI）
│   ├── agent/                # Agent管理（BossAgent/ExpertAgents/SeniorAgent）
│   ├── data/                 # 数据处理（采集/清洗/存储）
│   ├── service/              # 业务服务（监控/预警/修复/扫描）
│   └── utils/                # 工具函数（日志/加密/校验）
├── config/                   # 配置文件
│   ├── config.yaml           # 系统配置（数据库/LLM/Agent）
│   └── config_template.yaml  # 配置模板
├── docs/                     # 文档与配置模板
│   ├── conf/                 # Nginx/Logstash配置
│   ├── scripts/              # BCC采集脚本/系统调优脚本
│   ├── systemd/              # 系统服务配置
│   └── docker/               # 沙盒Dockerfile
├── frontend/                 # 前端代码（Ant Design Vue）
├── requirements.txt          # Python依赖库
├── LICENSE                   # 开源协议
└── README.md                 # 项目说明（本文档）
```


## 常见问题
### 1. 安装BCC时提示“libbpf/src/bpf.h：没有那个文件或目录”
**解决方法**：
```bash
# 下载libbpf源码并解压到BCC目录
wget https://github.com/libbpf/libbpf/archive/refs/tags/v1.2.0.tar.gz
tar -xzf v1.2.0.tar.gz -C bcc/src/cc/
mv bcc/src/cc/libbpf-1.2.0 bcc/src/cc/libbpf
# 修复头文件引用
sed -i 's|#include "libbpf/src/|#include "|' bcc/src/cc/bcc_libbpf_inc.h
```

### 2. 沙盒验证脚本时提示“环境不一致”
**解决方法**：
```bash
# 同步目标服务器环境参数到沙盒
python app/utils/sync_env.py --target-ip <目标服务器IP> --sandbox-container <沙盒容器ID>
```

### 3. 自然语言交互时指令转化失败
**解决方法**：
1. 检查RAG知识库是否加载（`http://<服务器IP>:8000/api/rag/status`）
2. 若知识库未加载，执行以下命令初始化：
   ```bash
   python app/service/rag_service.py --init
   ```


## 贡献指南
我们欢迎社区贡献代码、文档或反馈问题，贡献流程如下：
1. Fork本仓库到个人账号
2. 创建特性分支（`git checkout -b feature/your-feature`）
3. 提交代码（`git commit -m "Add your feature"`）
4. 推送分支（`git push origin feature/your-feature`）
5. 提交Pull Request，描述功能细节及测试情况

贡献者需遵循以下规范：
- 代码风格：Python代码遵循PEP 8规范，前端代码遵循ESLint规范
- 提交信息：格式为“[模块名] 功能描述”（如“[Agent] 优化置信度计算逻辑”）
- 测试要求：新增功能需附带单元测试（覆盖核心逻辑）


## 许可证
本项目基于MIT许可证开源，详见[LICENSE](LICENSE)文件。


## 联系我们
- 项目维护者：Huang Yingyuan
- 邮箱：yingyuanhuang6@gmail.com
- 问题反馈：在GitHub Issues提交问题，或发送邮件说明需求与建议
- 合作交流：欢迎国产操作系统生态伙伴共同推进项目迭代，可通过邮箱联系合作事宜


## 致谢
本项目是广州城市理工学院团队参加第十九届“挑战杯”全国大学生课外学术科技作品竞赛的成果，感谢学校及指导老师的支持，同时感谢InfluxData、Elastic、Docker等开源社区提供的技术支撑。
