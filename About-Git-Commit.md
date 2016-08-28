## Git Commit Message 规范  
清晰明了说明本次提交的目的  

### 作用  
1. 提供更多历史信息，方便快速浏览  
```  
git log <last tag> HEAD --pretty=format:%s  
git log <last tag> HEAD --pretty=format:%s --grep lint  
```  
2. 格式  
```  
// 50 ~ 68 characters  
<type>(<scope>): <subject>  

// 72 characters
<body>

<footer>  
```  
* header  
type [scope] subject
```  
type:  
1. feat -- new featuer  
2. fix -- fix bug  
3. docs -- documentation  
4. style -- style will not influence logic  
5. test -- new test  
6. chore -- progress of building or accessibility change  
7. refactor -- refactor code (not new feature or fix bug)  

scope -- which layer commit will influenced  

subject -- what you do  
动词开头  
第一人称现在时  
第一个字母小写  
结尾无句号  
```  

* body  
对 commit 的详细描述  
可以分成多行  
第一人称现在时  
代码变动的动机以及和之前行为的对比  

* footer  
只用于两种情况  
一是不兼容的变动  
以`BREAKING CHANGE`开头，后面是对变动的表述以及变动理由和迁移方法  
二是关闭 Issue  
`Closes #234`  

### Revert  
如果当前的 commit 用于撤销以前的 commit 则必须以 revert: 开头，后面跟随被撤销 commit 的 header  
body 格式固定: This reverts commit {hash}.  

### Commitizen  
撰写和个 Commit Message 的工具  
```  
# install  
npm install -g commitizen  

# configuration  
commitizen init cz-conventional-changelog --save --save-exact  

# usage  
git cz  
```  

### validate commit message  
检查 node 项目的 commit message 是否符合格式  
```  

## From RubyChina  
Title 少于 50 字  
空行  
注释:  
 - 包含一个指向项目的 issue/card/story  
 - 提交信息中包含简短的故事  

注释要回答:  
为什么是必要的修改  
如何解决问题  
变化可能影响到的地方  
使用一般现在时  


## From [here](http://chris.beams.io/posts/git-commit/)  
1. 主题和注释之间用空行分隔  
2. 主题不超过 50 字  
3. 主题首字大写  
4. 主题不加句号  
5. 使用祈使句  
6. 注释控制在 72 字内  
7. 使用注释去解释 什么 和 为什么 而不是 如何  

