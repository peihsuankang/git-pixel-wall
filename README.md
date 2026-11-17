# Git 像素牆

部門 Git 工作坊的練習 repo。每個人在自己的座位用字母畫一張 8×8 的小圖，透過 Pull Request（PR）合併進 main。全部合併完，就是一面大家一起完成的像素牆。

- **在本機看**：雙擊 `index.html`，用瀏覽器打開。
- **在網路上看**：`https://<owner>.github.io/git-pixel-wall/`（開啟 GitHub Pages 後才有，merge 後大約 1 分鐘更新）

> 下面的指令以 Windows PowerShell 為準，Git Bash 也能用。`<...>` 的部分請換成你自己的內容。

---

## 1. 怎麼畫

打開 Issue 指定給你的座位檔，例如 `parts/seat3.js`：

```js
art("seat3", "amy", [
  "........",
  ".RR..RR.",
  "RRRRRRRR",
  "RRRRRRRR",
  ".RRRRRR.",
  "..RRRR..",
  "...RR...",
  "........",
]);
```

- 第二個參數是顯示在圖下面的名字，改成你的暱稱。
- 一行 8 個字，共 8 行。每個字母代表一種顏色，對照表在 `palette.js`；`.` 是空白。
- **只改引號裡面的字**，引號和逗號不要刪掉。
- 改完雙擊 `index.html`（已經開著的話按 F5 重新整理）看結果。如果你的格子變成紅色虛線框，代表檔案有地方寫錯，框裡會說明原因。

範例（可以直接照抄再修改）：

```text
笑臉          樹            貓
"..YYYY.."    "...GG..."    "K.....K."
".YYYYYY."    "..GGGG.."    "KK...KK."
"YYKYYKYY"    ".GGGGGG."    "KKKKKKK."
"YYYYYYYY"    "GGGGGGGG"    "KWKKWKK."
"YKYYYYKY"    "..GGGG.."    "KKKKKKK."
"YYKKKKYY"    "...NN..."    ".KKRKK.."
".YYYYYY."    "...NN..."    "..KKK..."
"..YYYY.."    "..NNNN.."    "........"
```

---

## 2. 開始前（只做一次）

```powershell
cd C:/git-ws
git clone https://github.com/peihsuankang/git-pixel-wall-test.git
cd git-pixel-wall
```

在這個 repo 裡設定你的名字和 email。`--local` 只影響這個 repo，不會改到你平常給公司專案用的設定：

```powershell
git config --local user.name "<你的名字>"
git config --local user.email "<數字>+<帳號>@users.noreply.github.com"
```

> noreply email 在 GitHub 右上角頭像 → Settings → Emails 可以找到。這個 repo 是公開的，用 noreply 就不會把公司 email 公開出去。

確認設定：

```powershell
git config --show-origin user.email
```

應該看到 `file:.git/config` 開頭，代表只在這個 repo 生效。

---

## 3. 規則

1. **不要在 main 上 commit。** 每個任務都先開自己的分支。
2. **只改 Issue 指定的檔案和位置。** 不要用格式化工具重排整份檔案。
3. **PR 描述寫 `Closes #<編號>`**，merge 後 Issue 會自動關閉。
4. **開完 PR，先去 review 別人的。**
5. 看到 `LF will be replaced by CRLF` 是正常的警告，不用理會。

---

## 4. 做一個任務的流程

### ① 開分支

第一個任務：剛 clone 下來的 main 就是最新的，直接開分支。

```powershell
git switch -c issue-<編號>-<名字>
```

之後的任務：先回到 main 拉最新的內容，再開分支。

```powershell
git switch main
git pull
git switch -c issue-<編號>-<名字>
```

### ② 修改、commit

用編輯器改檔案，存檔，用瀏覽器確認畫面，然後：

```powershell
git status
git add parts/seat<座位>.js
git commit -m "<這次改了什麼>"
```

### ③ push 分支

```powershell
git push -u origin issue-<編號>-<名字>
```

第一次 push 會跳出 GitHub 登入視窗，選「Sign in with your browser」，在瀏覽器登入並授權即可。之後就不會再問。

### ④ 開 PR

1. 打開 repo 頁面，點黃色提示列上的 **Compare & pull request**。
2. 描述照範本填，記得寫 `Closes #<編號>`。
3. 右側 **Reviewers** 選一位同事 → **Create pull request**。

### ⑤ Review 別人的 PR

