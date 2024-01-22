# Reusable Workflow

[简体中文](./README.md) | English

**reusable-workflow** is a collection of reuseable **GitHub Workflow**.You can references to this project's **GitHub Workflow** and use it in your own projects without having to re-edit it.

## Push Mirror Git Repository

This is a **GitHub Workflow** to fully mirror a single **Git Repository** and push that mirror to other **Git Repository** in bulk.

After practical testing this **GitHub Workflow** can push an mirror to the following code hosting platform's **Git Repository** :

- [GitHub](https://github.com/)
- [GitLab](https://gitlab.com/)
- [Bitbucket](https://bitbucket.org/)
- [Codeup](https://codeup.aliyun.com/)
- [Coding](https://coding.net/)
- [Gitee](https://gitee.com/)
- [JihuLab](https://jihulab.com/)
- [GitCode](https://gitcode.net/)
- [GitLink](https://www.gitlink.org.cn/)

### How to use

The usage can be roughly summarized in three steps:

1. Add three new items to your **GitHub's Git Repository** under **Secrets and variables** in  **Setting**:
   1. In **Repository secrets** add: `SSH_PRIVATE_KEY`. Its value is the **SSH Private Key**, which has the authority to **pull** and **push** operations on the **Git Repository**
   2. In **Repository variables** add: `SOURCE_REPO`, whose value will be the address of **Source Git Repository** (preferably the **SSH**'s **Git** repository address)
   3. In **Repository variables** add: `DESTINATION_REPO`, whose value will be the address of **Target Git Repository** (preferably the **SSH**'s **Git** repository address), **multiple addresses** can use `|` separate
2. Create a `.yml` or `.yaml` file in a new directory `.github/workflows` at the root of the **GitHub's Git Repository** where you want to sync your mirror
3. Call this **GitHub Workflow** in the file created in the previous step, the following is an example of the call:

```yml
# .github/workflows/your-file-name.yml

# Custom Github Workflow name
name: My push mirror Git Repository

# Customize Github Workflow triggers
on: [ push, delete, create ]

jobs:
  # Custom job name
  call-push-mirror-git-repository:

    # --------------------------------------The code above can be changed as needed--------------------------------------
    # -----------------------------The code after this is: Call the core step of the GitHub Workflow (cannot be changed)-----------------------------

    uses: HeavenZhi/reusable-workflow/.github/workflows/push-mirror-git-repository.yml@main
    secrets:
      ssh_private_key: ${{ secrets.SSH_PRIVATE_KEY }}
    with:
      source_repo: ${{ vars.SOURCE_REPO }}
      destination_repo: ${{ vars.DESTINATION_REPO }}

    # -----------------------------The code before this is: Call the core step of the GitHub Workflow (cannot be changed)-----------------------------
    # --------------------------------------The code below can be changed as needed--------------------------------------

    # -----------------------------------------------------Optional parameter-----------------------------------------------------
      # is_clone_bare is an optional parameter of the boolean type(This parameter is not required). If this parameter is not set, the default value is false(This means using git clone --mirror to clone a mirror from the source Git repository).
      # This parameter determines whether to use git clone --bare to clone an image from the source Git repository.
      # If the source Git repository of the code hosting platform contains Pull Requests, and the target Git repository is Codeup, Coding, or Gitee, set is_clone_bare to true.
      # !!!Thank you for the solution given by the staff of Aliyun & Codeup!!!
      is_clone_bare: false

      # is_push_force is an optional parameter of the boolean type(This parameter is not required). If this parameter is not set, the default value is false.
      # This parameter determines whether to push an mirror to the target Git repository by force.
      # !!!Forced push is risky and will overwrite the raw data of the target Git repository!!!
      is_push_force: false
```

Optional: Parameters that can be left unset

- `is_clone_bare`: An optional parameter of the `boolean` type, the default value is `false`.
  - This parameter determines whether to use **--bare** to clone an image from the **Source Git Repository**.
  - If the **Source Git Repository** of the code hosting platform contains **Pull Requests**, and the target Git repository is **Codeup**, **Coding**, or **Gitee**, set `is_clone_bare` to `true`.
- `is_push_force`: An optional parameter of the `boolean` type, the default value is `false`.
  - This parameter determines whether to push an mirror to **Target Git Repository** by **--force**.
  - !!Forced push is risky and **will overwrite the raw data of Target Git Repository**!!

### Common error handling methods:

#### Codeup、Coding、Gitee

```shell
Warning: Permanently added 'codeup.aliyun.com' (RSA) to the list of known hosts.
remote: 权限被拒绝: branch:refs/pull/1/merge is illegal
To codeup.aliyun.com:60b0bc44b8301d20d58b51c2/HeavenZhi/test.git
 ! [remote rejected] main -> main (pre-receive hook declined)
 ! [remote rejected] refs/pull/1/head -> refs/pull/1/head (pre-receive hook declined)
 ! [remote rejected] refs/pull/1/merge -> refs/pull/1/merge (pre-receive hook declined)
error: failed to push some refs to 'codeup.aliyun.com:60b0bc44b8301d20d58b51c2/HeavenZhi/test.git'
Error: Process completed with exit code 1.
```

```shell
Warning: Permanently added 'e.coding.net' (RSA) to the list of known hosts.
remote: [err=02] Push to refs/pull dir is rejected!        
remote: CODING 禁止 Push 到 refs/pull ref 目录        
remote: error: hook declined to update refs/pull/1/head        
remote: [err=02] Push to refs/pull dir is rejected!        
remote: CODING 禁止 Push 到 refs/pull ref 目录        
remote: error: hook declined to update refs/pull/1/merge        
To e.coding.net:an-yu/HeavenZhi/test.git
   c8ded6b..3db261c  main -> main
 ! [remote rejected] refs/pull/1/head -> refs/pull/1/head (hook declined)
 ! [remote rejected] refs/pull/1/merge -> refs/pull/1/merge (hook declined)
error: failed to push some refs to 'e.coding.net:an-yu/HeavenZhi/test.git'
Error: Process completed with exit code 1.
```

```shell
Warning: Permanently added 'gitee.com' (ED25519) to the list of known hosts.
remote: Powered by GITEE.COM [GNK-6.4]        
To gitee.com:HeavenZhi/test.git
   c8ded6b..3db261c  main -> main
 ! [remote rejected] refs/pull/1/head -> refs/pull/1/head (deny updating a hidden ref)
 ! [remote rejected] refs/pull/1/merge -> refs/pull/1/merge (deny updating a hidden ref)
error: failed to push some refs to 'gitee.com:HeavenZhi/test.git'
Error: Process completed with exit code 1.
```

The conditions for the recurrence of the error are:

> When calling the `${vars. SOURCE-REPO}` configured by this **GitHub Workflow** to point to exist  a **Pull Requests** in the **Source Git Repository** of the code hosting platform, cloning the mirror of the **Git Repository** and pushing it to the **Codeup**, **Coding**, and **Gitee** platforms will trigger these platforms to return this error.
>
> This is true even if the optional parameter `is_push_force` is configured to `true` when calling **Push Mirror Git Repository**.
> 
> PS:
> These platforms will be triggered to return this error, regardless of whether the **Pull Requests** are open or closed.

The reason for this error is:

> When creating **Pull Requests** in the **Source Git Repository** of the code hosting platform, a special reference that this code hosting platform can recognize will be automatically created, which is not recognized by other code hosting platforms.
> 
> When the **Git Repository** containing this particular reference is pushed to other code hosting platforms, some code hosting platforms will consider this unrecognized special reference to be an illegal reference and refuse to accept the mirror containing the illegal reference for push operation.

The big guy of <b style="color:red">Alibaba Cloud & Codeup</b> explained the reason for the error and gave the solution. The solution can also solve the same problem of **Coding** and **Gitee**.

![Aliyun_resolve](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@main/image/Aliyun_resolve.png)

Based on the solution given by the big guy of <b style="color:red">Alibaba Cloud & Codeup</b>, now only need to set the optional parameter `is_clone_bare` to `true` when calling this **GitHub Workflow**.

<b style="color:red">!!!Thank you for the free technical support provided by Ali Cloud & Codeup!!!</b>

#### When the optional parameter is_push_force is used

When the **Source Git Repository** uses the optional parameter `is_push_force` to push the **Target Git Repository** of the code hosting platform such as `gitlab.com`, `jihu.com`, `gitcode.net`, `gitlink.org.cn`, Errors may occur. The following is a list of common error handling methods.

##### GitLab、Jihu

```shell
Warning: Permanently added 'gitlab.com' (ED25519) to the list of known hosts.
remote: GitLab: You are not allowed to force push code to a protected branch on this project.
To gitlab.com:HeavenZhi/test.git
 ! [remote rejected] main -> main (pre-receive hook declined)
error: failed to push some refs to 'gitlab.com:HeavenZhi/test.git'
Error: Process completed with exit code 1.
```

The reason for this error is that **Git Repository** on platforms such as `gitlab.com`, `jihu.com`, etc. prohibit `--force` push to the default branch.

To solve this problem is also very simple, allow the **Git Repository** of platforms such as `gitlab.com`and `jihu.com` to **force push** against the default branch:

![GitLab_config_force](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@main/image/GitLab_config_force.gif)

##### GitCode

```shell
Warning: Permanently added 'gitcode.net' (RSA) to the list of known hosts.
remote: GitLab: The default branch of a project cannot be deleted.
To gitcode.net:Heaven__Zhi/test.git
 ! [remote rejected] master (pre-receive hook declined)
 ! [remote rejected] main -> main (pre-receive hook declined)
error: failed to push some refs to 'gitcode.net:Heaven__Zhi/test.git'
Error: Process completed with exit code 1.
```

The reason for this error is:

1. The default branch of the **Source Git Repository** is different from the default branch of the **Target Git Repository** of the `gitcode.net` platform.
2. **Git Repository** for platforms such as `gitcode.net` do not allow the default branch to be deleted.

To push an mirror to `gitcode.net`'s **Git Repository** using `--force` is a bit more complicated and requires a three-step setup.

1. In the **Git Repository** of `gitcode.net` create a branch with the same name as the default branch of the **Source Git Repository**:
   ![GitCode_added_main_branch](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@main/image/GitCode_added_main_branch.gif)
2. In `gitcode.net`'s **Git Repository**, set the default branch to the one you created in the previous step:
   ![GitCode_switch_default_branch](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@main/image/GitCode_switch_default_branch.gif)
3. Unprotect the `master` branch in `gitcode.net`'s **Git Repository**:
   ![GitCode_move_protection](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@main/image/GitCode_move_protection.gif)

##### GitLink

```shell
Warning: Permanently added 'code.gitlink.org.cn' (ED25519) to the list of known hosts.
remote: 
remote: Gitea: branch master is the default branch and cannot be deleted        
To code.gitlink.org.cn:HeavenZhi/test.git
 ! [remote rejected] master (pre-receive hook declined)
 ! [remote rejected] main -> main (pre-receive hook declined)
error: failed to push some refs to 'code.gitlink.org.cn:HeavenZhi/test.git'
Error: Process completed with exit code 1.
```

The reason for this error is:

1. The default branch of the **Source Git Repository** is different from the default branch of the **Target Git Repository** of the `gitlink.org.cn` platform.
2. **Git Repository** for platforms such as `gitlink.org.cn` do not allow the default branch to be deleted.

In the **Git Repository** of `gitlink.org.cn` create a branch with the same name as the default branch of the **Source Git Repository**, and set the new branch as the default branch:

![GitLink_switch_default_branch](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@main/image/GitLink_switch_default_branch.gif)
