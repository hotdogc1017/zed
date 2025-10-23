# SQL

SQL 文件由 [SQL 扩展](https://github.com/zed-extensions/sql) 处理。

- Tree-sitter：[nervenes/tree-sitter-sql](https://github.com/nervenes/tree-sitter-sql)

### 格式化

Zed 支持使用外部工具（如 [`sql-formatter`](https://github.com/sql-formatter-org/sql-formatter)）自动格式化 SQL。

1. 安装 `sql-formatter`：

```sh
npm install -g sql-formatter
```

2. 确认 `sql-formatter` 已在 PATH 中并检查版本：

```sh
which sql-formatter
sql-formatter --version
```

3. 在 Zed 中配置使用 `sql-formatter` 格式化：

```json [settings]
  "languages": {
    "SQL": {
      "formatter": {
        "external": {
          "command": "sql-formatter",
          "arguments": ["--language", "mysql"]
        }
      }
    }
  },
```

可根据需要将 `mysql` 替换为其他支持的方言（如 `duckdb`、`hive`、`mariadb`、`postgresql`、`redshift`、`snowflake`、`sqlite`、`spark` 等）。

上述设置可放在项目设置（`.zed/settings.json`）或用户设置（`~/.config/zed/settings.json`）。

### 高级格式化

`sql-formatter` 也支持[更多配置选项](https://github.com/sql-formatter-org/sql-formatter#configuration-options)。可在项目中创建 `.sql-formatter.json`：

```json [settings]
{
  "language": "postgresql",
  "tabWidth": 2,
  "keywordCase": "upper",
  "linesBetweenQueries": 2
}
```

使用 `.sql-formatter.json` 后，Zed 中的设置可以简化：

```json [settings]
  "languages": {
    "SQL": {
      "formatter": {
        "external": {
          "command": "sql-formatter"
        }
      }
    }
  },
```
