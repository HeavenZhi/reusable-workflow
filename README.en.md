# Reusable Workflow

[简体中文](./README.md) | English

**reusable-workflow** is a collection of reuseable **GitHub Workflow**.You can references to this project's **GitHub Workflow** and use it in your own projects without having to re-edit it.

## Push Mirror Git Repository

This is a **GitHub Workflow** to fully mirror a single **Git Repository** and push that mirror to other **Git Repository** in bulk.

After practical testing this **GitHub Workflow** can push an mirror to the following code hosting platform's **Git Repository** :

- `gitlab.com`
- `bitbucket.org`
- `gitee.com`
- `jihulab.com`
- `codeup.aliyun.com`
- `coding.net`
- `gitcode.net`
- `gitlink.org.cn`

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
      # is_force is an optional parameter of the boolean type(This parameter is not required). If this parameter is not set, the default value is false.
      # This parameter determines whether to push an mirror to the target Git repository by force.
      # !!!Forced push is risky and will overwrite the raw data of the target Git repository!!!
      is_force: false
```

Optional: Parameters that can be left unset

- `is_force`: An optional parameter of the `boolean` type, the default value is `false`.
  - This parameter determines whether to push an mirror to **Target Git Repository** by **force**.
  - !!Forced push is risky and **will overwrite the raw data of Target Git Repository**!!

### notice

#### When the optional parameter: is_force is used

When the **Source Git Repository** uses the optional parameter `is_force` to push the **Target Git Repository** of the code hosting platform such as `gitlab.com`, `jihu.com`, `gitcode.net`, `gitlink.org.cn`, Errors may occur. The following is a list of common error handling methods.

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

![GitLab_config_force](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@dev/image/GitLab_config_force.gif)

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
   ![GitCode_added_main_branch](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@dev/image/GitCode_added_main_branch.gif)
2. In `gitcode.net`'s **Git Repository**, set the default branch to the one you created in the previous step:
   ![GitCode_switch_default_branch](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@dev/image/GitCode_switch_default_branch.gif)
3. Unprotect the `master` branch in `gitcode.net`'s **Git Repository**:
   ![GitCode_move_protection](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@dev/image/GitCode_move_protection.gif)

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

![GitLink_switch_default_branch](https://cdn.jsdelivr.net/gh/HeavenZhi/reusable-workflow@dev/image/GitLink_switch_default_branch.gif)
