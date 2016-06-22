# 合作开发

# 贡献代码


核心开发团队和社区的联系信息可以在下面找到。PX4项目使用了三个分支Git branching model:  
- [master](https://github.com/px4/firmware/tree/master) 默认情况下不稳定，可以看到快速的开发。  
- [beta](https://github.com/px4/firmware/tree/beta) 已充分测试，面向飞行测试者。  
- [stable](https://github.com/px4/firmware/tree/stable) 指向最新的发布分支。  

我们尝试通过[rebases保持一个线性的历史](https://www.atlassian.com/git/tutorials/rewriting-history)，避免[Github flow](https://guides.github.com/introduction/flow/)。但是由于全球的开发队伍和快速的开发转移，我们会定期分类合并。

为了贡献新的功能，首先[注册Github账户](https://help.github.com/articles/signing-up-for-a-new-github-account/)，然后[fork](https://help.github.com/articles/fork-a-repo/)仓库，[创建新分支](https://help.github.com/articles/creating-and-deleting-branches-within-your-repository/)，加入你的改变，最后发送[pull request]。当它们通过我们的持续的[综合测试](https://en.wikipedia.org/wiki/Continuous_integration)，更新就会被合并。

所有的贡献必须在 [BSD 3-clause license](https://opensource.org/licenses/BSD-3-Clause)许可下进行,并且所有的代码在使用上不能提出任何的，进一步的限制。

## 测试飞行结果

飞行测试对于保证质量非常重要，请从microSD卡上传飞行日志到 [Log Muncher](http://logs.uaventure.com)，并在[论坛](http://groups.google.com/group/px4users)分享连接，附带有书面飞行报告。
Test flights are important for quality assurance. Please upload the logs from the microSD card to [Log Muncher](http://logs.uaventure.com) and share the link on the [forum](http://groups.google.com/group/px4users) along with a verbal flight report.

## 论坛和聊天

- [Google+](https://plus.google.com/117509651030855307398)
- [Gitter](https://gitter.im/PX4/Firmware?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
- [PX4 Users Forum](http://groups.google.com/group/px4users)

## 每周开发电话

PX4团队在每周同时进行电话联系（通过[Mumble](http://mumble.info) client)客户端连接）。

- TIME: 19:00h Zurich time, 1 p.m. Eastern Time, 10 a.m. Pacific Standard Time
- Server: mumble.dronecode.org
  - Port: 10028
  - Channel: PX4 Channel
  - The agenda is announced the same day on the [px4users forum](http://groups.google.com/group/px4users)
