# 完整开发流程

 <img width="1383" alt="image" src="https://github.com/user-attachments/assets/d143f335-db01-42fa-9e79-34108c8cf714" />

## 详细说明
1. **dev 开发阶段**：根据 Jira 分配的任务从 prod 拉取分支，命名需严格按照下方分支规范（目前以 featureA 为例）；开发自测通过后，需合并入 dev 分支查看是否有冲突并解决，然后再次验证功能
2. **stg 提测阶段**：featureA 合入 dev 分支自测通过后，便可合入 stg 分支，依旧需要查看是否有冲突并解决；在 stg 环境发版自测通过后，可进行提测；提测失败需要在 featureA 分支进行修复，后续需要重新先后合并入 dev、stg 分支进行再次验证
3. **rel 验收阶段**：featureA 在测试验收通过后，需合入 rel 分支
4. **prod 上线阶段**：
5. **hotfix 紧急修复**：


# 分支规范
为了规范团队的分支管理，提高协作效率，推荐使用以下 Git 分支命名规则。

---

## 命名结构

一般分支名称通常以 `类型/描述` 的结构命名：

```
<type>/<JiraID>-<task_name>
```

---

## 📂 分支类型（type）

| 类型        | 说明                                                                 | 示例                                    |
|-------------|----------------------------------------------------------------------|-----------------------------------------|
| **feature** | 新功能开发、优化等                                                          | `feature/221-login_page`                    |
| **bugfix**  | 修复 bug                                                            | `bugfix/532-login_error`                |
| **hotfix**  | 紧急修复生产环境问题                                                | `hotfix/325-payment_crash`                  |

---

## 注意事项

1. **分支名称应清晰简洁**：type 只能取以上其中一种，JiraID 为对应 Jira 上的任务ID（JiraID 递增，自动排序方便查看），task_name 尽量描述简洁具体任务内容，
2. **统一使用小写字母**：task_name 开发者根据具体任务内容取不超过 3 个单词的名称，单词之间用 `_` 分隔，最后以  `-` 接续 JiraID。
3. **避免使用特殊字符**：如 `@`, `#`, `$` 等，避免潜在兼容性问题。
4. **及时删除无用分支**：测试或实验分支完成任务后应及时清理。

## 分支拉取规范
1. 统一从 origin prod 最新提交记录拉取 feature 分支
2. 未上线 prod 但在 rel 发现的 bug 需要从最新 origin rel 拉取 bugFix 分支进行修复
3. dev、stg 发现的 bug 直接在对应的 feature 分支进行修复即可
4. 预计上线版本内容在 stg 测试验证通过后，在 origin stg 最新提交拉取 release 分支，该分支上只允许提交一次 commit 修改版本号


# 分支提交规范

1. feature 分支只允许合并入 dev、stg 分支，且在合并入之前，原则上最多只能有 5 次commit记录，其他类型的分支（bugfix、hotfix等）理论上应该更少提交记录
2. bugfix、hotfix 分支允许合入 dev、stg、rel 分支，只有 rel 分支仅通过 Gitlab merge request 合并入 prod 分支
3. 不允许在 dev、stg、rel、prod 分支上直接 push 代码（Gitlab 做控制）
4. feature 分支在合入 dev、stg 分支时，需按照版本排期先后合入，featureA 分支是在 featureB 分支前合并入 dev，则合并入 stg 也需要在 featureB 之前
5. rel 分支要不要？要的话需要 测试将排期错开 拉 rel 分支

改善点：Jenkins 版本能否与 日期之类的关联 v1.0.0.12 => v1.0.5.15
      开发只关注 dev stg rel? 版本控制是否也需要测试控制






