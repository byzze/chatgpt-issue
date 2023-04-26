# 安装教程
安装 wrk 的步骤如下：

1. 下载 wrk 的源代码。

   可以从 wrk 的 Github 仓库下载源代码：

   ```
   git clone https://github.com/wg/wrk.git
   ```

   或者直接从 wrk 的 releases 页面下载 pre-build 版本。

2. 安装依赖库。

   在 Ubuntu 系统中安装依赖库可以使用以下命令：

   ```
   sudo apt-get install build-essential libssl-dev git -y
   ```

3. 编译 wrk。

   进入 wrk 的源代码目录，运行以下命令：

   ```
   make
   ```

   编译完成后，会在当前目录下生成一个 `wrk` 可执行程序。

4. 将 `wrk` 程序复制到 `/usr/local/bin` 目录。

   运行以下命令进行复制：

   ```
   sudo cp wrk /usr/local/bin
   ```

5. 验证安装。

   运行以下命令：

   ```
   wrk -v
   ```

   如果输出版本号，则表示安装成功。

   ```
   wrk 4.1.0 (2018-12-05)
   Built with OpenSSL 1.1.1f  31 Mar 2020
   Copyright (c) 2011-2018 William Ahern
   ...
   ```
# 使用教程
使用 wrk 进行压力测试的基本命令格式如下：

```
wrk -c <concurrent> -t <threads> -d <duration> -s <script> <url>
```

参数说明：

- `-c`: 模拟并发连接数，默认为 1。
- `-t`: 使用多少个线程，默认为 1。
- `-d`: 测试持续时间，单位为秒，默认为 10 秒。
- `-s`: 执行 Lua 脚本，可以用于模拟复杂的业务场景。
- `<url>`: 需要测试的 URL。

例如，以下命令会模拟 10 个并发连接，使用 2 个线程，持续 30 秒，向 `http://localhost:8080/` 发送 HTTP GET 请求。

```
wrk -c 10 -t 2 -d 30s http://localhost:8080/
```

运行结束后，wrk 会输出测试结果，包括每秒钟的请求数、每次请求的平均延迟时间、99% 的请求延迟时间、错误请求数等信息。在测试中，应该尽量模拟真实的流量场景，以便更好地评估系统的性能。