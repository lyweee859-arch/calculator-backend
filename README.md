# Calculator Backend

前后端分离计算器的独立 FastAPI 后端仓库。递归下降解析器完成全部表达式计算；SQLAlchemy + psycopg 将成功记录写入 PostgreSQL。未使用 `eval()` 或 `exec()`。

## 技术栈与目录

Python 3.12、FastAPI、SQLAlchemy、psycopg、PostgreSQL、pytest。

```text
app/main.py                       FastAPI 入口和启动建表
app/api/routes.py                 HTTP API
app/calculator/tokenizer.py       词法分析
app/calculator/parser.py          表达式解析与计算
app/database/models.py            SQLAlchemy 历史表模型
app/database/database.py          数据库连接与增删查
app/schemas/schemas.py            请求模型
app/services/calculator_service.py 计算与保存流程
tests/                            解析器、API、数据库 URL 测试
requirements.txt                  运行依赖
requirements-dev.txt              测试依赖
.env.example                      环境变量示例
```

## 安装与启动

在本仓库根目录运行：

```powershell
python -m venv .venv
.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
$env:DATABASE_URL = "postgresql://用户名:密码@主机/数据库?sslmode=require"
.venv\Scripts\python.exe -m uvicorn app.main:app --reload
```

服务地址 `http://127.0.0.1:8000`，Swagger：`http://127.0.0.1:8000/docs`。`DATABASE_URL` 必须是 PostgreSQL 连接串；`postgres://` 和 `postgresql://` 前缀会自动转换为 psycopg 驱动格式。应用启动时自动创建 `calculation_history` 表。连接串仅放在运行环境，不提交真实 `.env` 或数据库密码。

## 表达式语法

支持四则运算、括号、一元正负号、右结合乘方 `^`、后缀阶乘 `!`，以及 `sin`、`cos`、`tan`、`arcsin`、`arccos`、`arctan`、`sqrt`、`abs`、`ln`、`log`、`exp`。常数用 `π`（或 `pi`）和 `e`。函数需带括号，例如 `sin(π/2)+sqrt(9)`；三角函数及反三角函数的角度单位均为弧度，`log` 以 10 为底。阶乘仅接受 0 到 170 的整数；函数定义域外的输入返回 400，不写入历史。

## API

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| POST | `/api/calculate` | 请求 `{"expression":"(1+2)*3"}`；成功后保存历史，错误返回 400 |
| GET | `/api/history` | 按新到旧查询历史 |
| DELETE | `/api/history/{id}` | 删除单条，不存在返回 404 |
| DELETE | `/api/history` | 清空历史 |

## 测试

```powershell
.venv\Scripts\python.exe -m pytest tests -q
```

测试使用临时数据库，不会修改真实 PostgreSQL。可尝试 `1+2`、`1+2*3`、`(1+2)*3`、`3*-2`、`3.14*2`、`1/0` 和非法表达式。

**Deployment URL:** To be added after deployment. 课程作业的独立前端见 [calculator-frontend](https://github.com/lyweee859-arch/calculator-frontend)；同域前端与 Vercel 配置见 [calculator-vercel](https://github.com/lyweee859-arch/calculator-vercel)。
