<div align="center">

  <h2 align="center">Cloudflare - Turnstile Solver</h2>
  <p align="center">
基于 Python 的 Cloudflare Turnstile 验证码解析工具，使用 Camoufox 浏览器引擎。支持多线程并发、RESTful API、自动缓存清理、代理支持，以及多平台 Docker 部署（amd64/arm64）。
  </p>
</div>

---

### ❗ 免责声明

- 对于使用本项目可能导致的任何后果（如 API 被封锁、IP 被禁止等），作者概不负责。
- 本项目最初为个人兴趣和自用而开发。如果你希望看到更多更新，请给仓库加 Star。

---

### ⚙️ 安装指南

1. **确保系统已安装 Python 3.8+**。

2. **创建虚拟环境**：

   ```bash
   python -m venv venv
   ```

3. **激活虚拟环境**：
   - **Windows**：
     ```bash
     venv\Scripts\activate
     ```
   - **macOS/Linux**：
     ```bash
     source venv/bin/activate
     ```

4. **安装依赖**：

   ```bash
   pip install -r requirements.txt
   ```

5. **启动求解器**：
   - 运行脚本（查看 [🔧 命令行参数](#-命令行参数) 了解配置项）：
     ```bash
     python api_solver.py
     ```

---

### 🔧 命令行参数

| 参数              | 默认值    | 类型      | 说明                                            |
| ----------------- | --------- | --------- | ----------------------------------------------- |
| `--thread`        | `1`       | `integer` | 并发浏览器线程数。                              |
| `--proxy`         | `False`   | `boolean` | 启用代理支持，从 `proxies.txt` 中随机选择代理。 |
| `--host`          | `0.0.0.0` | `string`  | API 服务监听地址。                              |
| `--port`          | `5072`    | `integer` | API 服务监听端口。                              |
| `--debug`         | `False`   | `boolean` | 启用调试模式，输出详细日志。                    |

---

### 🐳 Docker 部署

#### 使用 GitHub Container Registry

多平台镜像（amd64 和 arm64）通过 GitHub Actions 自动构建并发布。

```bash
docker pull ghcr.io/kaonimei/turnstile-solver:latest
```

#### 启动容器

```bash
docker run -d \
  -p 5072:5072 \
  -e TZ=Asia/Shanghai \
  --name turnstile_solver \
  ghcr.io/kaonimei/turnstile-solver:latest
```

可以在镜像名称后追加[命令行参数](#-命令行参数)：

```bash
docker run -d \
  -p 5072:5072 \
  -e TZ=Asia/Shanghai \
  --name turnstile_solver \
  ghcr.io/kaonimei/turnstile-solver:latest \
  --host 0.0.0.0 --port 5000 --thread 2 --browser_type camoufox
```

#### 本地构建

```bash
docker build -t turnstile-solver .
docker run -d -p 5072:5072 --name turnstile_solver turnstile-solver
```


---

### 📡 API 文档

#### 创建求解任务

```http
GET /turnstile?url=https://example.com&sitekey=0x4AAAAAAA
```

**请求参数**：

| 参数          | 类型   | 说明                                                    | 必填 |
| ------------- | ------ | ------------------------------------------------------- | ---- |
| `url`         | string | 包含 CAPTCHA 的目标页面 URL（如 `https://example.com`） | 是   |
| `sitekey`     | string | CAPTCHA 的站点密钥（如 `0x4AAAAAAA`）                   | 是   |
| `action`      | string | 求解过程中触发的操作，如 `login`                        | 否   |
| `cdata`       | string | 自定义数据，用于附加 CAPTCHA 参数                       | 否   |

**响应**：

如果请求成功接收，服务器将返回验证码识别任务的 `task_id`：

```json
{
  "task_id": "d2cbb257-9c37-4f9c-9bc7-1eaee72d96a8"
}
```

---

#### 查询结果

```http
GET /result?id=<task_id>
```

| 参数 | 类型   | 说明                                    | 必填 |
| ---- | ------ | --------------------------------------- | ---- |
| `id` | string | 由 `/turnstile` 接口返回的唯一任务 ID。 | 是   |

**响应**：

如果成功通过验证码，服务器将返回以下信息：

```json
{
  "elapsed_time": 7.625,
  "value": "0.KBtT-r"
}
```

---

### 📜 致谢

灵感来源于 [Turnaround](https://github.com/Body-Alhoha/turnaround)
原始代码作者 [Theyka](https://github.com/Theyka/Turnstile-Solver)
修改者 [Sexfrance](https://github.com/sexfrance)
优化重构 [taozhiyu](https://github.com/taozhiyu)