![image](https://github.com/user-attachments/assets/04b1f923-1616-402e-b59d-1ac89bf3966b)




<img width="1354" alt="image" src="https://github.com/user-attachments/assets/f9c77aab-03a4-411a-9ab9-147691da45a0" />



<img width="1451" alt="image" src="https://github.com/user-attachments/assets/d75d307e-60db-4346-8d25-2576b050be0a" />








merge 顺序错了！！ 0509 后续再多提交装作 bugFix 然后再合到 stg 后面再把 fea 合到 rel rel 再多个 ver 提交 prod merge 后续看下如何统一所有环境提交记录 











# `git reset` 与 `git rebase -i HEAD~` 的使用步骤与场景

## 一、`git reset` 的使用步骤与场景

### **1. 使用步骤**
1. **查看提交历史**：
   使用 `git log --oneline` 检查提交历史，找到需要回退的目标提交：
   ```bash
   git log --oneline
   ```
   示例输出：
   ```text
   abc123 First commit
   def456 Second commit
   ghi789 Third commit
   jkl012 Fourth commit
   mno345 Fifth commit
   ```

2. **选择一种 `reset` 模式**：
   - `--soft`：保留提交记录，文件更改会放入暂存区。
   - `--mixed`（默认）：保留文件更改，但清空暂存区。
   - `--hard`：直接回退到目标提交，所有后续更改都被丢弃。

   **示例命令**：
   ```bash
   git reset --soft abc123
   ```

3. **重新提交更改（可选）**：
   如果使用 `--soft` 或 `--mixed` 模式，修改完成后重新提交：
   ```bash
   git commit -m "New commit message"
   ```

4. **推送到远端（如果需要）**：
   如果已经将分支推送到远端，可能需要强制推送：
   ```bash
   git push origin <branch-name> --force
   ```

---

### **2. 使用场景**
1. **合并多个提交**：
   将多个提交合并为一个新的提交，清理提交历史。
   - 示例：将最近的 3 次提交合并为 1 次：
     ```bash
     git reset --soft HEAD~3
     git commit -m "Combine changes into one commit"
     ```

2. **撤销最近的提交**：
   如果误提交了代码，可以回退到上一个提交。
   - 示例：
     ```bash
     git reset --mixed HEAD~1
     ```

3. **重置分支到特定提交**：
   将分支状态恢复到指定提交，清理不需要的更改。
   - 示例：
     ```bash
     git reset --hard abc123
     ```

---

## 二、`git rebase -i HEAD~` 的使用步骤与场景

### **1. 使用步骤**
1. **运行交互式 rebase**：
   使用 `git rebase -i HEAD~<number>` 命令打开交互式编辑器。例如：
   ```bash
   git rebase -i HEAD~5
   ```

2. **修改提交操作**：
   在交互式编辑器中，按顺序列出最近的提交记录，类似以下内容：
   ```text
   pick abc123 First commit
   pick def456 Second commit
   pick ghi789 Third commit
   pick jkl012 Fourth commit
   pick mno345 Fifth commit
   ```
   - **`pick`**：保留提交。
   - **`squash`（或 `s`）**：将当前提交合并到前一个提交。
   - **`edit`**：修改提交内容。
   - **`reword`**：修改提交信息。

   示例：将 `ghi789` 和 `jkl012` 合并到 `def456` 中：
   ```text
   pick abc123 First commit
   pick def456 Second commit
   squash ghi789 Third commit
   squash jkl012 Fourth commit
   pick mno345 Fifth commit
   ```

3. **编辑提交信息**：
   保存后，Git 会提示你编辑合并后的提交信息。按需修改后保存并退出。

4. **解决冲突（如果有）**：
   如果 rebase 过程中出现冲突，按照提示解决冲突后继续：
   ```bash
   git add <file-name>
   git rebase --continue
   ```

5. **推送到远端（如果需要）**：
   完成 rebase 后，强制推送到远端：
   ```bash
   git push origin <branch-name> --force
   ```

---

### **2. 使用场景**
1. **整理提交历史**：
   将多个零碎的提交合并为一个有意义的提交，便于代码审查。
   - 示例：将最近 4 次提交整理为 1 次：
     ```bash
     git rebase -i HEAD~4
     ```

2. **修改提交信息**：
   更新提交历史中的某条提交信息。
   - 示例：修改最近 3 次提交的信息：
     ```bash
     git rebase -i HEAD~3
     ```
     将目标提交标记为 `reword`。

3. **调整提交顺序**：
   在交互式编辑器中，重新排列提交的顺序，便于代码逻辑清晰。

4. **删除不需要的提交**：
   在交互式编辑器中，将目标提交的操作改为 `drop`。

---

## 三、`git reset` 与 `git rebase -i` 的对比

| 特性                     | `git reset`                                           | `git rebase -i`                                       |
|--------------------------|-------------------------------------------------------|------------------------------------------------------|
| **操作范围**             | 直接修改当前分支的 HEAD 和索引状态。                    | 逐步重新应用提交，允许修改、合并、删除提交历史。       |
| **是否保留提交顺序**     | 不保留，直接回退到目标提交后一步完成。                  | 保留提交顺序，并允许手动调整提交顺序。                 |
| **冲突可能性**           | 无需逐步应用提交，一般不会触发冲突。                    | 逐步应用提交，可能会触发冲突。                        |
| **适用场景**             | 简单的回退操作或合并提交。                              | 复杂的提交历史整理与精细化调整。                       |

---

## 四、总结

- 使用 **`git reset`** 时：
  - 适合快速回退提交、合并提交，操作简单直接。
  - 不适合需要复杂调整或保留提交顺序的场景。

- 使用 **`git rebase -i`** 时：
  - 适合需要精细化整理提交历史的场景。
  - 需要注意解决冲突，并在操作完成后强制推送更新。
 
