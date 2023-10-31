---
layout: post
title: "PowerShell の stderr の落とし穴"
description: ""
tags: 
---

PowerShell では外部プログラムの標準エラー (stderr) を error stream に流すために大きな落とし穴があるので、まとめておく。

* stderr を redirect して外部プログラムを起動し、 stderr に出力すると一行ごとに ErrorRecord でラップされる
* stdout への redirect `2>&1` 、ファイルへの redirect `2> a.txt`、`$null` への redirect `2>$null` で ErrorRecord でのラップがされる
* 外部プログラムの stderr を stdout やファイルに redirect すると、 `NativeCommandError` と出力される
* stderr に出力があるとエラーとみなされ `$?` は `$false` になり、エラーは `$Error` に記録される。
* `$ErrorActionPreference = "stop"` していると、stderr に一行目が出力されると処理が止まる
* PowerShell スクリプトで 外部プログラムの起動において stderr を redirect していなくても、その PowerShell スクリプトの stderr が redirect されると ErrorRecord でのラップがされる
* `$null` への redirect では stderr は捨てられるが、エラーが発生したことは `$Error` に記録される。

対策としては、`$?` ではなく `$LastExitCode` をチェックする。
stderr の元の文字列が見たいときは stdout に redirect して ToString() する `2>&1 |% { "$_" }`。

* [$LastExitCode=0, but $?=False in PowerShell. Redirecting stderr to stdout gives NativeCommandError - Stack Overflow](https://stackoverflow.com/q/10666101/2691131)
* [ Capture program stdout and stderr to separate variables - Stack Overflow](https://stackoverflow.com/q/24222088/2691131)

[The Big Book of PowerShell Error Handling](https://devops-collective-inc.gitbook.io/the-big-book-of-powershell-error-handling/putting-it-all-together)
という本にも書かれている。

> If an external executable writes anything to the StdErr stream,
> PowerShell sometimes sees this and wraps the text in an ErrorRecord,
> but this behavior doesn't seem to be consistent.
> I'm not sure yet under what conditions these errors will be produced,
> so I tend to stick with $LASTEXITCODE when I need to tell whether an external command worked or not.

本家でもたくさん議論されていて状況がよくわからない。

* [Don't wrap stderr as ErrorRecord by SteveL-MSFT · Pull Request #5190 · PowerShell/PowerShell](https://github.com/PowerShell/PowerShell/pull/5190)
* [StdErr is wrapped as ErrorRecord producing undesired effects · Issue #3813 · PowerShell/PowerShell](https://github.com/PowerShell/PowerShell/issues/3813)
* [Inconsistent handling of native command stderr · Issue #3996 · PowerShell/PowerShell](https://github.com/PowerShell/PowerShell/issues/3996)


## Invoke-NativeApplication

[Invoke-NativeApplication](https://github.com/mnaoumov/Invoke-NativeApplication) 
を使うと良さそう。
特に `$ErrorActionPreference = "stop"` しているスクリプトで有効と思われる。

* `$ErrorActionPreference` を `Continue` にしてコマンドを実行する
* 外部コマンドを実行し stdout と stderr の各行を文字列のリストで返す
* 文字列には IsError プロパティがセットされる
* `$LastExitCode` に基づき例外を投げる (例外よりも Write-Error で error stream に出力したほうがよいかも)
* `-IgnoreExitCode` と `-AllowedExitCodes` 引数あり
* あいにく stderr に出力があると エラーとして `$Error` に記録が残る

## Start-Process

Start-Process を使うと、PowerShell が stdout や stderr を一行ずつ処理するのを回避でき、処理されずにコンソールに出力されるようである。

~~~powershell
function Invoke-NativeCommand($command) {
    $process = Start-Process $command -ArgumentList $args -Wait -NoNewWindow -PassThru
    if ($process.ExitCode -ne 0) {
       Write-Error -Category InvalidResult -Message "$command exits with $($process.ExitCode)"
    }
}
~~~

私がやりたかったことはこれで十分だった。
欲を言えば、
親プロセスである PowerShell の stdout と stderr を継承して欲しいのだが。
この方法では PowerShell スクリプトの stdout と stderr をファイルにリダイレクトしても、
Start-Process で起動したプロセスはコンソールに出力されるようである。

* [PowerShell: Running Executables - TechNet Articles](https://social.technet.microsoft.com/wiki/contents/articles/7703.powershell-running-executables.aspx)

## PowerShell 7.1

PowerShell 7.1 で [改修された](https://learn.microsoft.com/en-us/previous-versions/powershell/scripting/whats-new/what-s-new-in-powershell-71?view=powershell-7.1#breaking-changes-and-improvements) 。

- Fix $? to not be $false when native command writes to stderr (#13395)
- Make $ErrorActionPreference not affect stderr output of native commands (#13361)
