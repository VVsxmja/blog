---
title: 使用 Netdata 来方便地查看开发机的系统指标
authors:
  - me
---

Time to say goodbye to `htop` and `dstat` .

<!-- truncate -->

---

不知道大伙在 Linux 系统上进行开发的时候，有没有过这样的体验：

- 我跑的某个工具卡住了，但我不知道它是在计算，还是在进行网络传输，还是挂了。
- 我的某个程序跑的很慢，但我不知道它 Bound 在 IO 还是 CPU 还是别的地方。

如果你之前只接触过 Windows 系统，那么这时你应该会下意识地寻找「任务管理器」这种东西。你可能会找到 `htop` ，或者（如果你在使用图形见面）某个桌面环境的任务管理器。

这些程序提供了最基本的系统指标，例如 CPU 利用率、内存利用率等。但当我们遇到一些比较复杂/玄学的问题时，我们可能会想：如果能看到更详细的系统指标，说不定能找到什么解决问题的线索。

比较有经验的 Linux 用户此时会转而使用 `dstat` 等工具，这些工具能够看到更加细节、一般的「任务管理器」不会展示的指标。

但这些工具仍然存在「不够开箱即用」，「难以回放历史记录」等问题，例如初次使用 `dstat` 的用户可能会遇到下面的困难：

- `dstat` 的默认输出中是不包含时间信息的，需要通过加上 `-t` 来在每行输出中附加时间信息。
- `dstat` 默认输出到命令行，缺少回看功能，需要通过加上 `-o ...` 来在打印命令行输出的同时输出到 CSV 文件。
- `dstat` 不支持在运行时更改采样间隔，只能对着 CSV 去 Sample 。

另外，相信大伙在项目中工作时，往往会发出「线上的基建，如果能在我的开发机上也布一套就好了」的感慨，在 CI 或是在生产部署上可以访问到的详细的监控，和我们开发机上可怜的寥寥几项指标相比，就像是奥迪和奥迪双钻的区别。

有没有一套开箱即用的监控服务，能很方便地在我的开发机上布起来，能够解决上面的所有痛点，并且也具备接入其他应用的能力？

答案是有的，请看 [Netdata](https://www.netdata.cloud/) 。

# 如果你没有听说过 Netdata

[Netdata](https://www.netdata.cloud/) 是一款很强大的[开源](https://github.com/netdata/netdata)监控工具。

百闻不如一见，Netdata 官方在世界各地都布了 Netdata Demo ，大伙可以直接点进去看看 Netdata 长什么样。

| [FRANKFURT](https://frankfurt.netdata.rocks) | [NEWYORK](https://newyork.netdata.rocks) | [ATLANTA](https://atlanta.netdata.rocks) | [SANFRANCISCO](https://sanfrancisco.netdata.rocks) | [TORONTO](https://toronto.netdata.rocks) | [SINGAPORE](https://singapore.netdata.rocks) | [BANGALORE](https://bangalore.netdata.rocks) |
| --- | --- | --- | --- | --- | --- | --- |

![Netdata Agent](https://github.com/netdata/netdata/assets/2662304/af4caa23-19be-46ef-9779-8fdad8d99d2a)

Netdata 除了自身具有观测系统指标的能力之外，同时也支持接入其他应用（例如 OpenMetrics 和 StatsD）以拓展它的能力。比如，通过[这个插件](https://github.com/netdata/netdata/tree/master/src/collectors/systemd-journal.plugin)，你可以在 Netdata 中查看 `systemd` 的 Journal Logs 了！

相信你此时已经按耐不住自己的心情，想要自己部署一套 Netdata 实例了！

