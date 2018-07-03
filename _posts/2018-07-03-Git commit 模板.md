```
# Type(<scope>): <subject>

# <body>

# <footer>

# eg.
# (docs)(README.md): A 新增 README.md文档
# (style)(Demo.jsx): U 更新 格式化代码
# (feat)(FoodMenu.jsx): A 新增 菜品多规格功能
# (fix)(FoodGroup.jsx): F 修复 新建菜品时,前端报promise错误(closes #123)

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
# Body 部分是对本次 commit 的详细描述，可以分成多行
# Footer用来关闭 Issue或以BREAKING CHANGE开头，后面是对变动的描述、
#       以及变动理由和迁移方法

# 英文版详细例子，请看这里
# https://github.com/sparkbox/how_to/tree/master/style/git
# 中文版介绍，请看这里
# http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html

```
