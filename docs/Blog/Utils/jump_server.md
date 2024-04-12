在服务器上下载时，常常遇到下载缓慢的问题，这个时候如果自己有另外一个跳板机，可以用跳板机来下载

## 基本说明

1. 首先，你需要在本地机器和跳板机之间建立一个SSH连接，并设置端口转发。你可以使用以下命令：

```bash
ssh -L local_port:destination_server:destination_port username@jump_server
```

其中：

- `local_port` 是你本地机器上的端口，你将通过这个端口访问目标服务器。
- `destination_server` 和 `destination_port` 是目标服务器的地址和端口。
- `username@jump_server` 是你的用户名和跳板机的地址。

2. 当你运行上述命令后，你的本地机器会和跳板机建立一个SSH连接，并且所有发送到 `local_port` 的数据都会被转发到 `destination_server:destination_port`。

3. 然后，你可以在本地机器上运行下载命令，例如 `wget` 或 `curl`，并将 `localhost:local_port` 作为目标地址。例如，如果你想使用 `wget` 来下载一个文件，你可以运行以下命令：

```bash
wget http://localhost:local_port/path_to_file
```

## 举例

### 场景

我的跳板机是ifyou.tech，用户名是root，要下载的文件链接是https://dumps.wikimedia.org/zhwiki/20240401/zhwiki-20240401-pages-articles-multistream.xml.bz2

### 具体操作

首先，你需要在你的本地机器和跳板机之间建立一个SSH连接，并设置端口转发。你可以使用以下命令：

```bash
ssh -L 8080:dumps.wikimedia.org:443 root@ifyou.tech
```

这条命令会在本地机器的8080端口和dumps.wikimedia.org的443端口（HTTPS默认端口）之间建立一个SSH隧道。所有发送到本地机器的8080端口的数据都会被转发到dumps.wikimedia.org的443端口。

然后，你需要在一个新的终端窗口或者标签页中运行以下命令来下载文件：

```bash
wget --no-check-certificate https://localhost:8080/zhwiki/20240401/zhwiki-20240401-pages-articles-multistream.xml.bz2
```

这条命令会通过SSH隧道来从dumps.wikimedia.org下载文件。`--no-check-certificate`选项是因为我们现在是通过localhost和一个非标准的端口来访问dumps.wikimedia.org，所以wget会警告我们证书不匹配。如果你有适当的证书，或者你可以接受这个风险，你可以使用这个选项来忽略警告。

请注意，你可能需要输入你在跳板机上的密码来建立SSH连接。如果你配置了SSH密钥认证，你可以使用`-i`选项来指定你的私钥文件，例如`ssh -i /path/to/your/private/key -L 8080:dumps.wikimedia.org:443 root@ifyou.tech`。
