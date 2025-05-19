# 完整开发流程
<img width="1462" alt="image" src="https://github.com/user-attachments/assets/1de2a576-e00c-4933-9f69-b0efeee3334f" />


### 详细说明
1. **dev 开发阶段**：根据 Jira 分配的任务从 prod 拉取分支，命名需严格按照下方分支规范（目前以 featureA 为例）；开发自测通过后，需合并入 dev 分支查看是否有冲突并解决，然后再次验证功能
2. **stg 提测阶段**：featureA 合入 dev 分支自测通过后，便可合入 stg 分支，依旧需要查看是否有冲突并解决；在 stg 环境发版自测通过后，可进行提测；提测失败需要在 featureA 分支进行修复，后续需要重新先后合并入 dev、stg 分支进行再次验证
3. **rel 验收阶段**：featureA 在测试验收通过后，需合入 rel 分支，待所有该版本都需要上线的 feature、bugfix 等分支成功合入 rel 分支后，需要给该版本打上 tag，然后使用 Jenkins 为该版本打包，打包完成后需发送邮件请示版本部署（邮件模版参考下方）；出现 bug 的情况下需重新回到开发阶段的 featureA 分支进行及时修复；测试及产品验收通过后，申请将 rel 分支合入 prod 分支
4. **prod 上线阶段**：rel 成功合入 prod 后，需要构建 prod 镜像，构建成功后需要再次发送邮件请示正式上线，上线后待顾客验收；线上环境发现的紧急 bug 需要先评估是否退版还是紧急修复，紧急修复需拉取 hotfix 分支进行修复，非紧急 bug 需拉取 bugfix 分支进行修复，流程与功能开发分支一致
5. **hotfix 紧急修复**：hotfix 分支完成自测通过后，需要直接合入 rel 分支进行验证，验证通过后执行 rel 验收阶段后续相同步骤，顾客验收通过后，需将 hotfix 分支同步合入 dev、stg 分支


## 分支规范
为了规范团队的分支管理，提高协作效率，推荐使用以下 Git 分支命名规则。

---

### 命名结构

一般分支名称通常以 `类型/描述` 的结构命名：

```
<type>/<JiraID>-<task_name>
```

---

### 📂 分支类型（type）

| 类型        | 说明                                                                 | 示例                                    |
|-------------|----------------------------------------------------------------------|-----------------------------------------|
| **feature** | 新功能开发、优化等                                                          | `feature/221-login_page`                    |
| **bugfix**  | 修复 bug                                                            | `bugfix/532-login_error`                |
| **hotfix**  | 紧急修复生产环境问题                                                | `hotfix/325-payment_crash`                  |

---

### 注意事项

1. **分支名称应清晰简洁**：type 只能取以上其中一种，JiraID 为对应 Jira 上的任务ID（JiraID 递增，自动排序方便查看），task_name 尽量描述简洁具体任务内容，
2. **统一使用小写字母**：task_name 开发者根据具体任务内容取不超过 3 个单词的名称，单词之间用 `_` 分隔，最后以  `-` 接续 JiraID。
3. **避免使用特殊字符**：如 `@`, `#`, `$` 等，避免潜在兼容性问题。
4. **及时删除无用分支**：测试或实验分支完成任务后应及时清理。

### 分支限制
1. prod 分支只允许从 rel 合并，且只能通过 Gitlab merge request 操作
2. 不允许在 rel、prod 分支上直接 push 代码
3. 各 feature、bugfix、hotfix 分支之间不可互相合并

### 发信模版
收件人：loraliao@star-link.tech <br>
抄送 ：qp-rd
```
【0411】后台升级  -- 【日期】服务名升级
1. 升級環境：QP REL / PROD
2. 需要停機：否
3. 需要執行SQL：否 / 
      粘贴 sql 文件夹地址（https://hk-qp-git.axiom-gaming.tech/qp-game-v1/rd-backend-core/sql-game-db/-/tree/main/prod-qp-db-release/2025-05-09-add-ebg
4. 操作流程：
	1） 一键维护所有游戏
	2） 稍微过个半分钟左右，执行 SQL
	3） 拉取最新镜像，部署服务
	4） 登陆 接入管理后台 - 缓存管理 - Redis 缓存列表 - 点击更新 游戏类型缓存数据
	
5. 是否有模塊配置需要異動修改：否
6. 升級版號： 

	www-office-game-static: 
	www-office-game: 

	www-office-gameapi-static: 
	www-office-gameapi: 

	www-office-platform-static: 
	www-office-platform: 

	srv-api-office-platform: ❌

	srv-api-office-game: ✅ 

7.升級內容：		
	1） 平台後管理後台-時間查詢修復 bug 修复
	2） Rel數據查詢-額度紀錄修復 bug 修复
	3） 機器人-一鍵啟用 功能完成
	4） gameapi-API测试地址管理-游戏类型需新增百人炸金花 bug 修复
```






