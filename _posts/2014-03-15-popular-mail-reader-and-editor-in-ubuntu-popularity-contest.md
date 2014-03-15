---
layout: post
title: "Ubuntu Popularity Contest (popcon) での人気の mail-reader と editor"
description: ""
category: 
tags: [linux]
---
{% include JB/setup %}

[Ubuntu Popularity Contest](http://popcon.ubuntu.com/) で package の人気投票を行っています。
ここでの vote の意味は atime が 30日以内に更新されていることです。

> ~~~
> Q) What is considered a 'vote' for a package ?
> 
> A) A computer 'vote' for a package if according to the data provided in the
>    report, a program provided or depending on the package was used less than
>    thirty days ago. This computation is performed by the popcon server.  
> ~~~

mail-reader と editor に該当する package は下の script で取得します。

~~~
apt-list-provides () { apt-cache showpkg $1 | awk '/Pa/, /Reverse P/ {next} {print $1}' | sort -u }
~~~

まずは mail-reader の結果から。

~~~
#rank name                            inst  vote   old recent no-files (maintainer)
706   thunderbird                    1050501 56184 990831  3087   399 (Alexander Sack)                
994   evolution                      1806590 25163 1779917  1094   416 (Debian Evolution Maintainers)  
1880  kmail                          338898  5153 332341  1318    86 (Debian Qt/kde Maintainers)     
2305  emacs23                        90288  3064 86867   310    47 (Unknown)                       
2407  bsd-mailx                      292652  2610 289470   419   153 (Unknown)                       
2639  heirloom-mailx                 54907  1978 51882  1008    39 (Unknown)                       
3115  mutt                           41431  1236 40083    91    21 (Adeodato Simó)                
3964  mailutils                      20020   635 19261   112    12 (Jordi Mallach)                 
4939  sylpheed                       36275   362 35710   189    14 (Ricardo Mones)                 
5058  emacs24                         1275   338   896    41     0 (Unknown)                       
5358  claws-mail                     22788   283 22442    57     6 (Ricardo Mones)                 
7791  xemacs21-mule                  12504   108 12367    18    11 (Ohura Makoto)                  
9119  emacs23-nox                     2419    72  2332    14     1 (Unknown)                       
9125  jed                            10322    72 10227    17     6 (Debian Jed Group)              
10308 balsa                           7186    51  7129     5     1 (Debian Gnome Maintainers)      
10766 emacs23-lucid                   1564    46  1514     4     0 (Unknown)                       
11353 nmh                             4366    40  4305    15     6 (Nick Rusnov)                   
15942 gnumail.app                     3234    16  3211     5     2 (Eric Heintzmann)               
16477 mew-bin                         1312    15  1289     6     2 (Tatsuya Kinoshita)             
16608 xemacs21-nomule                 2018    15  2000     1     2 (Ohura Makoto)                  
16677 emacs24-lucid                     51    14    36     1     0 (Unknown)                       
16678 emacs24-nox                       60    14    45     1     0 (Unknown)                       
17080 xjed                            3136    14  3117     2     3 (Debian Jed Group)              
18711 xemacs21-mule-canna-wnn         1070    11  1051     3     5 (Ohura Makoto)                  
19920 mailutils-mh                     787     9   774     4     0 (Jordi Mallach)                 
22319 cone                             652     6   644     2     0 (Varun Hiremath)                
23468 wl                               589     6   498     1    84 (Tatsuya Kinoshita)             
23872 im                               554     5   543     6     0 (Tatsuya Kinoshita)             
24596 mew                              460     5   375     2    78 (Tatsuya Kinoshita)             
25000 sup-mail                         205     5   199     1     0 (Unknown)                       
27796 gnus                            1722     3  1154     6   559 (James Troup)                   
30149 vm                              1073     3   896     7   167 (Manoj Srivastava)              
34701 tkrat                            210     2   207     1     0 (Mattia Monga)                  
185437 mew-beta                         246     0   105     1   140 (Tatsuya Kinoshita)             
185438 mew-beta-bin                     407     0   400     1     6 (Tatsuya Kinoshita)             
246726 wl-beta                          273     0   248     0    25 (Tatsuya Kinoshita)             
~~~

evolution のほうが thunderbird よりインストールされているが、
使われているのは thunderbird であることがわかります。


次は editor の結果。

