# etcd for LoongArch64

<p align="center"><a href="README.md">English</a> | <a href="README-zh.md">中文</a></p>

<p align="center"><img src="https://img.shields.io/badge/etcd%20LoongArch64%20%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%91%E8%A1%8C%E7%89%88-blue?logo=etcd&logoColor=white" alt="etcd LoongArch64 龙芯架构发行版"></p>

Build [etcd](https://github.com/etcd-io/etcd) binaries and Docker images for the **LoongArch64 (loong64)** architecture
via CI/CD.

## How it works

A GitHub Actions workflow clones the specified etcd version, applies a patch to enable loong64 support (switches base
image to `ghcr.io/loong64/debian:trixie-slim`), and cross-compiles with `GOOS=linux GOARCH=loong64` in a Debian 13
container. Target platform: `linux/loong64`.

See [Discussion #6 — Why Use container: debian:13?](https://github.com/orgs/kubernetes-loong64/discussions/6) for the
rationale behind the Debian 13 container choice.

## Branch naming

Push a branch named `loong64-<etcd-version>` (e.g. `loong64-v3.6.8`) to trigger a build.

## [Release](https://github.com/xuxiaowei-com-cn/etcd-loong64/releases)

Push a tag matching `release-loong64-<etcd-version>+<sequence>` (e.g. `release-loong64-v3.6.8+1-alpha.1`) to publish a
GitHub Release with the built binaries and Docker images.

The suffix in the sequence indicates the release stage:

| Suffix  | Stage         |
|---------|---------------|
| `alpha` | Internal beta |
| `beta`  | Public beta   |
| `rc`    | Pre-release   |
| (none)  | Stable        |

## Release artifacts

Each release includes the following files:

| File               | Description              |
|--------------------|--------------------------|
| `etcd`             | etcd server binary       |
| `etcdctl`          | etcd command line client |
| `etcdutl`          | etcd utility tool        |
| `etcd-loong64.tar` | Docker image tarball     |

Each file has a corresponding `.asc` detached GPG signature.

Docker images are pushed to:

- [![kubernetesloong64/etcd](https://img.shields.io/docker/v/kubernetesloong64/etcd?sort=semver&arch=loong64&logo=docker&label=kubernetesloong64%2Fetcd)](https://hub.docker.com/r/kubernetesloong64/etcd/tags)
- [![kubernetesloong64/etcd-loong64](https://img.shields.io/docker/v/kubernetesloong64/etcd-loong64?sort=semver&arch=loong64&logo=docker&label=kubernetesloong64%2Fetcd-loong64)](https://hub.docker.com/r/kubernetesloong64/etcd-loong64/tags)

| Image                                  | Description            |
|----------------------------------------|------------------------|
| `kubernetesloong64/etcd-loong64:<tag>` | Image with loong64 tag |
| `kubernetesloong64/etcd:<tag>`         | Standard image tag     |

Example for a release:

```
kubernetesloong64/etcd:v3.6.8-0
kubernetesloong64/etcd-loong64:v3.6.8-0-loong64
```

## Verify releases

- Releases are signed with GPG.
- Download the public key from [keys.openpgp.org](https://keys.openpgp.org).
- [FCF8724722CCBF9F51B1FBE376532BE7E3013105](https://keys.openpgp.org/debug?q=FCF8724722CCBF9F51B1FBE376532BE7E3013105)

```shell
gpg --keyserver keys.openpgp.org --recv-keys FCF8724722CCBF9F51B1FBE376532BE7E3013105
echo "FCF8724722CCBF9F51B1FBE376532BE7E3013105:6:" | gpg --import-ownertrust
```

Each release artifact has a corresponding `.asc` detached signature. To verify, download both the file and its `.asc`
signature from the release, then:

```shell
gpg --verify <file>.asc <file>
```

## License

[Apache License 2.0](LICENSE)
