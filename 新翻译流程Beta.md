# 新翻译流程 Beta

## issue (旧)流程的简单复盘

使用 `issue` 来管理整套流程会带来以下痛点：

1. **每一次校对改动的不透明化**：对于 issue 改动，如果校对人员直接对 issue 的 `comment` 进行修改，无法看到其修改历史和修改的具体内容及对比（改前片段&改后片段）；而如果在 comment 增加改动建议，则会对终稿中是否已经完成修改造成困扰，无法快速定位；
2. 当前 CMB 在文章定稿时无法快速定位到哪一篇是待发布文章 容易引起混乱；
3. 缺少翻译者和校对者的沟通与交流记录，对于翻译者翻译水平的提高不友好；
4. 上线后的文章修改流程困难且复杂，发布者无法再次重新进行快速挽救以及追责；

为了解决以上痛点，经校对成员讨论希望引入 `git flow` 流程，以项目开发中传统的代码Review流程，来管理博客翻译、校对、发布流程；

## 对于 GitHub 各个模块的分类

![Apple TV](image/15321387057664/Apple%20TV.png)

**issue**：
1.所有成员均有编辑权限。当发现待翻译文章，可以直接发布新的issue，将新文章的超链接加入文章池中。
加入文章的规则如下：
- 需要添加中文标题(# 标题)
- 需要添加原文创作时间
- 需要添加原文tag
- 需要添加categories
- 需要添加permalink
- 需要添加keywords 
- 需要添加custom_title 
- 需要添加description

具体说明参考[这个内容](https://raw.githubusercontent.com/SwiftGGTeam/translation/master/%E4%B9%A6%E5%86%99%E8%A7%84%E8%8C%83%E5%8F%8ADemo/SwiftGG%E5%8D%9A%E6%96%87%E4%B9%A6%E5%86%99%E8%A7%84%E8%8C%83.md)。

完整示例参考[这个文件](https://raw.githubusercontent.com/SwiftGGTeam/translation/master/%E4%B9%A6%E5%86%99%E8%A7%84%E8%8C%83%E5%8F%8ADemo/20160726_simple-barcode-reader-app-swift.md)。

**pull request**：
1.GitHub 的功能之一。这里我们将其定义成为一次翻译+校对的抽象。
2.翻译者通过临时分支提交翻译文件，发起 PR 对 stage 分支新增一个文件（翻译的文章），可以直接点击[create new file](https://github.com/SwiftGGTeam/GGHexo/new/stage/src)。
创建临时分支规则如下：
- 分支命名规则 [hostname]/[title]，host 为翻译的文章网站的除去协议部分，title 即为原文的英文标题
- 分支合并请求规则，必须完成整个翻译流程后才能提出 PR
- 分支处理规则，翻译完后对校对者提出的问题应在自己的分支上作出对应的修改

3.校对同学在翻译的 PR 中进行 comment，并在上面指出问题和修改方案，由 commit 提交者(即翻译者)继续修改。
校对规则如下：
- 校对分两个两个步骤进行，翻译者选择比较信任或合作较好的成员进行初次校对，Owner 进行二次校对
- 第一个校对的 assignee 由翻译者选取，Owner 为每一篇文章的默认 assignee
- 一校和二校都通过（Approve）则定稿的同学可以 Merge 该分支
- 所有的校对过程中校对者只是提供意见翻译的同学进行具体的修改，每个部分的修改一校和二校都应该知晓并被通过
- 校对完成后，需要在页面右上角点击绿色按钮“Review changes”，在下拉菜单中根据情况选择对应的项并“Submit review”。具体来说，如果内容需要修改，选择”Request changes“；如果内容没问题，选择”Approve“

**repository**：master 分支仅存储已经发布的文章列表。
定稿规则：
- 确定发布后可将该分支删掉
- 后期文章发布后发现质量很低可以 revert PR 相当于打回重新做一遍

## 整体流程梳理

![流程](image/15321387057664/%E6%B5%81%E7%A8%8B.png)

1. 领取流程：issue 作为文章池，与原先的抓取文章保持一致。
2. 临时分支定义：仅仅用来对 master 分支发起 PR，在整个流程结束后（Merge 进 master 分支）将临时分支删除。
3. 翻译完成后，对 repo 增加文件，发起 PR。然后进入校对流程，校对者对已经翻译文章的PR进行 comment 修改意见，由翻译者在 PR 中追加 commit 进行翻译修改。完成修改后确认修改无误对修改段落进行折叠。
4. 确认校对完成：当校对者完成校对工作后，在 comment 中回复 OK。每当一个 PR 获得两个 OK 后，方可由发布者 Merge。
5. 发布流程：当发布者需要当日更新文章，则从 PR 列表中对要发布的文章 PR 进行 merge，并进行主站发布工作。
6. 若上线文章出现问题，可从之前 merge 的 PR 中直接进行 Revert 操作，将该次翻译判为无效并对整个 flow 的经手人进行追责。若想再次上线该文章，需要重新发起 PR。

## Q & A

### Q：会不会带来和代码 merge 同样的问题，多分支 merge 造成的冲突？

A：不会。因为翻译流程 PR 只对 master 仓库进行 Add 文件操作，所以不会带来多人同事修改一个文件的情况。

### Q：对于 Git 操作不熟悉的同学学习成本会不会比较大？

A：Git 属于分布式版本管理工具，我个人认为是每个程序员的必备技能之一。这里推荐一个教程 [git-recipes](https://github.com/geeeeeeeeek/git-recipes)。后期我会录制一个视频来讲解整个过程。

> 有不懂的问题可以，可以随时联系我([冬瓜](https://github.com/Desgard))。
