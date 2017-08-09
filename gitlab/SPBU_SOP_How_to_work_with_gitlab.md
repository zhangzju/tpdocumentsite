## How to work with gitlab

Authorized by Double.

## SVN Repository 轉換

### 建立 gitlab repository

登入 gitlab 後, 建立 Repository, 點選 New Project:
![new project](imgs/git/double01.png)

### 輸入 repository 相關資訊

1. 選擇 Project, 表示 develop by project
2. 輸入 Project 名稱
3. 輸入 Project 描述 
4. 選擇 Private, 後續再指定 Member
5. 建立

![input infomation](imgs/git/double02.png)

以 HGW 案子為例:

![input infomation](imgs/git/double03.png)

### 準備相關網址

創建完成後, 將 git repository 網址與 欲轉換的 svn repository 網址複製起來:

![prepare url](imgs/git/double04.png)

![prepare url](imgs/git/double05.png)

### 在 linux 環境內進行代碼轉換

* export SVN 與 GIT URL:

```shell

export SVNURL=http://sohoiipfpublic.rd.tp-link.net/svn/ISP/Tender/Platform/Vocalno_BBA_2_0
export GITURL=https://spcodes.rd.tp-link.net/project/TestProject.git

```

* 取得該項目所有 SVN commiter 資訊

```shell

svn log "$SVNURL" -q | awk -F '|' '/^r/ {sub("^ ", "", $2); sub(" $", "", $2); print $2" = "$2" <"$2">"}' | sort -u > ./authors-transform.txt

```
![get svn info](imgs/git/double06.png)

* 將項目從 SVN 轉換為 GIT 至本地端

```shell

git svn clone "$SVNURL" --no-metadata --preserve-empty-dirs -A ./authors-transform.txt --no-minimize-url ./temp

```
![get svn info](imgs/git/double39.png)
![get svn info](imgs/git/double40.png)


*  將 remote git repository 加入控管

```shell

cd temp
git remote add origin "$GITURL"

```

![add remote](imgs/git/double07.png)

* 將本地端之 GIT化項目 push 至 remote git repository

```shell

git push -u origin --all

```

![push branch](imgs/git/double08.png)

* push 完成後即可在 gitlab 進行操作

![handle gitlab](imgs/git/double09.png)

## 創建 develop 分支

###  建立 develop 分支

新增完後就會看到目前有兩個 branch 存在:

![add develop branch](imgs/git/double10.png)

### develop 設定 protect 屬性

將 develop 設定 protect 屬性, 僅 Master 可在此分支 merge 與 push:

![set protect](imgs/git/double11.png)
![set protect](imgs/git/double12.png)
![set protect](imgs/git/double13.png)

## WorkingFlow

### 建立分支
Leader 一般會收到以下幾個來源的任務:

來源: QA Bugzilla

![bugzilla](imgs/git/double14.png)

來源: 前線

![bugzilla](imgs/git/double15.png)

來源: 自測 或 其它

![bugzilla](imgs/git/double16.png)

Leader 須在 Redmine 建立 ticket  ID 並 assign Owner :

![bugzilla](imgs/git/double17.png)

Owner 須自行建 Gitlab Issue 來對應 Redmine Issue ID (可單個或多個):

![bugzilla](imgs/git/double18.png)

填寫 Issue 資訊, 例如: Redmine #1799 可簡寫為 RM1799,

```
Title: RM1799_簡述
Description: 簡述 Issue 來源, Bugzilla ID 或 前線
Assignee: 給自己
```

![bugzilla](imgs/git/double19.png)

Submit issue 後, 最後再 New branch 來建立屬於該 Issue 的 branch:

![bugzilla](imgs/git/double20.png)

### 抓取分支
若先前已使用 git clone https://spcodes.rd.tp-link.net/project/Archer_C5v_TT_v1.git 抓取過

將 gitlab 上的分支 sync 回來:

```shell

git pull

```

![bugzilla](imgs/git/double21.png)

觀察到目前位在哪個分支：

```shell

git branch -a

```
![bugzilla](imgs/git/double22.png)

切換到 bugfix/voip_route_issue 分支:

```shell

git checkout bugfix/voip_route_issue

```
![bugzilla](imgs/git/double23.png)

或者可重新抓取該分支:

```shell

git clone https://spcodes.rd.tp-link.net/project/Archer_C5v_TT_v1.git -b bugfix_voip_20170524

```

### 將修改commit 至 local git

修改完查看已修改之檔案:
```shell

git status

```
![bugzilla](imgs/git/double24.png)

加入 staging area:

```shell

git add

```
![bugzilla](imgs/git/double25.png)

準備 commit message template: ex: /home/bba/git-src/commit/git_template.txt:

```

[BUGFIX][ENHANCEMENT] Title
Customer: TTNET
ProductConfig: VR600_TT_V1
Description: N/A
Symptom: N/A
Rootcause: N/A
Solution: N/A
BugZllia ID: N/A
Related Revision: N/A
Author: Double

```

將 message template 加入 git 環境:

```shell

git config --global commit.template "/home/bba/git-src/commit/git_template.txt"

```

Commit 至 local 端, Commit 前會要求修改 template 訊息:

```shell

git commit 

```

若要重新修改前次 Commit 訊息:

```shell

git commit --amend 

```

### Push至 remote git

觀察目前所在分支:

```shell

git branch -a

```
![bugzilla](imgs/git/double26.png)

將剛剛的修改推送出去:

```shell

git push

```

![bugzilla](imgs/git/double27.png)

 此時在分支上就存在新的繳交:

![bugzilla](imgs/git/double28.png)

### 發動 Merge Request

對想要merge 的分支點擊 Merge Request:

![bugzilla](imgs/git/double29.png)

 假設這個 branch 需在 milestone 前 merge 完成:

![bugzilla](imgs/git/double30.png)

Owner 修正完並提 Merge, 填寫相關資訊且須勾選 branch 移除:

![bugzilla](imgs/git/double31.png)
![bugzilla](imgs/git/double32.png)

### Accept Merge Request

Assignee 會看到有一個 Merge request 通知:

![bugzilla](imgs/git/double33.png)

Assignee 確認無誤後(搭配 code review 機制), Owner 未勾選移除, Merge 後 Leader 將主動移除勾選移除 source branch, 接著點擊 Accept:

![bugzilla](imgs/git/double34.png)

完成 Merge 後如下:

![bugzilla](imgs/git/double35.png)

可看到 branch 被合併回 develop:

![bugzilla](imgs/git/double36.png)

Gitlab Issue 會在 Merge完成後自動 close, 因為該 branch 是由 issue  建立的
 
最後 Owner 須在 Redmine 與 bugzilla 作狀態更新:

![bugzilla](imgs/git/double37.png)
![bugzilla](imgs/git/double38.png)

## Q&A:

### clone 時遇到 server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none?
```shell

git config --global http.sslverify false

```