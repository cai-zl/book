# Python

## 官方资源

- 官网: [python.org](https://www.python.org)
- 官方教程: [docs.python.org/3/tutorial](https://docs.python.org/3/tutorial/)
- 标准库文档: [docs.python.org/3/library](https://docs.python.org/3/library/)
- PEP 索引: [peps.python.org](https://peps.python.org)

## 环境管理

```bash
# 安装 (推荐 pyenv 管理多版本)
brew install pyenv
pyenv install 3.12
pyenv global 3.12

# 虚拟环境
python -m venv .venv
source .venv/bin/activate       # Linux/macOS
.venv\Scripts\activate          # Windows

# 包管理
pip install <package>
pip install -r requirements.txt
pip freeze > requirements.txt
```

## 推荐学习资源

- [Python Tutorial (官方)](https://docs.python.org/3/tutorial/) — 入门首选
- [Real Python](https://realpython.com) — 高质量教程
- [Python Cookbook](https://python3-cookbook.readthedocs.io) — 实用技巧合集
- [Awesome Python](https://awesome-python.com) — 资源汇总

## 常用框架/库

| 类别 | 推荐 |
|------|------|
| Web 框架 | [FastAPI](https://fastapi.tiangolo.com)、[Flask](https://flask.palletsprojects.com)、[Django](https://www.djangoproject.com) |
| 数据处理 | [Pandas](https://pandas.pydata.org)、[Polars](https://pola.rs) |
| HTTP 客户端 | [httpx](https://www.python-httpx.org)、[requests](https://docs.python-requests.org) |
| ORM | [SQLAlchemy](https://www.sqlalchemy.org)、[Django ORM](https://docs.djangoproject.com/en/stable/topics/db/models/) |
| 任务队列 | [Celery](https://docs.celeryq.dev)、[ARQ](https://arq-docs.helpmanual.io) |
| 测试 | [pytest](https://docs.pytest.org) |
| 类型检查 | [mypy](https://mypy.readthedocs.io) |
| 代码格式化 | [Ruff](https://docs.astral.sh/ruff/)（替代 black + isort + flake8） |
