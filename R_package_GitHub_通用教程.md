# 从零建立 R Package 并上传到 GitHub：通用操作教程

本教程面向第一次整理科研代码、建立 R package 并上传到 GitHub 的读者。目标是让读者能够独立完成：

1. 建立标准 R package 项目；
2. 整理 R 函数、帮助文档、README、vignette 和测试；
3. 使用 Git 管理版本；
4. 创建 GitHub 仓库并首次上传；
5. 后续修改代码后安全更新 GitHub；
6. 使用 GitHub Actions 自动检查 package；
7. 在需要时创建正式版本标签和 Release。

---

## 1. 开始前需要准备什么

建议安装：

- R
- RStudio
- Git
- GitHub 账号

在 R 中安装常用开发包：

```r
install.packages(c(
  "devtools",
  "usethis",
  "roxygen2",
  "testthat",
  "rmarkdown",
  "knitr"
))
```

检查 Git 是否已经安装。

在 Terminal 中运行：

```bash
git --version
```

如果显示类似：

```text
git version 2.xx.x
```

说明 Git 已经可以使用。

---

# 2. 建立一个新的 R Package

假设你想建立一个名为：

```text
myPackage
```

的 R package。

## 方法一：使用 RStudio

在 RStudio 中依次点击：

```text
File
→ New Project
→ New Directory
→ R Package
```

填写：

```text
Package name: myPackage
```

选择保存目录，然后创建项目。

## 方法二：使用 `usethis`

在 RStudio Console 中运行：

```r
usethis::create_package("~/myPackage")
```

创建完成后，RStudio 通常会自动打开新的 package 项目。

---

# 3. 理解 R Package 的基本目录结构

一个比较规范的 package 通常类似：

```text
myPackage/
├── DESCRIPTION
├── NAMESPACE
├── README.Rmd
├── README.md
├── LICENSE
├── R/
├── man/
├── tests/
│   └── testthat/
├── vignettes/
└── .Rbuildignore
```

各目录的作用如下。

| 文件或目录 | 作用 |
|---|---|
| `DESCRIPTION` | package 名称、版本、作者、依赖等基本信息 |
| `NAMESPACE` | 导出函数及包之间的命名空间关系 |
| `R/` | 存放正式 R 函数 |
| `man/` | 自动生成的帮助文档 |
| `tests/` | 自动测试 |
| `vignettes/` | 长篇教程、算法说明 |
| `README.Rmd` | README 源文件 |
| `README.md` | GitHub 实际显示的 README |
| `LICENSE` | 软件许可证 |
| `.Rbuildignore` | R package 构建时忽略的文件 |

---

# 4. 编辑 DESCRIPTION

打开：

```text
DESCRIPTION
```

一个简单示例：

```text
Package: myPackage
Title: A Short Description of the Package
Version: 0.1.0
Authors@R:
    person(
      given = "Your",
      family = "Name",
      email = "your@email.com",
      role = c("aut", "cre")
    )
Description: Provides functions for ...
License: MIT + file LICENSE
Encoding: UTF-8
Roxygen: list(markdown = TRUE)
RoxygenNote: 7.3.2
```

常见版本规则：

```text
0.1.0   第一个正式开发版本
0.1.1   小型 bug 修复
0.2.0   明显功能更新
1.0.0   相对成熟的正式版本
```

---

# 5. 添加许可证

科研软件常用 MIT License。

在 RStudio Console 中运行：

```r
usethis::use_mit_license("Your Name")
```

它会自动生成：

```text
LICENSE
LICENSE.md
```

---

# 6. 编写 R 函数

正式函数应存放在：

```text
R/
```

例如创建：

```text
R/add_numbers.R
```

内容：

```r
#' Add two numbers
#'
#' @param x A numeric value.
#' @param y A numeric value.
#'
#' @return The sum of x and y.
#' @export
#'
#' @examples
#' add_numbers(1, 2)
add_numbers <- function(x, y) {
  x + y
}
```

这里以 `#'` 开头的部分是 `roxygen2` 文档。

---

# 7. 自动生成帮助文档

