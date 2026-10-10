<div align="center">

# MINEZ Citizens

A fork of [Citizens](https://github.com/CitizensDev/Citizens2) maintained for the MINEZ server.

<a href="README.md">English</a> ｜
<a href="docs/README_zh-CN.md">简体中文</a>

<br>

<div>
<a href="https://github.com/CitizensDev/Citizens2"><img src="docs/badges/upstream.svg" alt="upstream"></a>
<a href="#supported-versions"><img src="https://img.shields.io/badge/Minecraft-3%20versions-4caf50" alt="Minecraft"></a>
<a href="LICENSE"><img src="https://img.shields.io/badge/license-OSL--3.0-9e9e9e" alt="license"></a>
</div>

<div>
<img src="https://img.shields.io/badge/26.1.2-81c784" alt="26.1.2">
<img src="https://img.shields.io/badge/26.2-81c784" alt="26.2">
<img src="https://img.shields.io/badge/26.3-81c784" alt="26.3">
</div>

<br>

<a href="https://wiki.citizensnpcs.co/Citizens_Wiki">Documentation</a> ｜
<a href="https://github.com/MINEZ/Citizens/issues">Issue Tracker</a> ｜
<a href="https://github.com/MINEZ/Citizens/releases">Releases</a> ｜
<a href="#changes-from-upstream">What's Changed</a>

</div>

> [!IMPORTANT]
> **This is an unofficial fork and is not affiliated with, endorsed by, or supported by the Citizens developers.**
>
> Never take anything about this fork to the upstream repository or the official Discord. Use [this repository's issue tracker](https://github.com/MINEZ/Citizens/issues) instead.
>
> To tell the two apart, reproduce the problem on an unmodified upstream build first:
>
> - **It still happens without this fork** — an upstream problem. Report it upstream as you normally would, with no mention of this fork.
> - **It does not happen without this fork** — ours. It belongs here, and only here.

## About

This repository tracks the upstream `master` branch and adds a small set of changes needed by the MINEZ server. Everything not listed below is identical to upstream, so the [Citizens Wiki](https://wiki.citizensnpcs.co/Citizens_Wiki) and the [Javadoc](https://jd.citizensnpcs.co/) apply unchanged.

### Supported Versions

Only the Spigot versions shown at the top of this page are built by default — the same set as upstream's default `spigot-release` profile. Older versions remain in the source tree under upstream's `full` profile, but this fork neither builds nor tests them.

Our commits follow upstream by rebase, so the history stays linear:

```bash
git fetch upstream
git rebase upstream/master
```

Because rebasing rewrites commit hashes, pushing after a sync requires `git push origin master --force-with-lease`.

## Changes from Upstream

| Name | Type | Change |
| --- | --- | --- |
| Mannequin NPCs on Paper 26.3 | Behaviour | Fixed |

### Fixes

- **Mannequin NPCs crashed the server thread on Paper 26.3.** Paper 26.3 overrides `LivingEntity#getBukkitEntity` with a narrower return type, which makes the compiler emit a bridge method that dispatches back to the most specific override. Citizens is compiled against Spigot, where that override does not exist, so the mannequin's own override called `super.getBukkitEntity()` expecting the base implementation and landed in Paper's bridge instead — which called the mannequin's override again, until the stack overflowed. Spawning a mannequin NPC was enough to trigger it. The override now reads and writes the entity's `bukkitEntity` field directly and never calls the superclass method.

## Building

Requires JDK 25 and the listed Spigot versions installed via [BuildTools](https://www.spigotmc.org/wiki/buildtools/).

```bash
mvn clean package -DBUILD_NUMBER=<upstream build>.<fork revision>
```

To build against 26.3 only, which needs nothing but Spigot 26.3 installed, add `-P dev`.

The result lands in `dist/target/`, named `Citizens-<version>-b<upstream build>.<fork revision>.jar`.

## Versioning

The project version follows upstream unchanged. Fork revisions are recorded in the build number instead: in `Citizens-2.0.44-b<upstream build>.1`, the upstream build is the [upstream CI](https://ci.citizensnpcs.co/job/Citizens2/) build this is based on and the trailing `.1` is the fork revision. This keeps our version numbers from colliding with upstream releases and keeps `pom.xml` free of conflicts when syncing.

The download links on the upstream project point at the official CI and do **not** include these changes.

## Related Repositories

| Repository | Purpose |
| --- | --- |
| [MINEZ/Denizen](https://github.com/MINEZ/Denizen) | Scripting engine, whose NPC features are built on Citizens |

## Upstream

Citizens is developed by [fullwall and the Citizens contributors](https://github.com/CitizensDev/Citizens2/graphs/contributors). For using the plugin itself, the [Citizens Wiki](https://wiki.citizensnpcs.co/Citizens_Wiki) and the [Discord](https://discord.gg/Q6pZGSR) remain the right places to go — for Citizens as upstream ships it, not for anything in this fork.

The changes here are built for one server's needs and are maintained separately. Please keep discussion of them out of upstream's channels.

## License

Citizens is licensed under the Open Software License 3.0. Modifications in this fork are provided under the same terms. See [LICENSE](LICENSE) for the full text.

Under OSL-3.0, anyone who distributes a build of this fork — or offers it to others over a network — must make the corresponding source available under the same license. This repository serves that purpose.
