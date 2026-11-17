<div align="center">

# MINEZ Citizens

面向 MINEZ 服务器维护的 [Citizens](https://github.com/CitizensDev/Citizens2) Fork。

<a href="../README.md">English</a> ｜
<a href="README_zh-CN.md">简体中文</a>

<br>

<div>
<a href="https://github.com/CitizensDev/Citizens2"><img src="badges/upstream.svg" alt="upstream"></a>
<a href="#受支持的版本"><img src="https://img.shields.io/badge/Minecraft-3%20versions-4caf50" alt="Minecraft"></a>
<a href="../LICENSE"><img src="https://img.shields.io/badge/license-OSL--3.0-9e9e9e" alt="license"></a>
</div>

<div>
<img src="https://img.shields.io/badge/26.1.2-81c784" alt="26.1.2">
<img src="https://img.shields.io/badge/26.2-81c784" alt="26.2">
<img src="https://img.shields.io/badge/26.3-81c784" alt="26.3">
</div>

<br>

<a href="https://wiki.citizensnpcs.co/Citizens_Wiki">查阅文档</a> ｜
<a href="https://github.com/MINEZ/Citizens/issues">问题反馈</a> ｜
<a href="https://github.com/MINEZ/Citizens/releases">版本发布</a> ｜
<a href="#相对上游的改动">改动一览</a>

</div>

> [!IMPORTANT]
> **本项目是非官方 Fork，与 Citizens 开发者没有关系，既未经其认可，也不由其提供支持。**
>
> 与本 Fork 有关的一切，请提交至[本仓库的 issue](https://github.com/MINEZ/Citizens/issues)，不要带到上游仓库或官方 Discord。
>
> 区分方法是先在未经修改的上游构建上复现：
>
> - **脱离本 Fork 仍会出现** —— 属于上游的问题，照常向上游反馈，不必也不要提及本 Fork。
> - **脱离本 Fork 便不再出现** —— 属于我们的问题，只应提交到这里。

## 关于

本仓库跟随上游 `master` 分支，仅承载 MINEZ 服务器所需的少量改动。除下文列出的内容外，其余部分与上游完全一致，因此 [Citizens Wiki](https://wiki.citizensnpcs.co/Citizens_Wiki) 与 [Javadoc](https://jd.citizensnpcs.co/) 同样适用。

### 受支持的版本

默认仅构建本页顶部列出的这几个 Spigot 版本，与上游默认的 `spigot-release` 配置一致。更早的版本仍保留在源码中，归上游的 `full` 配置构建，但本 Fork 既不构建也不测试。

我们的提交以变基方式跟随上游，历史保持线性：

```bash
git fetch upstream
git rebase upstream/master
```

变基会重写提交哈希，因此同步后推送需要 `git push origin master --force-with-lease`。

## 相对上游的改动

| 名称 | 类型 | 改动 |
| --- | --- | --- |
| Paper 26.3 上的 mannequin NPC | 行为 | 修复 |

### 修复

- **mannequin NPC 在 Paper 26.3 上会让服务器主线程崩溃。** Paper 26.3 以更窄的返回类型覆写了 `LivingEntity#getBukkitEntity`，编译器随之生成一个桥接方法，转而调用最具体的那个覆写。Citizens 是对着 Spigot 编译的，Spigot 中没有这个覆写，于是 mannequin 自己的覆写调用 `super.getBukkitEntity()` 时，本想调用基类实现，实际进入的却是 Paper 的桥接方法——后者又回头调用 mannequin 的覆写，如此往复，直至栈溢出。生成一个 mannequin NPC 就足以触发。现改为直接读写实体的 `bukkitEntity` 字段，不再调用父类方法。上游随后也改写了这一方法，但仍会调用 `super.getBukkitEntity()`，在 Paper 上照旧栈溢出。

## 构建

需要 JDK 25，并通过 [BuildTools](https://www.spigotmc.org/wiki/buildtools/) 构建全部所列的 Spigot 版本。

```bash
mvn clean package -DBUILD_NUMBER=<上游构建号>.<Fork 修订号>
```

若只针对 26.3 构建，加上 `-P dev` 即可，此时只需装好 Spigot 26.3。

产物位于 `dist/target/`，文件名形如 `Citizens-<版本号>-b<上游构建号>.<Fork 修订号>.jar`。

构建时还会按 `pom.xml` 中的版本号重新生成本页顶部的 upstream 徽章，无须手动维护。上游提升版本号后，请将重新生成的 `docs/badges/upstream.svg` 随同步一并提交。

每次推送到 `master` 也会由 [GitHub Actions](https://github.com/MINEZ/Citizens/actions) 构建：以 BuildTools 生成各 Spigot 版本并缓存，插件 jar 上传为 Artifact。发 Release 时手动运行这个工作流并填入构建号，Release 附带的就是它构建的 jar。每周一次的定时运行使缓存不会过期。

## 版本号

项目版本号沿用上游，不作改动。Fork 的修订信息记录在构建号中：在 `Citizens-2.0.44-b<上游构建号>.1` 中，上游构建号是所基于的[上游 CI](https://ci.citizensnpcs.co/job/Citizens2/) 构建，末尾的 `.1` 是本 Fork 的修订号。这样既不会与上游的版本号撞车，同步上游时也不会在 `pom.xml` 上产生冲突。

上游项目页面上的下载链接指向官方 CI，**不包含**本 Fork 的改动。

## 相关仓库

| 仓库 | 用途 |
| --- | --- |
| [MINEZ/Denizen](https://github.com/MINEZ/Denizen) | 脚本引擎，其 NPC 功能构建于 Citizens 之上 |

## 上游

Citizens 由 [fullwall 及各位贡献者](https://github.com/CitizensDev/Citizens2/graphs/contributors)开发。使用插件本身的问题，仍应前往 [Citizens Wiki](https://wiki.citizensnpcs.co/Citizens_Wiki) 与官方 [Discord](https://discord.gg/Q6pZGSR)——但仅限于上游原本提供的内容，不包括本 Fork 的任何改动。

此处的改动为单一服务器的需求而作，独立维护。请勿将相关讨论带入上游的各个渠道。

## 许可证

Citizens 以 Open Software License 3.0 开源。本 Fork 的修改同样适用该许可证。完整条款见 [LICENSE](../LICENSE)。

依照 OSL-3.0，凡是分发本 Fork 的构建，或通过网络向他人提供其功能，都须以同一许可证公开相应的源码。本仓库即用于此。