~~~
#rank name                            inst  vote   old recent no-files (maintainer)
1262  nano                           2640571 13050 2624793  1799   929 (Jordi Mallach)                 
1596  vim-tiny                       2629776  7661 2618002  3186   927 (Debian Vim Maintainers)        
1853  vim                            260686  5313 254543   685   145 (Debian Vim Maintainers)        
2305  emacs23                        90288  3064 86867   310    47 (Unknown)                       
2452  vim-gnome                      130977  2469 128317   136    55 (Debian Vim Maintainers)        
3973  vim-gtk                        32293   632 31595    51    15 (Debian Vim Maintainers)        
5058  emacs24                         1275   338   896    41     0 (Unknown)                       
6084  vim-nox                         8431   205  8195    25     6 (Debian Vim Maintainers)        
9119  emacs23-nox                     2419    72  2332    14     1 (Unknown)                       
9125  jed                            10322    72 10227    17     6 (Debian Jed Group)              
10603 jove                            1656    48  1603     2     3 (Cord Beermann)                 
10766 emacs23-lucid                   1564    46  1514     4     0 (Unknown)                       
11496 vim-athena                       730    39   680    10     1 (Unknown)                       
16677 emacs24-lucid                     51    14    36     1     0 (Unknown)                       
16678 emacs24-nox                       60    14    45     1     0 (Unknown)                       
16906 mg                              1066    14  1036    14     2 (Trent Buck)                    
17080 xjed                            3136    14  3117     2     3 (Debian Jed Group)              
18862 fte-xwindow                     2045    10  2033     0     2 (Zdenek Kabelac)                
19232 ne                              1200    10  1185     2     3 (Carlo Contavalli)              
19566 elvis                           1411     9  1398     2     2 (Kapil Hari Paranjape)          
20491 le                               819     8   809     2     0 (Debian Qa Group)               
21117 vile                             609     8   597     1     3 (Brendan O'dea)                 
21152 xvile                            703     8   688     3     4 (Brendan O'dea)                 
22535 jupp                            3524     6  3511     4     3 (Unknown)                       
29370 nano-tiny                        516     3   513     0     0 (Jordi Mallach)                 
30798 elvis-console                    515     2   513     0     0 (Kapil Hari Paranjape)          
30931 fte-console                      797     2   793     0     2 (Zdenek Kabelac)                
36667 fte-terminal                    1013     1  1009     0     3 (Zdenek Kabelac)                
37928 levee                            163     1   161     1     0 (Felipe Augusto Van De Wiel)    
243335 vigor                            695     0   694     1     0 (Colin Watson)                  
~~~

nano が1位でした。
Vim と Emacs は時系列で結果を知りたいところです。

[Debian Popularity Contest](http://popcon.debian.org/) も気になったので調査しました。
もしかすると Ubuntu 上で調査したため apt-list-provides の結果が正しくないかも。

~~~
#rank name                            inst  vote   old recent no-files (maintainer)
464   mutt                           148185 37842 86653 23637    53 (Antonio Radici)                
542   bsd-mailx                      145000 32206 91691 21048    55 (Robert Luberda)                
985   evolution                      65321 11375 38578 15331    37 (Debian Evolution Maintainers)  
1291  emacs23                        15882  7251  7845   782     4 (Rob Browning)                  
1651  kmail                          15028  4339  8685  1997     7 (Debian Qt/kde Maintainers)     
1697  heirloom-mailx                 11990  4114  7252   615     9 (Hilko Bengen)                  
2744  mailutils                       4046  1377  2491   177     1 (Jordi Mallach)                 
3079  emacs24                         1465  1060   303   100     2 (Rob Browning)                  
3226  claws-mail                      2663   946  1326   390     1 (Ricardo Mones)                 
3465  emacs23-nox                     2320   798  1431    91     0 (Rob Browning)                  
4844  jed                             1389   405   945    39     0 (Debian Jed Group)              
4987  sylpheed                        1389   377   964    47     1 (Ricardo Mones)                 
5081  thunderbird                      736   360   171    79   126 (Not in sid)                    
6298  xemacs21-mule                   1012   222   759    31     0 (Mark Brown)                    
7559  emacs23-lucid                    320   146   163    11     0 (Rob Browning)                  
9069  balsa                            816    93   691    32     0 (Debian Gnome Maintainers)      
9377  emacs24-nox                      129    86    35     8     0 (Rob Browning)                  
9405  nmh                              898    86   761    51     0 (Nick Rusnov)                   
10309 mew-bin                          269    71   189     9     0 (Tatsuya Kinoshita)             
10974 emacs24-lucid                     81    61    11     9     0 (Rob Browning)                  
13747 wl                               136    37    91     7     1 (Tatsuya Kinoshita)             
13749 xjed                             197    37   148    12     0 (Debian Jed Group)              
14091 xemacs21-nomule                  164    35   123     6     0 (Mark Brown)                    
16277 cone                             124    24    92     8     0 (Varun Hiremath)                
19471 mew-beta-bin                      79    16    62     1     0 (Tatsuya Kinoshita)             
19915 mailutils-mh                      79    15    62     2     0 (Jordi Mallach)                 
19920 mew                              138    15   111     8     4 (Tatsuya Kinoshita)             
20203 gnumail.app                      104    14    81     9     0 (Debian Gnustep Maintainers)    
21164 sup-mail                          84    13    69     2     0 (Debian Ruby Extras Maintainers)
21373 gnus                             131    12   112     2     5 (Not in sid)                    
22146 im                                87    11    71     5     0 (Tatsuya Kinoshita)             
24314 mew-beta                          55     9    28     1    17 (Tatsuya Kinoshita)             
24657 xemacs21-mule-canna-wnn           45     9    34     2     0 (Mark Brown)                    
27048 tkrat                             26     7    17     2     0 (Mattia Monga)                  
27080 vm                               688     7   657    20     4 (Manoj Srivastava)              
28670 wl-beta                           92     6    50    35     1 (Tatsuya Kinoshita)             
~~~

mutt 強し。
thunderbird は大幅にランクダウン。
しかし、 evolution 健闘している。 

~~~
#rank name                            inst  vote   old recent no-files (maintainer)
519   nano                           162731 33612 108776 20274    69 (Jordi Mallach)                 
631   vim                            63700 25487 34195  3996    22 (Debian Vim Maintainers)        
784   vim-tiny                       161775 16677 118459 26575    64 (Debian Vim Maintainers)        
1291  emacs23                        15882  7251  7845   782     4 (Rob Browning)                  
1889  vim-gtk                         6100  3194  2514   390     2 (Debian Vim Maintainers)        
1965  vim-gnome                       4840  2898  1680   257     5 (Debian Vim Maintainers)        
2331  vim-nox                         5119  2009  2732   377     1 (Debian Vim Maintainers)        
3079  emacs24                         1465  1060   303   100     2 (Rob Browning)                  
3465  emacs23-nox                     2320   798  1431    91     0 (Rob Browning)                  
4844  jed                             1389   405   945    39     0 (Debian Jed Group)              
5531  jove                            1316   297   998    21     0 (Cord Beermann)                 
7044  elvis                            327   173   152     2     0 (Kapil Hari Paranjape)          
7559  emacs23-lucid                    320   146   163    11     0 (Rob Browning)                  
7752  vim-athena                       394   138   197    59     0 (Debian Vim Maintainers)        
8833  mg                               351   100   236    15     0 (Trent W. Buck)                 
9377  emacs24-nox                      129    86    35     8     0 (Rob Browning)                  
10974 emacs24-lucid                     81    61    11     9     0 (Rob Browning)                  
11488 jupp                             199    55   136     8     0 (Thorsten Glaser)               
12713 ne                               168    44   114    10     0 (Julián Moreno Patiño)          
13749 xjed                             197    37   148    12     0 (Debian Jed Group)              
13933 fte-xwindow                      232    35   180    17     0 (Debian Qa Group)               
16331 le                                99    24    70     5     0 (Raphael Geissert)              
17474 vile                              53    21    29     3     0 (Brendan O'dea)                 
17535 fte-terminal                     110    20    84     6     0 (Debian Qa Group)               
22067 elvis-console                     34    11    22     1     0 (Kapil Hari Paranjape)          
23813 fte-console                       68     9    53     6     0 (Debian Qa Group)               
25781 vigor                             66     8    54     4     0 (Colin Watson)                  
27113 xvile                             22     7    12     3     0 (Brendan O'dea)                 
28269 nano-tiny                         53     6    45     2     0 (Jordi Mallach)                 
37953 levee                             19     2    15     2     0 (Jari Aalto)                    
~~~

ほぼ Ubuntu と同じ。

