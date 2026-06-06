# etcd for LoongArch64

<p align="center"><a href="README.md">English</a> | <a href="README-zh.md">中文</a></p>

<p align="center"><img src="https://img.shields.io/badge/etcd%20LoongArch64%20%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%91%E8%A1%8C%E7%89%88-blue?logo=etcd&logoColor=white" alt="etcd LoongArch64 龙芯架构发行版"></p>

通过 CI/CD 构建 [etcd](https://github.com/etcd-io/etcd) 的 **LoongArch64 (loong64)** 架构二进制文件和 Docker 镜像。

## 工作原理

GitHub Actions 工作流克隆指定的 etcd 版本，打上 loong64 适配补丁（将基础镜像替换为 `ghcr.io/loong64/debian:trixie-slim`），在
Debian 13 容器中使用 `GOOS=linux GOARCH=loong64` 交叉编译。目标平台：`linux/loong64`。

关于 Debian 13
容器选型的理由，详见 [Discussion #6 — 为什么使用 container: debian:13？](https://github.com/orgs/kubernetes-loong64/discussions/6)。

## 分支命名

推送 `loong64-<etcd 版本>` 格式的分支（如 `loong64-v3.6.8`）即可触发构建。可追加 `+<build>`
（如 `loong64-v3.6.8+0`）携带构建元数据。

## [发布](https://github.com/kubernetes-loong64/etcd-loong64/releases)

推送 `release-loong64-<etcd 版本>` 格式的标签（如 `release-loong64-v3.6.8+0`）即可自动创建 GitHub
Release 并上传构建产物和 Docker 镜像。

`+<build>` 后缀提供构建元数据（如 `+0`、`+1-alpha.1`）。在 git clone 时会去除该后缀，在 Docker 标签中 `+` 会替换为 `-`。

后缀表示发布阶段：

| 后缀      | 阶段   |
|---------|------|
| `alpha` | 内测版  |
| `beta`  | 公测版  |
| `rc`    | 预发布版 |
| （无后缀）   | 正式版  |

## 发布产物

每个发布包含以下文件：

| 文件                 | 描述            |
|--------------------|---------------|
| `etcd`             | etcd 服务端二进制文件 |
| `etcdctl`          | etcd 命令行客户端   |
| `etcdutl`          | etcd 实用工具     |
| `etcd-loong64.tar` | Docker 镜像压缩包  |

每个文件都有对应的 `.asc` 分离 GPG 签名。

Docker 镜像推送至：

- [![kubernetesloong64/etcd](https://img.shields.io/docker/v/kubernetesloong64/etcd?sort=semver&arch=loong64&logo=docker&label=kubernetesloong64%2Fetcd)](https://hub.docker.com/r/kubernetesloong64/etcd/tags)
- [![kubernetesloong64/etcd-loong64](https://img.shields.io/docker/v/kubernetesloong64/etcd-loong64?sort=semver&arch=loong64&logo=docker&label=kubernetesloong64%2Fetcd-loong64)](https://hub.docker.com/r/kubernetesloong64/etcd-loong64/tags)

| 镜像                                     | 描述                    |
|----------------------------------------|-----------------------|
| `kubernetesloong64/etcd:<tag>`         | K8s 兼容标签（仅正式版，固定带 -0） |
| `kubernetesloong64/etcd:<tag>`         | 带构建元数据的标准标签           |
| `kubernetesloong64/etcd-loong64:<tag>` | 含 loong64 架构后缀的标签     |

`release-loong64-v3.6.8+0` 发布示例：

```
kubernetesloong64/etcd:v3.6.8-0                 # K8s 兼容标签（仅正式版，固定带 -0）
kubernetesloong64/etcd:v3.6.8-0                 # 带构建元数据的标准标签
kubernetesloong64/etcd-loong64:v3.6.8-0-loong64 # 含 loong64 架构后缀的标签
```

## 验证发布

- 发布文件使用 GPG 签名。
- 从 [keys.openpgp.org](https://keys.openpgp.org) 下载公钥。
- [FCF8724722CCBF9F51B1FBE376532BE7E3013105](https://keys.openpgp.org/debug?q=FCF8724722CCBF9F51B1FBE376532BE7E3013105)

```shell
gpg --keyserver keys.openpgp.org --recv-keys FCF8724722CCBF9F51B1FBE376532BE7E3013105
echo "FCF8724722CCBF9F51B1FBE376532BE7E3013105:6:" | gpg --import-ownertrust
```

或者，手动下载公钥文件后导入：

```shell
gpg --import /tmp/xxx
```

每个发布产物都有对应的 `.asc` 分离签名。验证时，从发布页面下载文件和对应的 `.asc` 签名文件，然后：

```shell
gpg --verify <文件>.asc <文件>
```

## 许可证

[Apache License 2.0](LICENSE)
