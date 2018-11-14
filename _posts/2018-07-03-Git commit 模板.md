```
# Type(<scope>): <subject>
# eg.
# docs(README.md): A 新增 README.md文档
# style(Demo.jsx): U 更新 格式化代码
# feat(FoodMenu.jsx): A 新增 菜品多规格功能
# fix(FoodGroup.jsx): F 修复 新建菜品时,前端报promise错误(closes #123)
# refactor(ImportFoodExcel.jsx): U 更新 重构菜品导入功能

# git emoji 
# https://github.com/leesx/git-commit-emoji-cn/blob/master/README.md
# :art: 改进代码结构/代码格式 :zap: 提高性能 :fire: 移除代码 :bug: 修复bug :ambulance: 重要补丁 :sparkles: 引入新功能
# :memo: 撰写文档 :rocket: 部署功能 :lipstick: 更新UI和样式 :tada: 初次提交 :wrench: 修改配置文件 :hammer: 重大重构

# <body>
# Body 部分是对本次 commit 的详细描述，可以分成多行

# <footer>
# Footer用来关闭 Issue或以BREAKING CHANGE开头，后面是对变动的描述以及变动理由和迁移方法

# type 字段包含:
# feat：新功能（feature）
# fix：修补bug
# docs：文档（documentation）
# style： 格式（不影响代码运行的变动）
# refactor：重构（即不是新增功能，也不是修改bug的代码变动）
# test：增加测试
# chore：构建过程或辅助工具的变动
# scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等。
# subject是 commit 目的的简短描述，不超过50个字符

# 英文版详细例子，请看这里
# https://github.com/sparkbox/how_to/tree/master/style/git
# 中文版介绍，请看这里
# http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html

```
### emoji 指南

emoji                                   | emoji 代码                   | commit 说明
:--------                               | :--------                    | :--------
:art: (调色板)                          | `:art:`                      | 改进代码结构/代码格式
:zap: (闪电)                            | `:zap:`                      | 提高性能
:fire: (火焰)                           | `:fire:`                     | 移除代码或文件
:bug: (bug)                             | `:bug:`                      | 修复 bug
:ambulance: (急救车)                    | `:ambulance:`                | 重要补丁
:sparkles: (火花)                       | `:sparkles:`                 | 引入新功能
:memo: (备忘录)                         | `:memo:`                     | 撰写文档
:rocket: (火箭)                         | `:rocket:`                   | 部署功能
:lipstick: (口红)                       | `:lipstick:`                 | 更新 UI 和样式文件
:tada: (庆祝)                           | `:tada:`                     | 初次提交
:white_check_mark: (白色复选框)         | `:white_check_mark:`         | 增加测试
:lock: (锁)                             | `:lock:`                     | 修复安全问题
:apple: (苹果)                          | `:apple:`                    | 修复 macOS 下的问题
:penguin: (企鹅)                        | `:penguin:`                  | 修复 Linux 下的问题
:checkered_flag: (旗帜)                 | `:checked_flag:`             | 修复 Windows 下的问题
:bookmark: (书签)                       | `:bookmark:`                 | 发行/版本标签
:rotating_light: (警车灯)               | `:rotating_light:`           | 移除 linter 警告
:construction: (施工)                   | `:construction:`               | 工作进行中
:green_heart: (绿心)                    | `:green_heart:`              | 修复 CI 构建问题
:arrow_down: (下降箭头)                 | `:arrow_down:`               | 降级依赖
:arrow_up: (上升箭头)                   | `:arrow_up:`                 | 升级依赖
:construction_worker: (工人)            | `:construction_worker:`      | 添加 CI 构建系统
:chart_with_upwards_trend: (上升趋势图) | `:chart_with_upwards_trend:` | 添加分析或跟踪代码
:hammer: (锤子)                         | `:hammer:`                   | 重大重构
:heavy_minus_sign: (减号)               | `:heavy_minus_sign:`         | 减少一个依赖
:whale: (鲸鱼)                          | `:whale:`                    | Docker 相关工作
:heavy_plus_sign: (加号)                | `:heavy_plug_sign:`          | 增加一个依赖
:wrench: (扳手)                         | `:wrench:`                   | 修改配置文件
:globe_with_meridians: (地球)           | `:globe_with_meridians:`     | 国际化与本地化
:pencil2: (铅笔)                        | `:pencil2:`                  | 修复 typo

### 参考

- [gitmoji](https://github.com/carloscuesta/gitmoji/)
- [An emoji guide for your commit messages](https://gitmoji.carloscuesta.me/)
- [styleguide-git-commit-message](https://github.com/slashsBin/styleguide-git-commit-message)
- [emoji-cheat-sheet](http://www.webpagefx.com/tools/emoji-cheat-sheet/)