函数写好以后，在 RStudio Console 中运行：

```r
devtools::document()
```

它会自动更新：

```text
NAMESPACE
man/
```

因此一般不要手工修改 `NAMESPACE` 和 `man/*.Rd`。

---

# 8. 本地加载并测试 package

运行：

```r
devtools::load_all()
```

然后测试函数：

```r
add_numbers(1, 2)
```

预期得到：

```text
[1] 3
```

---

# 9. 添加 README

运行：

```r
usethis::use_readme_rmd()
```

会生成：

```text
README.Rmd
```

README 通常至少应包含：

- package 的用途；
- 安装方法；
- 最小示例；
- 输入输出；
- 软件状态；
- License；
- 引用方法。

修改 `README.Rmd` 后运行：

```r
devtools::build_readme()
```

或：

```r
rmarkdown::render("README.Rmd")
```

生成：

```text
README.md
```

GitHub 首页主要显示 `README.md`。

推荐流程：

```text
修改 README.Rmd
        ↓
重新生成 README.md
        ↓
两者一起提交到 Git
```

---

# 10. 添加 vignette

vignette 适合写：

- 方法说明；
- 算法规范；
- 完整使用教程；
- 长篇示例。

创建 vignette：

```r
usethis::use_vignette("getting-started")
```

通常会生成：

```text
vignettes/getting-started.Rmd
```

构建：

```r
devtools::build_vignettes()
```

如果只想预览一个 vignette：

```r
rmarkdown::render(
  "vignettes/getting-started.Rmd",
  output_format = "rmarkdown::html_vignette"
)
```

通常不需要把生成的 HTML 提交到 GitHub。

可以在：

```text
vignettes/.gitignore
```

加入：

```text
*.html
*.R
```

---

# 11. 添加自动测试

初始化 testthat：

```r
usethis::use_testthat()
```

为某个函数创建测试文件：

```r
usethis::use_test("add_numbers")
```

会生成类似：

```text
tests/testthat/test-add_numbers.R
```

例如：

```r
test_that("add_numbers works", {
  expect_equal(add_numbers(1, 2), 3)
  expect_equal(add_numbers(-1, 1), 0)
})
```

运行测试：

```r
devtools::test()
```

---

# 12. 正式检查整个 package

这是上传 GitHub 前非常重要的一步。

运行：

```r
devtools::check()
```

理想结果：

```text
0 errors
0 warnings
0 notes
```

某些环境相关 NOTE 不一定代表 package 有问题，但应逐项检查原因。

---

# 13. 初始化 Git

下面操作在 **Terminal** 中完成。

先进入 package 根目录：

```bash
cd /path/to/myPackage
```

例如：

```bash
cd ~/myPackage
```

初始化 Git：

```bash
git init
```

查看状态：

```bash
git status
```

---

# 14. 创建 .gitignore

可以使用：

```r
usethis::use_git_ignore(c(
  ".Rproj.user",
  ".Rhistory",
  ".RData",
  ".Ruserdata"
))
```

也可以手动创建 `.gitignore`，常见内容：

```text
.Rproj.user
.Rhistory
.RData
.Ruserdata
*.Rcheck/
```

不要把个人临时文件、运行缓存、大型中间文件上传到 GitHub。

---

# 15. 第一次 Git commit

在 Terminal 中运行：

```bash
git status
```

确认需要提交的文件后：

```bash
git add .
```

再次检查：

```bash
git status
```

然后提交：

```bash
git commit -m "Initial package release"
```

---

# 16. 在 GitHub 创建仓库

登录 GitHub。

点击：

```text
New repository
```

填写：

```text
Repository name: myPackage
```

如果本地已经存在 README、LICENSE 和 `.gitignore`，建议 GitHub 创建仓库时：

- 不要再勾选 README；
- 不要再添加 `.gitignore`；
- 不要再自动添加 License。

然后点击：

```text
Create repository
```

---

# 17. 将本地仓库连接到 GitHub

GitHub 会给出类似地址：

```text
https://github.com/USERNAME/myPackage.git
```

