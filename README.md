# etcd for LoongArch64

<p align="center"><a href="README.md">English</a> | <a href="README-zh.md">中文</a></p>

<p align="center"><img src="https://img.shields.io/badge/etcd%20LoongArch64%20%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%91%E8%A1%8C%E7%89%88-blue?logo=etcd&logoColor=white" alt="etcd LoongArch64 龙芯架构发行版"></p>

Build [etcd](https://github.com/etcd-io/etcd) binaries and Docker images for the **LoongArch64 (loong64)** architecture via CI/CD.

## How it works

A GitHub Actions workflow clones the specified etcd version, applies a patch to enable loong64 support (switches base image to `ghcr.io/loong64/debian:trixie-slim`), and cross-compiles with `GOOS=linux GOARCH=loong64` in a Debian 13 container. Target platform: `linux/loong64`.

## Branch naming

Push a branch named `loong64/<etcd-version>` (e.g. `loong64/v3.6.8`) to trigger a build.

## [Release](https://github.com/xuxiaowei-com-cn/etcd-loong64/releases)

Push a tag matching `release-loong64/<etcd-version>/<sequence>` (e.g. `release-loong64/v3.6.8/1-alpha.1`) to publish a GitHub Release with the built binaries and Docker images.

The suffix in the sequence indicates the release stage:

| Suffix  | Stage         |
|---------|---------------|
| `alpha` | Internal beta |
| `beta`  | Public beta   |
| `rc`    | Pre-release   |
| (none)  | Stable        |

## Verify releases

Releases are signed with GPG. Download the public key from [keys.openpgp.org](https://keys.openpgp.org) [F3693AB74BBA0D84C227AB34F3A4B5061568FC57](https://keys.openpgp.org/debug?q=F3693AB74BBA0D84C227AB34F3A4B5061568FC57)：

```shell
gpg --keyserver keys.openpgp.org --recv-keys F3693AB74BBA0D84C227AB34F3A4B5061568FC57
echo "F3693AB74BBA0D84C227AB34F3A4B5061568FC57:6:" | gpg --import-ownertrust
```

Each release includes a `signatures.tar.gz` containing detached signatures for all artifacts. To verify:

```shell
# Download signatures.tar.gz from the release, then:
tar -xzf signatures.tar.gz --strip-components=1
gpg --verify <file>.asc <file>
```

## License

[Apache License 2.0](LICENSE)
