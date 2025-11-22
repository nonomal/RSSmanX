# RSSman X 文档

RSSman X 是一个组合了多个实用服务（Tiny Tiny RSS, Mercury, OpenCC, RSSHub, Huginn 等）的项目，旨在帮助您构建卓越的 RSS 体验。

## 快速开始 (Quick Start)

遵循以下简单步骤，在您的服务器上运行 RSSman X。

### 前提条件

- **Docker**: 确保已安装并运行 Docker。
- **Docker Compose**: 确保已安装 Docker Compose。

### 安装步骤

1.  **克隆仓库**
    ```bash
    git clone https://github.com/Colin-XKL/RSSmanX.git
    cd RSSmanX
    ```

2.  **选择版本**
    进入您希望使用的版本目录：
    - **Lite (精简版)**: 基础的 TTRSS 和 RSSHub。
      ```bash
      cd rssman-lite
      ```
    - **Standard (标准版)**: 包含 TTRSS, RSSHub, Mercury 等。
      ```bash
      cd rssman-standard
      ```
    - **Ultimate (终极版)**: 全功能集合，包含 Huginn, 全局代理和智能路由。
      ```bash
      cd rssman-ultimate
      ```

3.  **配置环境变量**
    每个版本目录下都有一个 `.env` 文件。打开此文件并配置变量。

    关键变量检查：
    - `DB_Password`: 设置安全的数据库密码。
    - `HOST`: 服务器的 IP 地址或域名。
    - `TTRSS_PORT`: 访问 TTRSS 的端口（例如 777）。
    - `TTRSS_SELF_URL`: 访问 TTRSS 实例的完整 URL（例如 `http://your-domain.com:777`）。

4.  **运行应用**
    在后台启动服务：
    ```bash
    docker-compose up -d
    ```
    *注意：首次运行可能需要一些时间下载镜像。*

5.  **访问服务**
    打开浏览器访问 `http://YOUR_SERVER_IP:777`（或您定义的端口）。
    - **默认用户名**: `admin`
    - **默认密码**: `password`
    - **重要**: 登录后请立即更改密码。

---

## 高级定制 (High Level Customization)

适用于需要对环境和网络设置进行更多控制的高级用户。

### 环境变量 (.env)

`.env` 文件是配置的核心。

| 变量 | 描述 |
| :--- | :--- |
| `DB_Password` | PostgreSQL 数据库的密码。 |
| `DB_DIR` | 宿主机上存储数据库文件的路径。根据版本默认为 `/data/docker` 或 `~/.docker`。 |
| `HOST` | 服务器的 IP 地址或域名。 |
| `TTRSS_PORT` | 映射到 TTRSS 容器的外部端口。 |
| `HUGINN_PORT` | (仅限 Ultimate/Standard) 映射到 Huginn 的外部端口。 |
| `RSS_ALLOW_PORTS` | TTRSS 允许连接以获取订阅源的端口列表。默认：`80,443,3000,7000`。 |
| `TTRSS_SELF_URL` | TTRSS 实例的公网 URL。对于正确生成内部链接至关重要。 |

### Ultimate 版本配置 (config.yaml)

**Ultimate** 版本包含全局代理功能（由 Clash 驱动），用于获取被屏蔽的订阅源或智能路由流量。通过 `rssman-ultimate/config.yaml` 进行配置。

**关键部分：**

- **`proxy-providers`**: 定义从何处获取代理节点列表。您可以将默认 URL 替换为您自己的订阅链接。
  ```yaml
  proxy-providers:
    global10:
      type: http
      url: "YOUR_SUBSCRIPTION_URL"
      path: ./merged_sub.yaml
      interval: 3600
  ```

- **`proxy-groups`**: 定义如何选择代理。
  - `fq`: 回退组，自动选择可用的代理。
  - `global-random`: 负载均衡流量，有助于规避反爬虫机制（例如 Cloudflare）。

- **`rules`**: 定义路由规则。
  - `DOMAIN-KEYWORD,google,fq`: 将 Google 流量路由通过 `fq` 代理组。
  - `MATCH,global-random`: 默认规则，将未匹配的流量路由通过 `global-random`。

**使用方法：**
如果您在获取特定订阅源时遇到问题，请修改 `config.yaml` 添加您自己的代理服务器或调整路由规则。