在 Terminal 中运行：

```bash
git branch -M main
```

添加远程仓库：

```bash
git remote add origin https://github.com/USERNAME/myPackage.git
```

检查：

```bash
git remote -v
```

然后第一次上传：

```bash
git push -u origin main
```

成功后，刷新 GitHub 页面即可看到 package。

---

# 18. 后续修改代码后如何更新 GitHub

以后每次修改代码，不需要重新创建仓库。

标准流程：

```bash
git status
```

查看改了什么：

```bash
git diff
```

添加修改：

```bash
git add .
```

提交：

```bash
git commit -m "Describe the change"
```

同步远程：

```bash
git pull --rebase origin main
```

上传：

```bash
git push origin main
```

建议每次 commit message 都说明修改内容，例如：

```bash
git commit -m "Fix node validation"
```

```bash
git commit -m "Update vignette documentation"
```

```bash
git commit -m "Add regression tests"
```

---

# 19. 如果只修改一个文件

例如只修改：

```text
vignettes/getting-started.Rmd
```

推荐：

```bash
git status

git diff -- vignettes/getting-started.Rmd

git add vignettes/getting-started.Rmd

git commit -m "Update vignette documentation"

git pull --rebase origin main

git push origin main
```

相比 `git add .`，这种方式更加安全，因为不会无意上传其他临时文件。

---

# 20. GitHub Actions：自动检查 R Package

推荐为科研 package 配置跨平台 R CMD check。

可以在 R 中运行：

```r
usethis::use_github_action("check-standard")
```

workflow 通常保存在：

```text
.github/workflows/R-CMD-check.yaml
```

每次：

```bash
git push origin main
```

之后，GitHub 会自动运行检查。

在 GitHub 网页：

```text
Actions
→ R-CMD-check
```

可以查看结果。

理想状态例如：

```text
Windows     success
macOS       success
Ubuntu      success
```

---

# 21. 正式版本发布前的建议检查

在准备发布一个正式版本前，建议至少执行：

```r
devtools::document()

devtools::build_readme()

devtools::build_vignettes()

devtools::test()

devtools::check()
```

然后：

```bash
git status
```

确认工作区干净。

---

# 22. 创建版本 tag

例如准备发布：

```text
v0.1.0
```

先确认当前 commit：

```bash
git rev-parse HEAD
```

创建 tag：

```bash
git tag -a v0.1.0 -m "myPackage v0.1.0"
```

上传 tag：

```bash
git push origin v0.1.0
```

---

# 23. 创建 GitHub Release

打开 GitHub 仓库：

```text
Releases
→ Draft a new release
```

选择：

```text
v0.1.0
```

Release title：

```text
myPackage v0.1.0
```

可以填写简短说明：

```text
First public release.

Main features:
- Feature A
- Feature B
- Unit tests
- Documentation
- Cross-platform R CMD checks
```

如果是正式版本：

```text
不要勾选 pre-release
```

然后：

```text
Publish release
```

---

# 24. 发布后的版本管理原则

一旦：

```text
v0.1.0
```

已经正式发布，就不要移动或覆盖这个 tag。

正确做法：

```text
v0.1.0
↓
发现小 bug
↓
v0.1.1

v0.1.1
↓
增加较明显功能
↓
v0.2.0
```

也就是说：

```text
旧版本保持冻结
新修改创建新版本
```

---

# 25. 推荐的科研软件开发流程

一个比较稳妥的完整流程是：

```text
编写 R 函数
        ↓
devtools::document()
        ↓
更新 README / vignette
        ↓
devtools::test()
        ↓
devtools::check()
        ↓
git status
        ↓
git diff
        ↓
git add
        ↓
git commit
        ↓
git pull --rebase origin main
        ↓
git push origin main
        ↓
GitHub Actions
        ↓
检查 CI
        ↓
版本成熟后创建 tag
        ↓
GitHub Release
```

---

# 26. 一个可以直接复用的日常更新模板

如果 package 已经上传 GitHub，以后日常修改可以直接使用：

