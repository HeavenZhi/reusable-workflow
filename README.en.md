# Reusable Workflow

[简体中文](./README.md) | English

**reusable-workflow** is a collection of reuseable **GitHub Workflow**.You can references to this project's **GitHub Workflow** and use it in your own projects without having to re-edit it.

## Push Mirror Git Repository

This is a **GitHub Workflow** to fully mirror a single **Git Repository** and push that image to other **Git Repository** in bulk.

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