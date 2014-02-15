---
layout: post
title: "Windows で Emacs の Magit が遅すぎる"
description: ""
category: 
tags: [emacs, magit]
---
{% include JB/setup %}

環境:

* Windows 7
* GNU Emacs 24.2.1 (i386-mingw-nt6.1.7601) of 2012-12-08 on GNUPACK
* git version 1.8.5.2.msysgit.0

elp-instrument-package で magit を指定して
g (magit-refresh) を実行した結果

~~~
Function Name                                   Call Count  Elapsed Time  Average Time
magit-refresh-status                            1           16.226        16.226
magit-mode-refresh-buffer                       1           16.226        16.226
magit-refresh                                   1           16.226        16.226
magit-git-string                                26          11.213999999  0.4313076923
magit-get-tracked-branch                        3           4.7490000000  1.5830000000
magit-get                                       9           3.882         0.4313333333
magit-git-dir                                   8           3.443         0.430375
magit-insert-status-remote-line                 1           3.019         3.019
magit-git-lines                                 5           2.4010000000  0.4802000000
magit-insert-unpushed-commits                   1           2.162         2.162
magit-insert-unpulled-commits                   1           2.16          2.16
magit-get-ref                                   4           1.7309999999  0.4327499999
magit-get-current-branch                        4           1.7309999999  0.4327499999
magit-git-exit-code                             3           1.31          0.4366666666
magit-get-all                                   3           1.292         0.4306666666
magit-bisecting-p                               3           1.291         0.4303333333
magit-insert-status-head-line                   1           0.867         0.867
magit-insert-untracked-files                    1           0.866         0.866
magit-insert-staged-changes                     1           0.863         0.863
magit-insert-status-tags-line                   1           0.863         0.863
magit-format-tracked-line                       1           0.863         0.863
magit-git-success                               2           0.863         0.4315
magit-read-rewrite-info                         2           0.861         0.4305
magit-rebase-info                               1           0.86          0.86
magit-insert-status-rebase-lines                1           0.86          0.86
magit-insert-stashes                            1           0.674         0.674
magit-get-next-tag                              1           0.432         0.432
magit-insert-bisect-rest                        1           0.432         0.432
magit-insert-status-merge-line                  1           0.432         0.432
magit-get-boolean                               1           0.431         0.431
magit-insert-unstaged-changes                   1           0.431         0.431
magit-insert-pending-changes                    1           0.431         0.431
magit-get-current-tag                           1           0.431         0.431
magit-anything-staged-p                         1           0.43          0.43
magit-format-rev-summary                        1           0.43          0.43
magit-insert-bisect-output                      1           0.43          0.43
magit-insert-status-local-line                  1           0.43          0.43
magit-insert-pending-commits                    1           0.43          0.43
magit-insert-bisect-log                         1           0.429         0.429
magit-file-lines                                1           0.001         0.001
magit-remember-point                            2           0.0           0.0
magit-map-sections                              6           0.0           0.0
magit-insert-empty-line                         1           0.0           0.0
magit-wash-raw-diffs                            1           0.0           0.0
magit-find-section                              16          0.0           0.0
magit-refresh-marked-commits-in-buffer          1           0.0           0.0
magit-find-section-at                           5           0.0           0.0
magit-wash-sequence                             3           0.0           0.0
magit-diff-abbrev-arg                           3           0.0           0.0
magit-current-section                           5           0.0           0.0
magit-correct-point-after-command               2           0.0           0.0
magit-wash-log                                  2           0.0           0.0
magit-section-set-hidden                        6           0.0           0.0
magit-section-path                              19          0.0           0.0
magit-decode-git-path                           1           0.0           0.0
magit-highlight-section                         3           0.0           0.0
magit-flatten-onelevel                          3           0.0           0.0
~~~
magit-refresh に 16.2秒かかり、
magit-git-string は 26回呼ばれ 11.2 秒、
magit-git-lines は 5回呼ばれ 2.4 秒掛かっている。

magit-git-exit-code, magit-git-string, magit-git-insert, magit-git-lines の
process-file を process-file-shell-command に置き換える。

~~~lisp
(defun magit-git-exit-code (&rest args)
  "Execute Git with ARGS, returning its exit code."
  (apply #'process-file-shell-command magit-git-executable nil nil nil
         (append magit-git-standard-options args)))

(defun magit-git-string (&rest args)
  "Execute Git with ARGS, returning the first line of its output.
If there is no output return nil.  If the output begins with a
newline return an empty string."
  (with-temp-buffer
    (apply #'process-file-shell-command magit-git-executable nil (list t nil) nil
           (append magit-git-standard-options args))
    (unless (= (point-min) (point-max))
      (goto-char (point-min))
      (buffer-substring-no-properties
       (line-beginning-position)
       (line-end-position)))))

(defun magit-git-insert (&rest args)
  "Execute Git with ARGS, inserting its output at point."
  (apply #'process-file-shell-command magit-git-executable nil (list t nil) nil
         (append magit-git-standard-options args)))

