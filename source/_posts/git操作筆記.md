---
title: git 操作筆記
date: 2020-02-13 19:08:13
tags: git
categories: git
---

# 資料參考

<img src="/images/learn-git.png" alt="為你自己學Git" style="zoom:50%;" />

# Vim 模式

> 分為 Normal 模式 & Insert 模式

- 使用 ESC 或 Ctrl + [ 可切換到 Normal 模式
  - :w 存檔
  - :q 離開
  - :wq 存檔離開
- Normal 模式輸入 i or a or o 可切換到 Insert 模式
  - i = Insert 
  - a = append
  - o = new line

<!-- more -->

# Git Bash Alias 設定

- 可以到 ~/.gitconfig 裡面設定

- 也可以到 .git 資料夾裡面的 config 檔案做設定

- 建議設定

  ```
  [alias]
  	co = checkout
  	br = branch
  	st = status
  	l = log --online --graph
  	ls =log --graph --pretty=format:"%h <%an> %ar %s"
  ```


# 查詢 Git Log 

[更詳細的檢視提交的歷史記錄](https://git-scm.com/book/zh-tw/v2/Git-%E5%9F%BA%E7%A4%8E-%E6%AA%A2%E8%A6%96%E6%8F%90%E4%BA%A4%E7%9A%84%E6%AD%B7%E5%8F%B2%E8%A8%98%E9%8C%84)

## 找 Commit 訊息裡面有紀錄金流串接的訊息

> 使用 --grep 參數

```bash
$ git log --online --grep="金流串接"
```

## 找 Commit 的檔案內容有提到 "insert into" 的語法

> 使用 -S 參數，可以搜尋 Commit 的檔案內容中，那些有使用到 SQL 的 insert into 語法

```bash
$ git log -S "insert into"
```

## 檢查 TimeSheet 的時候發現有天忘記寫了，查詢 git log 看看那天在幹嘛

> --since、--until、--after 、--before 一起使用

舉例：我想找 2019-01-03 這一天在幹嘛

```bash
$ git log --oneline --since="8am" --until="5pm" --after="2019-01-02" --before="2019-01-03"
```

# 修改 Commit 紀錄

## 為你自己學Git的經典例子，假如你在 Commit 訊息裡把客戶給罵了

有以下幾點方法：

1. 把 .git 目錄刪了 (不可取)
2. `git rebase` 修改黑歷史
3. `git reset` 重新 Commit
4. `--amend` 修改作後一次的 Commit

### --amend 修改 Commit

<img src="/images/amend-modify-commit.png" />

## Commit 後才發現漏了一個檔案，想要補檔案回去

> `--no-edit` ：不編輯 Commit 訊息。

<img src="/images/make-up-the-file.jpg" />

## 新增一個空的目錄到 repository 裡面

> 有時候開專案，都會想把一些常見的目錄開一開，可暫時沒資料，但是有潔癖的我不想亂塞檔案，又想把目錄同時被 commit 進去，這時候可以在目錄裡面放 `.gitkeep` 檔案。

<img src="/images/make-up-empty-folder.jpg" />

## 檢視 Commit 的檔案紀錄

### 找出誰養的蟲

```bash
$ git blame index.html
```

# 拯救手殘不小心把檔案或目錄刪掉的人

> checkout 會將檔案或目錄復原到上一個 Commit 的狀態，所以如果加上 HEAD~2 參數會復原到兩個版本以前的檔案狀態。

```bash
$ git checkout .
```

或是

```bash
$ git checkout <檔案>
```

<img src="/images/restore-files.jpg" />

# Commit 後想重新來一遍該怎麼做

## `git reset`

### 假設我今天寫了很笨的程式還Commit了，想要打掉重練要怎麼做?

<img src="/images/git-reset-01.jpg" />

> reset 後面可再加上 [ --mixed| --soft | --hard ] [詳細說明參考](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified)

可以輸入

```bash
$ git reset 8bfaa88^
```

或

```bash
$ git reset master^
```

或

```bash
$ git reset HEAD^
```

或

```bash
$ git reset 17776c7
```

### 如果我發現直接拿原本的程式直接重構比較快，想回到剛才的 Commit 要怎麼做?

> 搭配 `git reflog`，一般來說我們不會記錄 reset 前的 SHA-1 值，所以要用 `git reflog` 的紀錄來找。

# `git branch` 的操作

## 想修改手殘打錯的 branch 名稱

> 可以使用 `-m` 參數。

```bash
$ git branch -m <old_branch_name> <new_branch_name> 
```

## 刪除分支的時候

> 可使用 `-d` 或 `-D` 參數。
>
> `-D`：強制刪除
>
> `-d`：會因為沒有被合併而無法刪除分之
>
> [參考資料](https://git-scm.com/book/en/v2/Git-Branching-Branch-Management)

## 在過去的 Commit 開啟分支的方式

第一種、

```bash
$ git checkout <Commit-SHA-1值>
$ git checkout -b <new-branch-name>
```

第二種、

```bash
$ git branch <new-branch-name> <commit-SHA-1值>
```



# `git rebase`

## 另一種分支的合併方式 

合併的一般做法

```bash
$ git branch master
$ git merge <your_branch_name>
```

`git rebase`：可以重新定義，分支的基準點，rebase 會再重新計算出新的 Commit 物件，再將分支指向新的 Commit 物件。可直接參考[官方詳細資料](https://git-scm.com/docs/git-rebase)

如下圖

<img src="/images/git-rebase-01.jpg" />

<img src="/images/git-rebase-02.jpg" />

<img src="/images/git-rebase-03.jpg" />

## 修改過去的 Commit 訊息

> 可以先設定 git conifg --local core.editor notepad (或是其他的編輯工具)

1. 先使用 `git log`查詢要修改的 Commit 

<img src="/images/git-rebase-i-01.jpg" />

2. 接著選擇我們修改目標的前一個值，git 就會帶出比輸入的 Commit 還新的紀錄。

<img src="/images/git-rebase-i-02.jpg" />

<img src="/images/git-rebase-i-03.jpg" />

3. 再將 pick 修改成 r，存檔關閉。

<img src="/images/git-rebase-i-04.jpg" />

4. 關閉後就會再自動開啟記事本修改 commit ，修改完後存檔離開。

<img src="/images/git-rebase-i-05.jpg" />

5. 再回去查詢一下次，就會看到 Commit 被修改。

<img src="/images/git-rebase-i-06.jpg" />

# `git revert`

- revert 會新增一個 Commit  來取消另一個 Commit 的內容。

```bash
$ git revert HEAD --no-edit
```

