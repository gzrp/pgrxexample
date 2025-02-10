
pgrx是一个用于在Rust中开发PostgreSQL扩展的框架。

关键特性：

- 提供 cargo pgrx 工具管理开发环境
- Postgres 多版本支持，支持 Postgres 12 ~ Postgres 17
- 自动模式生成
- 安全
- 支持 UDF/Trigger/CustomType/operators
- 方便访问SPI
- 支持 Shared Memory

安装依赖

```bash
apt-get install -y git
apt install libclang-dev
sudo apt-get install build-essential libreadline-dev zlib1g-dev flex bison libxml2-dev libxslt-dev libssl-dev libxml2-utils xsltproc ccache pkg-config
```

安装 pgrx

```bash
cargo install --locked cargo-pgrx
```


初始化环境：init命令下载所有当前支持的PostgreSQL版本，编译到${PGRX_HOME}，然后运行initdb。当pgrx支持新的Postgres版本，重新运行pgrx init来下载并编译新的版本.
```bash
cargo pgrx init
```

Postgres 与 Rust 类型对照表

| Postgres Type              | Rust Type (as `Option<T>`)                              |
|----------------------------|---------------------------------------------------------|
| `bytea`                    | `Vec<u8>` or `&[u8]` (zero-copy)                        |
| `text`                     | `String` or `&str` (zero-copy)                          |
| `varchar`                  | `String` or `&str` (zero-copy) or `char`                |
| `"char"`                   | `i8`                                                    |
| `smallint`                 | `i16`                                                   |
| `integer`                  | `i32`                                                   |
| `bigint`                   | `i64`                                                   |
| `oid`                      | `u32`                                                   |
| `real`                     | `f32`                                                   |
| `double precision`         | `f64`                                                   |
| `bool`                     | `bool`                                                  |
| `json`                     | `pgrx::Json(serde_json::Value)`                         |
| `jsonb`                    | `pgrx::JsonB(serde_json::Value)`                        |
| `date`                     | `pgrx::Date`                                            |
| `time`                     | `pgrx::Time`                                            |
| `timestamp`                | `pgrx::Timestamp`                                       |
| `time with time zone`      | `pgrx::TimeWithTimeZone`                                |
| `timestamp with time zone` | `pgrx::TimestampWithTimeZone`                           |
| `anyarray`                 | `pgrx::AnyArray`                                        |
| `anyelement`               | `pgrx::AnyElement`                                      |
| `box`                      | `pgrx::pg_sys::BOX`                                     |
| `point`                    | `pgrx::pg_sys::Point`                                   |
| `tid`                      | `pgrx::pg_sys::ItemPointerData`                         |
| `cstring`                  | `&core::ffi::CStr`                                      |
| `inet`                     | `pgrx::Inet(String)` -- TODO: needs better support      |
| `numeric`                  | `pgrx::Numeric<P, S> or pgrx::AnyNumeric`               |
| `void`                     | `()`                                                    |
| `ARRAY[]::<type>`          | `Vec<Option<T>>` or `pgrx::Array<T>` (zero-copy)        |
| `int4range`                | `pgrx::Range<i32>`                                      |
| `int8range`                | `pgrx::Range<i64>`                                      |
| `numrange`                 | `pgrx::Range<Numeric<P, S>>` or `pgrx::Range<AnyRange>` |
| `daterange`                | `pgrx::Range<pgrx::Date>`                               |
| `tsrange`                  | `pgrx::Range<pgrx::Timestamp>`                          |
| `tstzrange`                | `pgrx::Range<pgrx::TimestampWithTimeZone>`              |
| `NULL`                     | `Option::None`                                          |
| `internal`                 | `pgrx::PgBox<T>` where `T` is any Rust/Postgres struct  |
| `uuid`                     | `pgrx::Uuid([u8; 16])`                                  |


pgrx 命令详解

创建新的扩展项目
cargo pgrx new my_extension

cd my_extension
$ tree
.
├── Cargo.toml
├── my_extension.control
├── sql
└── src
└── lib.rs

运行扩展，并且在 psql 上进行交互式测试,这将扩展编译为共享库，将其复制到指定的Postgres安装中，启动该Postgres实例并将您连接到与扩展名称相同的数据库。
一旦cargo-pgrx将我们放入psql中，我们就可以加载扩展并对示例函数执行SELECT。
cargo pgrx run

my_extension=# CREATE EXTENSION my_extension;
CREATE EXTENSION

my_extension=# SELECT hello_my_extension();
hello_my_extension
---------------------
Hello, my_extension


cargo pgrx 详解

https://github.com/pgcentralfoundation/pgrx/blob/develop/cargo-pgrx/README.md