(defun magit-git-lines (&rest args)
  "Execute Git with ARGS, returning its output as a list of lines.
Empty lines anywhere in the output are omitted."
  (with-temp-buffer
    (apply #'process-file-shell-command magit-git-executable nil (list t nil) nil
           (append magit-git-standard-options args))
    (split-string (buffer-string) "\n" 'omit-nulls)))
~~~

結果は

~~~
Function Name                                   Call Count  Elapsed Time  Average Time
magit-refresh-status                            1           2.771         2.771
magit-mode-refresh-buffer                       1           2.771         2.771
magit-refresh                                   1           2.771         2.771
magit-git-string                                26          0.9370000000  0.0360384615
magit-insert-unpushed-commits                   1           0.574         0.574
magit-insert-unpulled-commits                   1           0.572         0.572
magit-insert-unstaged-changes                   1           0.433         0.433
magit-git-lines                                 5           0.424         0.0848
magit-get-tracked-branch                        3           0.388         0.1293333333
magit-get                                       9           0.3170000000  0.0352222222
magit-git-dir                                   8           0.2850000000  0.0356250000
magit-insert-stashes                            1           0.279         0.279
magit-insert-status-remote-line                 1           0.247         0.247
magit-get-ref                                   4           0.1420000000  0.0355000000
magit-get-current-branch                        4           0.1420000000  0.0355000000
magit-git-exit-code                             3           0.1099999999  0.0366666666
magit-bisecting-p                               3           0.1070000000  0.0356666666
magit-get-all                                   3           0.1060000000  0.0353333333
magit-insert-status-head-line                   1           0.085         0.085
magit-insert-untracked-files                    1           0.075         0.075
magit-insert-staged-changes                     1           0.074         0.074
magit-git-success                               2           0.074         0.037
magit-insert-status-tags-line                   1           0.073         0.073
magit-read-rewrite-info                         2           0.072         0.036
magit-rebase-info                               1           0.071         0.071
magit-insert-status-rebase-lines                1           0.071         0.071
magit-format-tracked-line                       1           0.07          0.07
magit-format-rev-summary                        1           0.05          0.05
magit-get-next-tag                              1           0.038         0.038
magit-anything-staged-p                         1           0.037         0.037
magit-insert-bisect-log                         1           0.036         0.036
magit-insert-status-local-line                  1           0.036         0.036
magit-insert-bisect-rest                        1           0.036         0.036
magit-insert-pending-commits                    1           0.036         0.036
magit-insert-pending-changes                    1           0.036         0.036
magit-insert-status-merge-line                  1           0.036         0.036
magit-get-boolean                               1           0.035         0.035
magit-insert-bisect-output                      1           0.035         0.035
magit-get-current-tag                           1           0.035         0.035
magit-remember-point                            2           0.0           0.0
magit-map-sections                              6           0.0           0.0
magit-insert-empty-line                         1           0.0           0.0
magit-wash-raw-diffs                            1           0.0           0.0
magit-find-section                              16          0.0           0.0
magit-refresh-marked-commits-in-buffer          1           0.0           0.0
magit-find-section-at                           5           0.0           0.0
magit-file-lines                                1           0.0           0.0
magit-wash-sequence                             3           0.0           0.0
magit-diff-abbrev-arg                           3           0.0           0.0
magit-current-section                           5           0.0           0.0
magit-correct-point-after-command               2           0.0           0.0
magit-wash-log                                  2           0.0           0.0
magit-section-set-hidden                        6           0.0           0.0
magit-section-path                              19          0.0           0.0
magit-decode-git-path                           1           0.0           0.0
magit-highlight-section                         3           0.0           0.0
magit-flatten-onelevel                          3           0.0           0.0
~~~

magit-refresh は 2.7 秒になった。

process-file と process-file-shell-command の違いは
cmdproxy.exe を経由することのようである。

`benchmark` 関数を使うと簡単に試せる。

~~~lisp
(benchmark 10 '(process-file magit-git-executable nil nil nil))
=> "Elapsed time: 4.309000s"

(benchmark 10 '(process-file-shell-command magit-git-executable nil nil nil))
=> "Elapsed time: 0.351000s"
~~~
Cygwin Git を試しても違いはない。

~~~lisp
(setq magit-git-executable "c:/cygwin/bin/git.exe")

(benchmark 10 '(process-file magit-git-executable nil nil nil))
=> "Elapsed time: 5.583000s"

(benchmark 10 '(process-file-shell-command magit-git-executable nil nil nil))
=> "Elapsed time: 0.371000s"
~~~
* なぜ cmdproxy.exe を経由すると速くなるのか？
* cmdproxy.exe を真似して gitproxy.exe を作っても解決するか？

## 参考

* [emacsのmagitの性能を改善した - さくらんぼのlambda日記](http://lambdasakura.hatenablog.com/entry/2013/03/22/000211)
* [elisp - Why is (process-file "git") slower than (process-file-shell-command "git") on Emacs for Windows - Stack Overflow](http://stackoverflow.com/q/21753418/2691131)