1. 打開同事的 PR → **Files changed**，看改了哪些行。只應該動到 Issue 指定的檔案。
2. 想留意見：在那一行按 **+**，寫下意見。
3. 右上 **Review changes** → 選 **Approve** → **Submit review**。

### ⑥ Merge

收到 Approve 後，在自己的 PR 頁按 **Merge pull request** → **Confirm merge**，再按 **Delete branch**。

### ⑦ 回到 main、清掉分支

```powershell
git switch main
git pull
git branch -d issue-<編號>-<名字>
git fetch --prune
```

重新整理 `index.html`，就看得到大家的圖。

---

## 5. PR 顯示有衝突時

PR 頁出現 **This branch has conflicts that must be resolved**，代表別人先 merge 了，而且改到和你同一個地方。在**自己的分支**上把最新的 main 併進來：

```powershell
git switch issue-<編號>-<名字>
git fetch
git merge origin/main
```

出現 `CONFLICT (content): Merge conflict in ...` 之後：

1. 用 VS Code 打開那個檔案。衝突的地方長這樣：

   ```text
   <<<<<<< HEAD
   你的版本
   =======
   main 上的版本
   >>>>>>> origin/main
   ```

2. 改成你們要的內容，**`<<<<<<<`、`=======`、`>>>>>>>` 三種標記都要刪掉**。不一定要二選一，也可以兩邊各留一部分。
   VS Code 上方的 Accept Current / Accept Incoming / Accept Both 也可以用，點完記得存檔。
3. 存檔，用瀏覽器確認畫面正常，然後：

```powershell
git add <檔案>
git commit --no-edit
git push
```

push 之後 PR 會自動更新，衝突提示消失。請 reviewer 再看一次。

解到一半想放棄、回到 merge 之前：

```powershell
git merge --abort
```

> 為什麼不是 `git pull`？在已經 push 過的分支上打 `git pull`，只會拉同名的遠端分支，拿不到 main 的新內容。

---

## 6. 出錯時怎麼辦

| 狀況 | 指令 | 結果 |
|---|---|---|
| 改了檔案，還沒 add | `git restore <檔案>` | 修改丟掉，**救不回來** |
| add 了，還沒 commit | `git restore --staged <檔案>` | 拿出暫存區，修改還在工作目錄 |
| commit 了，還沒 push | `git reset HEAD~1` | 取消最後一個 commit，修改留在工作目錄；加 `--hard` 會一起丟掉 |
| reset 弄錯了 | `git reflog` | 找到之前的位置，再 `git reset --hard 'HEAD@{1}'` 回去 |
| 已經 push 了 | `git revert <commit>` | 加一個反向 commit，再 push |

> PowerShell 裡 `HEAD@{1}` 一定要加單引號，否則 PowerShell 會把它拆開，指令會失敗。

---

## 7. 現場常見錯誤

| 看到的訊息 | 原因 | 怎麼辦 |
|---|---|---|
| `has no upstream branch` | 新分支還沒和遠端配對 | `git push -u origin <分支名稱>` |
| `Permission to ... denied to <帳號>`（403） | 登入了別的帳號，或還沒接受協作邀請 | `git credential-manager github list` 確認帳號；到 GitHub 通知或 email 接受邀請 |
| `Password for 'https://...github.com'` | 登入視窗被關掉，改問密碼；GitHub 不接受密碼 | 按 Ctrl+C 中斷，**不要輸入密碼**，重新 push 走瀏覽器登入 |
| `! [rejected] ... (fetch first)` | GitHub 上有你沒有的 commit | 先 `git pull --no-edit`，再 push |
| push 到 main 被拒絕（`protected branch`） | main 只能透過 PR 修改，你在 main 上 commit 了 | 看下方「不小心在 main 上 commit」 |
| 畫面卡在 vi | Git 打開編輯器要你寫訊息 | 輸入 `:wq` 再按 Enter |
| 我的格子變成紅色虛線框 | 座位檔少了引號或逗號，或某一行不是 8 個字 | 看框裡的說明，對照上面的範例修正 |

**不小心在 main 上 commit**：把 commit 搬到新分支，再讓 main 回到 GitHub 上的樣子。

```powershell
git switch -c issue-<編號>-<名字>
git push -u origin issue-<編號>-<名字>
git switch main
git reset --hard origin/main
```

> `reset --hard` 會丟掉還沒 commit 的修改，先用 `git status` 確認是乾淨的。

---

## 8. 隨時確認自己在哪

```powershell
git status
git branch
git log --oneline --graph --all -10
```
