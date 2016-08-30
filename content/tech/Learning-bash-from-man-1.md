+++
Categories = ["bash", "learning"]
Description = ""
Tags = ["bash", "learn"]
date = "2016-08-30T21:22:11+08:00"
title = "从男人开始学习bash(1)"

+++
`man`是大家经常用的命令，但是学习bash的时候有很多书。今天发现`man bash`是一个不错的资源，摘抄一些供大家学习。


* 脚本加载顺序

  When  bash is invoked as an interactive login shell, or as a non-interactive shell with the --login option, it first reads and executes commands from the file /etc/profile, if that file exists.  After reading that file, it looks for ~/.bash_profile, ~/.bash_login, and ~/.profile, in that order, and reads and executes commands  from  the  first one that exists and is readable.  The --noprofile option may be used when the shell is started to inhibit this behavior.
  When a login shell exits, bash reads and executes commands from the files ~/.bash_logout and /etc/bash.bash_logout, if the files exists.
  When  an  interactive  shell that is not a login shell is started, bash reads and executes commands from ~/.bashrc, if that file exists.  This may be inhibited by using the --norc option.  The --rcfile file option will force bash to read and execute commands from file instead of ~/.bashrc.  

当`bash`作为一个交互式的登陆程序启动时，首先会读取`/etc/profile`里的内容，然后依次找`~/.bash_profile, ~/.bash_login, and ~/.profile`。退出时，也是执行`/.bash_logout ，/etc/bash.bash_logout`。当以不登陆的shell启动时，就执行`~/.bashrc`。

* 基本定义和保留字

定义
  
```
The following definitions are used throughout the rest of this document.
blank  A space or tab.
word   A sequence of characters considered as a single unit by the shell.  Also known as a token.
name   A word consisting only of alphanumeric characters and underscores, and beginning with an alphabetic character or an underscore.  Also referred to as an identifier.
metacharacter    A character that, when unquoted, separates words.  One of the following:
       |  & ; ( ) < > space tab
control operator A token that performs a control function.  It is one of the following symbols:
       || & && ; ;; ( ) | |& <newline>  
```

保留字

``` 
Reserved words are words that have a special meaning to the shell.  The following words are recognized as reserved when unquoted and either the first word of a simple  command (see SHELL GRAMMAR below) or the third word of a case or for command:
! case  coproc  do done elif else esac fi for function if in select then until while { } time [[ ]]
```

* Shell语法

  + 简单命令
   
A  simple  command  is a sequence of optional variable assignments followed by blank-separated words and redirections, and terminated by a control operator.  The first word specifies the command to be executed, and is passed as argument zero.  The remaining words are passed as arguments to the invoked command.

The return value of a simple command is its exit status, or 128+n if the command is terminated by signal n.

一系列由变量赋值+空格分割的单词+重定向，以控制操作结尾。返回值就是命令退出的返回值， 128+n表示这个程序被signal n结束。
 
   + PipeLine

格式： `[time [-p]] [ ! ] command [ [|⎪|&] command2 ... ]`  

`|&`表示 command的error也会追加到command2中，是` 2>&1 |`的缩写  
The return status of a pipeline is the exit status of the last command, unless the `pipefail` option is enabled.  If `pipefail` is enabled, the pipeline's return status is  the value of the last (rightmost) command to exit with a non-zero status, or zero if all commands exit successfully.  If the reserved word !  precedes a pipeline, the exit status of that pipeline is the logical negation of the exit status as described above.  The shell waits for all commands in the pipeline to terminate before returning a value.  
返回值是最后一个命令执行结果。`！`表示退出状态相反的逻辑  
If the time reserved word precedes a pipeline, the elapsed as well as user and system time consumed by its execution are reported when  the  pipeline  terminates.   The  -p option changes the output format to that specified by POSIX.  When the shell is in posix mode, it does not recognize time as a reserved word if the next token begins with a ’-‘.  
有`time`的话会统计一个执行时间。 `-p`表示使用POSIX时间输出

+ Lists

 A list is a sequence of one or more pipelines separated by one of the operators ;, &, &&, or ||, and optionally terminated by one of ;, &, or <newline>. Of these list operators, && and || have equal precedence, followed by ; and &, which have equal precedence.

 A sequence of one or more newlines may appear in a list instead of a semicolon to delimit commands.If a command is terminated by the control operator &, the shell executes the command in the background in a subshell.  The shell does not wait for the  command  to  finish,and the return status is 0.  Commands separated by a ; are executed sequentially; the shell waits for each command to terminate in turn.  The return status is the exit status of the last command executed.  
被`; , & && ||`分开的一个或多个的`pipeline`，可选`; , & <newline>`结尾  
`&`结尾表示在后台运行，被`；`分开的会分别执行，返回最后一个执行过的命令的状态作为返回值  
 
command1 && command2  
command2 is executed if, and only if, command1 returns an exit status of zero.  
command1 || command2  
command2  is  executed if and only if command1 returns a non-zero exit status.  The return status of AND and OR lists is the exit status of the last command executed in the list.  

And和Or逻辑区别，注意，Bash里面，0为真，其他为假。所以And时候，command1==0 才会执行command2。真假和C语言是颠倒的

