# SightPro 部署文档

## 目录
1. [系统要求](#系统要求)
2. [环境准备](#环境准备)
3. [数据库配置](#数据库配置)
4. [MinIO配置](#minio配置)
5. [后端部署](#后端部署)
6. [前端部署](#前端部署)
7. [系统启动](#系统启动)
8. [常见问题](#常见问题)

---


## 环境准备

### 1. 安装 Java JDK

#### Windows
1. 下载 JDK 8 或更高版本：https://www.oracle.com/java/technologies/downloads/
2. 安装并配置环境变量：
   
   JAVA_HOME=C:\Program Files\Java\jdk1.8.0_xxx
   PATH=%JAVA_HOME%\bin;%PATH%
   3. 验证安装：
   java -version
   javac -version
   #### Linuxh
# Ubuntu/Debian
sudo apt update
sudo apt install openjdk-8-jdk

# CentOS/RHEL
sudo yum install java-1.8.0-openjdk-devel

# 验证
java -version### 2. 安装 Maven

#### Windows
1. 下载 Maven：https://maven.apache.org/download.cgi
2. 解压到目录（如：`C:\Program Files\Apache\maven`）
3. 配置环境变量：
   MAVEN_HOME=C:\Program Files\Apache\maven
   PATH=%MAVEN_HOME%\bin;%PATH%
   4. 验证：
   
   mvn -version
   #### Linux
# Ubuntu/Debian
sudo apt install maven

# CentOS/RHEL
sudo yum install maven

# 验证
mvn -version### 3. 安装 Node.js

#### Windows/Linux/macOS
1. 下载 Node.js：https://nodejs.org/
2. 安装 Node.js 16.x 或更高版本
3. 验证安装：
   node -v
   npm -v
   ### 4. 安装 MySQL

#### Windows
1. 下载 MySQL：https://dev.mysql.com/downloads/mysql/
2. 安装 MySQL Server
3. 记住 root 密码

#### Linux
# Ubuntu/Debian
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation

# CentOS/RHEL
sudo yum install mysql-server
sudo systemctl start mysqld
sudo systemctl enable mysqld### 5. 安装 MinIO

#### Windows
1. 下载 MinIO：https://min.io/download
2. 解压到目录
3. 创建启动脚本 `start-minio.bat`：tch
   @echo off
   minio.exe server D:\minio-data --console-address ":9001"
   
#### Linux
# 下载
wget https://dl.min.io/server/minio/release/linux-amd64/minio
chmod +x minio
sudo mv minio /usr/local/bin/

# 创建数据目录
sudo mkdir -p /data/minio
sudo chown -R $USER:$USER /data/minio

# 启动（前台运行）
minio server /data/minio --console-address ":9001"

# 或使用 systemd 服务（后台运行）
# 创建 /etc/systemd/system/minio.service#### Docker（推荐）
docker run -d \
  -p 9000:9000 \
  -p 9001:9001 \
  --name minio \
  -e "MINIO_ROOT_USER=minioadmin" \
  -e "MINIO_ROOT_PASSWORD=minioadmin" \
  -v /data/minio:/data \
  minio/minio server /data --console-address ":9001"---

## 数据库配置

### 1. 创建数据库

登录 MySQL：sh
mysql -u root -p
执行 SQL：
CREATE DATABASE sightpro CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;### 2. 配置数据库用户（可选）

CREATE USER 'sightpro'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON sightpro.* TO 'sightpro'@'localhost';
FLUSH PRIVILEGES;### 3. 数据库表结构

**注意**：系统使用 JPA/Hibernate 自动创建表结构，首次启动时会自动创建。

如果需要手动创建，表结构如下：

- `user` - 用户表
- `model_config` - 模型配置表
- `detection_task` - 检测任务表
- `task_alarm` - 任务告警表

---

## MinIO配置

### 1. 启动 MinIO

#### Windows
minio.exe server D:\minio-data --console-address ":9001"#### Linux
minio server /data/minio --console-address ":9001"### 2. 访问 MinIO 控制台

- 访问地址：http://localhost:9001
- 默认用户名：`minioadmin`
- 默认密码：`minioadmin`

### 3. 创建存储桶

1. 登录 MinIO 控制台
2. 点击 "Create Bucket"
3. 创建名为 `sightpro` 的存储桶
4. 设置访问策略为 "Public"（如果需要）

**注意**：系统首次启动时会自动创建存储桶（如果不存在）。

### 4. 配置访问密钥

在 MinIO 控制台：
1. 进入 "Access Keys"
2. 创建新的 Access Key 和 Secret Key
3. 记录密钥信息，用于后端配置

---

## 后端部署

### 方式一：Java Spring Boot 后端（推荐）

#### 1. 配置数据库连接

编辑 `javaServer/src/main/resources/application.yml`：
ml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/sightpro?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: your_password
    driver-class-name: com.mysql.cj.jdbc.Driver

sightpro:
  jwt:
    secret: your-secret-key-change-in-production  # 生产环境请修改为强密钥
    expire-minutes: 43200
  minio:
    endpoint: http://localhost:9001
    access-key: your-access-key
    secret-key: your-secret-key
    bucket: sightpro
  upload-dir: ./uploads
  model-dir: ./models#### 2. 编译项目

cd javaServer
mvn clean package#### 3. 运行项目

**开发环境**：
mvn spring-boot:run**生产环境**：
java -jar target/sightpro-server-1.0.0.jar#### 4. 验证启动

- 访问：http://localhost:8000/health
- 应返回：`{"status":"ok"}`

### 方式二：Python FastAPI 后端（可选）

#### 1. 安装 Python 依赖

cd server
pip install -r requirements.txt#### 2. 配置环境变量

创建 `.env` 文件：

DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=sightpro

MINIO_ENDPOINT=localhost:9001
MINIO_ACCESS_KEY=your-access-key
MINIO_SECRET_KEY=your-secret-key
MINIO_BUCKET=sightpro
MINIO_SECURE=False

API_V1_PREFIX=/api/v1
UPLOAD_DIR=./uploads
MODEL_DIR=./models#### 3. 初始化管理员
h
python init_admin.py默认管理员账号：
- 用户名：`admin`
- 密码：`admin123`

#### 4. 启动服务

**开发环境**：
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000**生产环境**：h
uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 4---

## 前端部署

### 1. 安装依赖

cd web
npm install### 2. 配置 API 地址

编辑 `web/vite.config.js`：
script
export default defineConfig({
  server: {
    port: 8888,
    proxy: {
      '/api': {
        target: 'http://127.0.0.1:8000',  // 后端服务地址
        changeOrigin: true
      }
    }
  }
})### 3. 开发环境运行

npm run dev访问：http://localhost:8888

### 4. 生产环境构建
h
npm run build构建产物在 `web/dist` 目录。

### 5. 部署到 Nginx

#### Nginx 配置示例

server {
    listen 80;
    server_name your-domain.com;
    
    root /path/to/web/dist;
    index index.html;
    
    # 前端路由
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # API 代理
    location /api {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    # 静态资源缓存
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}重启 Nginx：
sudo nginx -s reload---

## 系统启动

### 启动顺序

1. **启动 MySQL**
   # Windows
   net start MySQL80
   
   # Linux
   sudo systemctl start mysql
   2. **启动 MinIO**
   # Windows
   start-minio.bat
   
   # Linux
   minio server /data/minio --console-address ":9001"
   3. **启动后端服务**
   
   # Java 后端
   cd javaServer
   java -jar target/sightpro-server-1.0.0.jar
   
   # 或 Python 后端
   cd server
   uvicorn app.main:app --host 0.0.0.0 --port 8000
   4. **启动前端服务**（开发环境）
   cd web
   npm run dev
   
### 验证部署

1. **检查后端服务**
   - 访问：http://localhost:8000/health
   - 应返回：`{"status":"ok"}`

2. **检查前端服务**
   - 访问：http://localhost:8888
   - 应显示登录页面

3. **登录系统**
   - 默认管理员账号：
     - 用户名：`admin`
     - 密码：`admin123`
   - **重要**：首次登录后请立即修改密码！

---

## 生产环境优化

### 1. 使用 systemd 管理服务（Linux）

#### 创建后端服务文件

`/etc/systemd/system/sightpro-backend.service`：

[Unit]
Description=SightPro Backend Service
After=network.target mysql.service

[Service]
Type=simple
User=your-user
WorkingDirectory=/path/to/javaServer
ExecStart=/usr/bin/java -jar /path/to/javaServer/target/sightpro-server-1.0.0.jar
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target启动服务：
sudo systemctl daemon-reload
sudo systemctl enable sightpro-backend
sudo systemctl start sightpro-backend
sudo systemctl status sightpro-backend### 2. 配置 JVM 参数
ash
java -Xms512m -Xmx2048m -XX:+UseG1GC \
  -jar target/sightpro-server-1.0.0.jar### 3. 配置日志

编辑 `application.yml`：

logging:
  level:
    root: INFO
    com.sightpro: DEBUG
  file:
    name: logs/sightpro.log
  pattern:
    file: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"### 4. 配置 HTTPS

使用 Nginx 反向代理配置 SSL：

server {
    listen 443 ssl http2;
    server_name your-domain.com;
    
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    
    # ... 其他配置
}### 5. 防火墙配置

# 开放端口
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 8000/tcp
sudo ufw allow 9000/tcp
sudo ufw allow 9001/tcp---

## 常见问题

### 1. 数据库连接失败

**问题**：无法连接到 MySQL

**解决方案**：
- 检查 MySQL 服务是否启动
- 检查数据库用户名和密码是否正确
- 检查数据库是否已创建
- 检查防火墙是否阻止了连接

### 2. MinIO 连接失败

**问题**：无法连接到 MinIO

**解决方案**：
- 检查 MinIO 服务是否启动
- 检查 endpoint 地址和端口是否正确
- 检查 access-key 和 secret-key 是否正确
- 检查存储桶是否已创建

### 3. 前端无法访问后端 API

**问题**：前端请求 API 返回 404 或 CORS 错误

**解决方案**：
- 检查后端服务是否启动
- 检查 `vite.config.js` 中的 proxy 配置
- 检查后端 CORS 配置
- 检查 API 路径前缀是否正确

### 4. 模型文件无法加载

**问题**：检测任务失败，提示模型文件不存在

**解决方案**：
- 检查模型文件路径是否正确
- 检查模型文件是否已上传到 MinIO
- 检查 `model-dir` 配置是否正确
- 确保模型文件格式为 ONNX

### 5. 内存不足

**问题**：系统运行缓慢或崩溃

**解决方案**：
- 增加 JVM 堆内存：`-Xmx2048m`
- 优化模型大小
- 减少并发任务数
- 使用 GPU 加速（如果支持）

### 6. 文件上传失败

**问题**：上传文件时提示错误

**解决方案**：
- 检查文件大小是否超过限制（默认 200MB）
- 检查 `upload-dir` 目录权限
- 检查磁盘空间是否充足
- 检查 MinIO 存储空间

### 7. 管理员账号无法登录

**问题**：使用默认账号无法登录

**解决方案**：
- 检查数据库是否已初始化
- 运行初始化脚本：`InitAdminRunner`（Java）或 `init_admin.py`（Python）
- 检查密码是否正确（默认：`admin123`）
- 检查用户状态是否为激活状态

---

## 技术支持

如遇到其他问题，请：
1. 查看日志文件：`logs/sightpro.log`
2. 检查系统资源使用情况
3. 查看数据库连接状态
4. 联系技术支持

---

## 更新日志

### v1.0.0
- 初始版本发布
- 支持图片和视频检测
- 支持模型管理
- 支持告警统计

---

**文档版本**: 1.0.0  
**最后更新**: 2024年