```bash
cd /path/to/myPackage

git status

git diff

git add .

git status

git commit -m "Describe your changes"

git pull --rebase origin main

git push origin main
```

如果只想提交一个文件：

```bash
git add path/to/file
```

这种情况下不要直接使用 `git add .`。

---

# 27. 一个可以直接复用的发布模板

假设准备发布 `v0.1.0`：

```bash
git status

git pull --rebase origin main

git rev-parse HEAD

git tag -a v0.1.0 -m "myPackage v0.1.0"

git push origin v0.1.0
```

然后在 GitHub 网页创建 Release。

---

# 28. 常见问题

## 28.1 `git add DESCRIPTION` 报错

```text
fatal: pathspec 'DESCRIPTION' did not match any files
```

通常说明当前 Terminal 不在 package 根目录。

先运行：

```bash
pwd
```

再：

```bash
ls
```

应该能看到：

```text
DESCRIPTION
R/
NAMESPACE
```

## 28.2 修改后 Git 没有检测到

运行：

```bash
git status
```

如果没有显示修改，检查：

- 文件是否保存；
- 修改的是不是当前仓库中的文件；
- 文件是否被 `.gitignore` 忽略。

## 28.3 不小心生成了 HTML

例如：

```text
vignettes/getting-started.html
```

一般不需要上传。

可以在：

```text
vignettes/.gitignore
```

加入：

```text
*.html
*.R
```

## 28.4 `git push` 被拒绝

如果远程 GitHub 比本地更新：

```bash
git pull --rebase origin main
```

处理完成后再：

```bash
git push origin main
```

## 28.5 GitHub Release 后还能修改代码吗

可以。

Release 只固定某个版本。

例如 `v0.1.0` 固定以后，`main` 仍然可以继续开发。

后续发布：

```text
v0.1.1
v0.2.0
```

即可。

---

# 29. 最小操作清单

如果已经有一个可以工作的 R package，上传 GitHub 最少只需要：

```bash
cd /path/to/package

git init

git add .

git commit -m "Initial commit"

git branch -M main

git remote add origin https://github.com/USERNAME/REPOSITORY.git

git push -u origin main
```

但对于科研软件，不建议只做到这里。

至少还应确保：

```text
DESCRIPTION 完整
README 完整
LICENSE 明确
函数有帮助文档
有基本测试
devtools::check() 可通过
GitHub Actions 可运行
版本号明确
```

---

# 30. 推荐原则

对于准备投稿论文或公开科研软件的项目，建议坚持以下原则：

1. 源代码和分析脚本使用相对路径，不使用个人服务器绝对路径。
2. 每个正式版本都应有明确版本号。
3. 正式发布过的 tag 不应移动。
4. README 应能让新读者知道软件用途和最小使用方法。
5. 复杂算法应提供 vignette 或 specification。
6. 关键函数应有 unit tests。
7. 重构后的软件应尽可能做 numerical regression validation。
8. 每次 push 后检查 CI。
9. 不要把临时文件、日志、大型中间文件直接提交到软件仓库。
10. 论文最终应引用实际用于最终分析的软件版本。

---

# 附录：常用命令速查

## RStudio Console

```r
devtools::document()

devtools::load_all()

devtools::test()

devtools::build_readme()

devtools::build_vignettes()

devtools::check()
```

## Terminal

```bash
git status

git diff

git add .

git commit -m "message"

git pull --rebase origin main

git push origin main

git log --oneline --decorate -10

git rev-parse HEAD

git tag -l
```

---

# 总结

建立科研 R package 并上传 GitHub，不只是“把脚本放到网上”，而是将代码整理为一个：

```text
可以安装
可以测试
可以阅读
可以追踪版本
可以重复使用
可以被论文引用
```

的软件项目。

最重要的工作流程可以记成：

```text
写代码
→ 写文档
→ 做测试
→ package check
→ Git commit
→ GitHub push
→ CI
→ tag
→ Release
```

只要保持这一套流程，后续无论是修复 bug、补充文档、增加功能还是为论文投稿准备正式版本，都会更加清晰、安全和可重复。