* 复合命令（compound command）

A compound command is one of the following.  In most cases a list in a command's description may be separated from the rest of the command by one or more newlines, and  may be followed by a newline in place of a semicolon.

 + `(List)`

 list  is executed in a `subshell` environment.  Variable assignments and builtin commands that affect the shell's environment do not remain in effect after the command completes.  The return status is the exit status of list.

在子Shell环境中执行List，变量赋值和内部变量操作不会影响子随后的命令。

+  `{ list; }`

在当前环境中执行List

  - `((expression))`

The expression is evaluated according to the rules described below under ARITHMETIC EVALUATION.  If the value of the expression is non-zero, the return status is 0;otherwise the return status is 1.  This is exactly equivalent to let "expression".  
数值计算，数值计算值不为0的时候返回0，否则返回1。好绕。。这点正好印证了对于bash而言，0才是真！
 
  - `[[ expression ]]`

Return a status of 0 or 1 depending on the evaluation of the conditional expression expression.  Expressions are composed of the primaries described below under CONDITIONAL EXPRESSIONS.  Word splitting and pathname expansion are not performed on the words between the [[ and ]]; tilde expansion, parameter and variable expansion,arithmetic  expansion,  command  substitution, process substitution, and quote removal are performed.  Conditional operators such as -f must be unquoted to be recognized as primaries.
表达式计算。 `< >`的比较会依赖于当前地区的字母序列进行比较  
When the == and != operators are used, the string to the right of the operator is considered a pattern and matched according to the rules described below under  Pattern  Matching, as if the extglob shell option were enabled.  The = operator is equivalent to ==.  If the shell option nocasematch is enabled, the match is performed without regard to the case of alphabetic characters.  The return value is 0 if the string matches (==) or does not match (!=) the pattern, and 1 otherwise.  `Any part of the pattern may be quoted to force the quoted portion to be matched as a string`?.  
`==` 和 `!=`使用是，右边的操作会被认为是一个pattern。`=`和`==`是相同的。  
 An  additional  binary  operator, =~, is available, with the same precedence as == and !=.  When it is used, the string to the right of the operator is considered an extended regular expression and matched accordingly (as in regex(3)).  The return value is 0 if the string matches the pattern, and  1  otherwise.   If  the  regular expression  is  syntactically  incorrect, the conditional expression's return value is 2.  If the shell option nocasematch is enabled, the match is performed without regard to the case of alphabetic characters.  

`=~` 右边被认为是一个正则表达式，返回2的话表示正则有问题。

`Any part of the pattern may be quoted to force the quoted portion to be matched as a string`?.Bracket  expressions  in regular  expressions  must be treated carefully, since normal quoting characters lose their meanings between brackets.  If the pattern is stored in a shell variable,quoting the variable expansion forces the entire pattern to be matched as a string.  Substrings matched by parenthesized subexpressions within the regular expression are  saved  in  the  array variable BASH_REMATCH.  The element of BASH_REMATCH with index 0 is the portion of the string matching the entire regular expression.  The element of BASH_REMATCH with index n is the portion of the string matching the nth parenthesized subexpression.  
使用正则时候要注意。特别是包含大括号的时候。

Expressions may be combined using the following operators, listed in decreasing order of precedence:
`( expression )`      Returns the value of expression.  This may be used to override the normal precedence of operators.    
`! expression`        True if expression is false.  
`expression1 && expression2`      True if both expression1 and expression2 are true.   
`expression1 || expression2`      True if either expression1 or expression2 is true.

`&& ||`都会造成逻辑短路。express2有可能会不执行。

  - `for name [ [ in [ word ... ] ] ; ] do list ; done`
 
for的一种表达形式

  - `for (( expr1 ; expr2 ; expr3 )) ; do list ; done`  

和传统的逻辑一致

  - `select name [ in word ] ; do list ; done`

做选择菜单的时候用，提供类似GUI的交互

  - `case word in [ [(] pattern [ | pattern ] ... ) list ;; ] ... esac`

Switch case语法

A case command first expands word, and tries to match it against each pattern in turn, using the same matching rules as for pathname expansion (see  Pathname  Expansion  below).The word is expanded using tilde expansion, parameter and variable expansion, arithmetic substitution, command substitution, process substitution and quote removal.  Each pattern examined is expanded using tilde expansion, parameter and variable expansion, arithmetic substitution, command substitution, and process substitution.   If  the  shell option nocasematch is enabled, the match is performed without regard to the case of alphabetic characters.  

When a match is found, the corresponding list is executed.  If the ;; operator is used, no subsequent matches are attempted after the first pattern match.  Using ;& in place of ;; causes  execution to continue with the list associated with the next set of patterns.  Using ;;& in place of ;; causes the shell to test the next pattern list in the statement, if any, and execute any associated list on a successful match.  The exit status is zero if no pattern matches.  Otherwise, it is the exit status of the last  command executed in list.  
如果将`;;`换成`;;&`，那么下一个pattern也会被测试

  - `if list; then list; [ elif list; then list; ] ... [ else list; ] fi`

if语法

  - `while list-1; do list-2; done`
  - `until list-1; do list-2; done`

while until语法

