---
title: Git Basic. 基礎 Git 指令
categories: software
tags:
  - git
  - github
  - software
keywords:
  - git
  - github
date: 2021-06-19 09:18:23
img: https://drive.google.com/uc?export=view&id=1wvmhnSy5zH5k3Kyphf8lOJk8tWdXtSyA
summary: 本篇介紹幾個 git 會用到的基本 command line 指令
---


本篇介紹幾個 git 會用到的基本 command line 指令

## 安裝 on Mac OS

[tested on macOS Catalina 10.15.1]

*可以用 Brew 安裝，若尚未安裝 Brew: [https://brew.sh/index_zh-tw](https://brew.sh/index_zh-tw)*

``` bash
$ brew install git
$ git --version**
git version 2.21.0 (Apple Git-122.2)
```

github 官網就有提供 GUI 的應用，想用 GUI 的直接去下載安裝就行了，也有簡單明瞭的教學，非常容易。另外也有好幾個第三方的好用 GUI 介面，Google 一下比較一下選自己喜歡的也行。

不過以下還是用 command line 的方法來操作，因為這還是最 general 到哪都可以用的基本方法。因為實務上，比如 code 都放在公司的 server，你可能也是都要 ssh 到 sever 上去改 code，改完之後要上傳到 github。而公司的 server 就是一台 Linux 環境，很可能是沒有提供 GUI 讓你使用的，所以你就只能用 command line 的方式完成 git 的上傳。

## Create Repo

去本地一個你想要放置 git 專案的地方，比如我想把我之後的 git code 都放在我 Mac local 的 `/Users/chungchris/git`

``` bash
$ cd /Users/chungchris/git
$ git init
```

就會看到在此 *git* 目錄下產生一個隱藏的 `.git` 資料夾，這樣就完成了：
``` bash
$ ls -al
total 0
drwxr-xr-x  3   chungchris staff 96 11 21 11:01 .
drwxr-xr-x+ 53  chungchris staff 1696 11 21 10:45 ..
drwxr-xr-x  9   chungchris staff 288 11 21 11:01 .git
```

所以如果之後想要把他刪掉，其實就是把 .git 刪掉就行了：
``` bash
$ rm -rf .git
```

然而，一般來說，`/Users/chungchris/git` 下面我會想要放很多 projects，而不是只有單一 project。而剛剛做的 `$ git init` 其實就是在本地新增一個 `repo` 的意思，而一個 project 可能會包含一個到數個 `repo`。
所以我們更想做的應該是要在 `/Users/chungchris/git` 下面創一個 project 專屬的資料夾，然後把這個資料夾 init 為一個 git 的 repo，或者甚至是在 project 的資料夾下創造多個 repo。

``` bash
$ pwd
/Users/chungchris/git

$ mkdir project20191121

$ cd project20191121/

$ git init
Initialized empty Git repository in /Users/chungchris/git/project20191121/.git/

$ ls -al
total 0
drwxr-xr-x 3 chungchris staff 96 11 21 11:25 .
drwxr-xr-x 4 chungchris staff 128 11 21 11:25 ..
drwxr-xr-x 9 chungchris staff 288 11 21 11:25 .git
```

比如我上面就創了一個名為 `project20191121` 的 folder，並將他做 `$ git init`，於是這個 folder 的 path 就從此成為了一個 git repo，如果你將來不希望他是一個 git repo，而只是一個普通的 folder，那就將 `.git` 刪掉就可以了。

接下來我們設置身份，這在之後上傳雲端 git 的時候需要用到。首先他必須符合我們的 github 帳號，比如我的 github 帳號是 `chungchris`，那我就在本地這樣設置，注意大小寫要一樣才行：

``` bash
$ git config --global user.name “chungchris”

$ git config --global user.email “imchungchris@hotmail.com”

$ git config --get --global user.name
chungchris

$ git config --get --global user.email
imchungchris@hotmail.com
```

## Creste File

接著我們試著在此 repo 下新增一個檔案：

``` bash
$ touch first_try.txt

$ git status
On branch master
No commits yet
Untracked files:
(use “git add <file>” to include in what will be committed)
first_try.txt
nothing added to commit but untracked files present (use “git add” to track)
```

比如上面我就新增了一個名為 `first_try.txt` 的文檔，然後我就可以透過 `$ git status` 來看一下我目前這個 repo 下的狀態，`$ git status` 在往後都會是一個非常基本且常用的指令。
我們會看到 git 告訴我們在 `master` 這個 branch 上面，多出了一個尚未被 track/commit 的檔案 `first_try.txt`，並建議用 `$ git add` 指令將其納入 track。（branch 的概念我們往後再敘述，現在姑且都先在 `master` 上操作）

## Commit

於是我們就依照他的建議做 add：

``` bash
$ git add first_try.txt

$ git status
On branch master
No commits yet
Changes to be committed:
(use “git rm — cached <file>” to unstage)
new file: first_try.txt
```

此時再看一下 `$ git status`，`first_try.txt` 就變成了綠色，也就是被納入 track 了。

接著 git 建議我們將其 commit：

``` bash
$ git commit first_try.txt
```

就會出現 commit message 的編輯頁：

``` bash
# Please enter the commit message for your changes. Lines starting
# with ‘#’ will be ignored, and an empty message aborts the commit.
#
# On branch master
#
# Initial commit
#
# Changes to be committed:
# new file: first_try.txt
#
~
```

你可以在此頁當中說明你 commit 這一筆 change 的原因，比如我們現在是新增了一個 file，那我們為什麼要新增這個 file，用途是什麼？未來如果我們做的事情是編輯了某個既有的 file，那原因可就要敘述為什麼做此改動，改動的方式是什麼之類的。這在實務上是非常重要的，因為其他在這個 project 上工作的人就可以透過這個 commit message 來快速知道 誰、在哪個時間點、因為什麼原因、做了什麼改動，如果描述得不清不楚，那就變得要實際去做 code change review，那就比較辛苦耗時了。

這裏我們就編輯一下 commit message，理由寫上 “Just for test”，然後用 `ESC` 和 `:wq` 儲存完成編輯。

``` bash
Just for test
# Please enter the commit message for your changes. Lines starting
# with ‘#’ will be ignored, and an empty message aborts the commit.
#
# On branch master
#
# Initial commit
#
# Changes to be committed:
# new file: first_try.txt
#
```

（`#` 開頭的幾行在之後都是不會出現的）

``` bash
$ git commit first_try.txt
[master (root-commit) b028b64] Just for test
1 file changed, 2 insertions(+)
create mode 100644 first_try.txt
```

到此 commit 就完成了，於是我們再看一次 git status：

``` bash
$ git status
On branch master
nothing to commit, working tree clean
```

就告訴我們目前所做的更動都已經 commit 了。

如果想要更改 commit msg 也是可以的：

``` bash
$ git commit --amend
```

如果想要做復原呢？比如我們剛剛在這個 repo 下做的事情就是新增了一個 `first_try.txt` 然後將其 commit 了，如果我要回到還沒有做這個動作的時候，我們可以用 reset 指令。但這時候我們可能會遇到一錯誤：

``` bash
$ git reset HEAD^
fatal: ambiguous argument ‘HEAD^’: unknown revision or path not in the working tree.
Use ‘ --‘ to separate paths from revisions, like this:
~
```

這是因為我們剛剛做的事情是盤古開天第一件事，所以沒有 `HEAD`。所以我們嘗試再多做一點，比如現在我們再進一步對已經 commit 的 `first_try.txt` 做一次編輯，`$ git status` 就會看到 `first_try.txt` 又變為紅色，狀態顯示為 `modified`。

``` bash
$ vim first_try.txt

$ git status .
On branch master
Changes not staged for commit:
(use “git add <file>” to update what will be committed)
(use “git checkout — <file>” to discard changes in working directory)
modified: first_try.txt
no changes added to commit (use “git add” and/or “git commit -a”)
```

這裏再介紹一個非常基本且常用的指令
``` bash
$ git diff .
diff — git a/first_try.txt b/first_try.txt
index 0b1076d..751d415 100644
 — — a/first_try.txt
+++ b/first_try.txt
@@ -1,2 +1,3 @@firstfirstfirst
+2222
```

透過這個指令，可以看到我們所做的更改是在 `first_try.txt` 這個檔案新增了一行 “2222” 的文字。

於是我們跟剛剛一樣做 add 和 commit
``` bash
$ git add -A .

$ git commit -m “make a small change”
[master ed597bd] make a small change
1 file changed, 1 insertion(+)

$ git status .
On branch master
nothing to commit, working tree clean
```

`$ git add -A .` 會將當前目錄下所有 untracked 都 add；

`$ git commit -m "..."` 在你的 commit message 只有短短幾個字的時候是很方便的，比如我現在想寫的 commit msg 只有 `make a small change`；

也可以用 `$ git commit -a -m "..."` 進一步化兩行為一行。

此時我們再看一下 `$ git status`，就又變為 clean 了。

這個時候我們就可以嘗試做 reset，讓他變為還沒有新增 2222 那一行的狀態：

``` bash
$ git reset HEAD^**
Unstaged changes after reset:
M first_try.txt

$ git status .
On branch master
Changes not staged for commit:
(use “git add <file>” to update what will be committed)
(use “git checkout — <file>” to discard changes in working directory)
modified: first_try.txt
no changes added to commit (use “git add” and/or “git commit -a”)
```

就可以看到做了 reset 之後，`first_try.txt` 又變回紅色的 `untracked` 狀態了，但此時 2222 還是在的喔，只是我們剛剛做的 add/commit 已經被復原了。

reset HEAD 後面的上標三角是指次數，因為我現在只要回復一級，所以只有一個三角，若想要直接復原兩次 commit 那就給他兩個三角 `HEAD^^`

現在 `first_try.txt` 已經回到了 untracked 狀態，那要怎麼真的把 2222 那行刪掉呢？可以用 `checkout` 指令。

這裡要用 git 的概念來理解，我們剛剛做過兩件事，第一件事是新增了 `first_try.txt`，第二件事是進一步再編輯了一次 `first_try.txt` 新增了 “2222” 這一行，而第一件事已經 commit 了，第二件事曾經 commit 過但又被 reset 了，所以現在等於是第二件事情編輯完而未 commit 的狀態。也就是說現在對 git 來說，他承認的官方狀態是我們做完第一件事的狀態，所以當我們使用了 `checkout` 這個指令，意思就是把當下 repo 承認的官方狀態給 checkout 出來，而這就覆蓋那個被我們就進一步編輯過的 `first_try.txt`，所以效果上就跟復原一樣。

``` bash
$ git checkout .
Updated 1 path from the index

$ git status .
On branch master
nothing to commit, working tree clean

$ cat first_try.txt
firstfirstfirst
```

可以看到做完 checkout 後，repo 狀態又變為 clean 了，於是也可以理解所謂 clean 都是與 git 承認的官方狀態去做比較的。

## Upload

我們在本地端有一個 repo，在雲端也給他個相應的 repo，名稱可以不同的，因為對於本地來說，他其實就可以被視為僅是一個有了 git 屬性的 folder，跟實際上 repo 的名字沒有必然直接關係。

比如我透過 github 網頁創了一個 repo：

![<透過 github 網頁創了一個 repo>](https://drive.google.com/uc?export=view&id=1kl90paRAKgE_UNWdq50HW8NV69qRE-gc)

上面可以看到一行有 remote 的指令，顧名思義如果我們在 local 執行了這行指令，那原本單純都只是在我們 local 操作的 git repo，就和這個剛剛在 github 上創造的名為 test 的 repo 產生了連結：

``` bash
$ git remote add origin https://github.com/chungchris/test.git
```

接著我們便可以把 local 做的改動傳到雲端了，當然指的是已經 local commit 的改動。上傳的這個動作在 git 中叫做 `push`：

``` bash
$ git push origin master
Username for ‘https://github.com': chungchris
Password for ‘https://chungchris@github.com':
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 240 bytes | 240.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/chungchris/test.git
* [new branch] master -> master
```

上面可以看到他要求我輸入帳號密碼，這可以藉由 config 簡化，稍後再述。此時我們去網頁上看，就可以看到 `first_try.txt` 已經被上傳了：

![<first_try.txt 已經被上傳>](https://drive.google.com/uc?export=view&id=1aAFnq05QK5qQE0n6_Upxx_E-dzuzyg_x)

並且它的內容是我們早先 local commit 版本：

![<first_try.txt 的內容>](https://drive.google.com/uc?export=view&id=1GPcptIR01OFGVdCn9gcE45dgiWI58h74)

點進去就看到了我們的 commit msg 以及做過的改動：

![<first_try.txt 的 commit msg 及做過的改動>](https://drive.google.com/uc?export=view&id=1bWtctXBzSaYM1QxGI3iBBD0P8NHCz7RY)

另外，多筆 commit 是可以一次 push 到 remote 的，只是上面例子當中只有一筆。

回到剛剛的 `$ git push` 指令，我們下的是 `$ git push origin master`。

第一個參數是指定 remote 對象是誰，因為我們更早下過
`$ git remote add origin https://github.com/chungchris/test.git`，所以自然 git 就知道這裡的 `origin` 指的是 `https://github.com/chungchris/test.git`

第二個參數是 branch 名稱，一樣 branch 的概念我們之後再一次一起敘述。但這裡可以先提的是，我們本地的 repo 已經和 remote 的 repo 產生連結，實務上，我們基本上就認定在這個 folder 底下的工作就都 default 視為要傳到該特定 remote 的特定 branch 了，如果這個需求成立，我們可以做進一步的綁定

``` bash
$ git checkout master
Already on ‘master’

$ git branch -u origin/master
Branch ‘master’ set up to track remote branch ‘master’ from ‘origin’.
```

從此我們要在這個 local repo 底下工作，要 push 東西到 remote 時，就直接 `$ git push` 就可以了，不需再指定 remote 和 branch。

上述也可以透過第一次做 push 時加上 -u 參數來完成，u 指的是 `upstream`。

接下來我們嘗試在 local 再對 `first_try.txt` 做進一步編輯並 commit 後上傳：

``` bash
$ vim first_try.txt

$ git status .
On branch master
Your branch is up to date with ‘origin/master’.
Changes not staged for commit:
(use “git add <file>” to update what will be committed)
(use “git checkout — <file>” to discard changes in working directory)
modified: first_try.txt
no changes added to commit (use “git add” and/or “git commit -a”)

$ git commit -a -m “add one more line”
[master c89866e] add one more line
1 file changed, 1 insertion(+), 1 deletion(-)

$ git status .
On branch master
Your branch is ahead of ‘origin/master’ by 1 commit.
(use “git push” to publish your local commits)
nothing to commit, working tree clean

$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 293 bytes | 293.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/chungchris/test.git
7f2499f..c89866e master -> master
```

在 github 上就又可以看到了

![<first_try.txt>](https://drive.google.com/uc?export=view&id=13PEE5YO0DBrZ3YwpAhvn3jw-U362e5Nn)

## Pull

前面講的是本地創造然後上傳，接下來將的就是下載，這是一個更常用的情境，有可能是下載別人的 repo 做進一步更改再上傳回去做貢獻，或者是下載自己的 repo 做進一步的開發。

我們一樣就用剛剛已經在 github 上創造的 `test` 這個 repo 為例，假設這個 repo 就是我今天要下載下來本地編輯的 repo：

到 github 的該 repo，就會看到 clone/download 的選項：

![<clone/download 選項>](https://drive.google.com/uc?export=view&id=1WzVey3TRY3vqW3qp-dk0g0IXU0C-ghXR)

這裏我在本地創了個新 folder 然後透過 git clone 把 code 抓下來：

```bash
$ mkdir repo_test

$ cd repo_test/

$ pwd
/Users/chungchris/repo_test

$ git clone https://github.com/chungchris/test.git
Cloning into ‘test’…
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 6 (delta 0), reused 3 (delta 0), pack-reused 3
Unpacking objects: 100% (6/6), done.

$ ls -al
total 0
drwxr-xr-x  3   chungchris staff 96 11 21 13:12 .
drwxr-xr-x+ 54  chungchris staff 1728 11 21 13:09 ..
drwxr-xr-x  4   chungchris staff 128 11 21 13:12 test

$ cd test/

$ ls -al
total 8
drwxr-xr-x 4 chungchris staff 128 11 21 13:12 .
drwxr-xr-x 3 chungchris staff 96 11 21 13:12 ..
drwxr-xr-x 12 chungchris staff 384 11 21 13:12 .git
-rw-r — r — 1 chungchris staff 52 11 21 13:12 first_try.txt
```

可以看到抓下來的 `test` 本身就已經維持是一個 git 的 repo 了。

於是我們嘗試對 `first_try.txt` 做編輯，然後一樣 commit 後 push：

``` bash
$ vim first_try.txt

$ git commit -a -m “make a contribution”
[master 40b2085] make a contribution
1 file changed, 3 insertions(+), 1 deletion(-)

$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 294 bytes | 294.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/chungchris/test.git
c89866e..40b2085 master -> master
```

在 github 上就又可以看到我們的改動了：

![<first_try.txt>](https://drive.google.com/uc?export=view&id=1LvztwFQQpZ-JYobG_gRsgXtNuImWJoJj)
