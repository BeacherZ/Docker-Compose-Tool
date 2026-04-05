# Docker-Compose-Tool 🐳

让 Docker 部署告别碎片化，实现“一容器一目录”的标准化管理。

## 🏗️ 核心哲学

图纸与材料“同屋”存放。在玩 Docker 的时候，最痛苦的莫过于处理 `-v` 卷映射。很多项目只给了一行 `docker run` 命令，或者默认用户能看懂的示例 `compose.yaml` 规则。结果就是：装了几个项目后，各种映射卷零零散散地躺在宿主机的各个目录里，极其混乱。

本工具的核心目的：

- **统一管理**：所有的 Docker 项目映射卷统一存放在一个大目录（如 `/opt/docker`）。
- **子目录隔离**：以容器名为子目录存放配置文件和数据（例如 `/opt/docker/vaultwarden`）。
- **无损迁移**：这相当于把 Docker 项目的“图纸”（Compose 文件）和“装修材料”（映射数据）放在了一起。
- **极简运维**：以后备份或迁移，只需要打包这个子目录；恢复时只需 `cd` 进去执行 `docker compose up -d` 即可。

## 💎 特色功能：自定义卷映射重构

针对“一容器一目录”的设计哲学，本工具对 Volume 卷映射进行了专项优化：

- **自动路径对齐**：工具会自动提取容器名，将其作为挂载路径的子目录，确保路径结构整齐划一。
- **绝对路径规范化**：自动识别并转换 `/mnt/data:/config` 等复杂绝对路径，防止手动编写 YAML 时出现缩进或语法错误。
- **命名卷（Named Volumes）自动声明**：当检测到非路径格式的映射（如 `db_data:/var`）时，工具会自动在 YAML 末尾生成根级别的 `volumes:` 声明块。
- **路径安全引号**：自动为所有路径添加双引号包裹，完美避开路径中包含空格、`@`、`$` 等特殊字符导致的解析失败。
- **系统级挂载不转换**：如 /var/run/docker.sock 等系统级挂载不会被替换。

## 🚀 为什么选择它？

- **100% 隐私安全**：纯 HTML + JS 静态实现，无后端交互。你的路径、环境变量、API Key 等敏感数据仅在浏览器本地处理，绝不上传。
- **生产环境预设**：默认勾选 `unless-stopped` 自动重启策略，符合云服务器（Alibaba Cloud/DMIT）的长期运行最佳实践。
- **零门槛自建**：项目仅包含 `index.html` 和 `volumes.js`，下载即可运行，无需复杂的 Docker 镜像构建或环境配置。

## 🛠️ 使用指南（下载即用）

### 获取文件：
下载本仓库中的 `index.html` 和 `volumes.js`。

### 本地运行：
确保两个文件在同一目录下，直接用 Chrome 或 Edge 浏览器打开 `index.html`。

### 自建网站运行：
将 `index.html` 和 `volumes.js` 文件上传到你自己的 Web 服务器根目录下，然后通过访问相应的 URL（例如 `http://docker.your-domain.com`）来运行。

### 一键转换：
1. 粘贴从官方文档或 GitHub 看到的 `docker run` 命令或者示例 `compose.yaml` 规则。
2. 确认生成的路径符合你的“统一大目录”规划。
3. 点击“转换为 Compose 配置”并一键复制。

### 快速部署：
在你的统一目录下创建docker项目同名文件夹，将代码存为 `compose.yaml`，然后 `cd` 进去执行 docker compose up -d 命令启动服务：

## 📄 转换对比示例
原始 Docker Run 命令：
```
docker pull vaultwarden/server:latest
docker run --detach --name vaultwarden \
  --env DOMAIN="https://vw.domain.tld" \
  --volume /vw-data/:/data/ \
  --restart unless-stopped \
  --publish 127.0.0.1:8000:80 \
  vaultwarden/server:latest
```
### 工具生成的标准化“图纸”：
```
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    ports:
      - "127.0.0.1:8000:80"
    volumes:
      - "/opt/docker/vaultwarden/vw-data/:/data/"
    environment:
      - "DOMAIN=https://vw.domain.tld"
    restart: unless-stopped
```
### 📄 开源协议

本项目采用 MIT License 协议开源。

如果您认同这种“一容器一目录”的部署哲学，请点一个 Star ⭐️ 鼓励作者持续优化！
