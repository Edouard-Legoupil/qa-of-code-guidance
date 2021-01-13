# Version control

Version control is managing changes to your analysis over time. In this chapter, we primarily discuss the benefits of using [the Git version control system](https://git-scm.com/) .

## Why version control?

As we discussed in [](principles.md), an audit trail is essential for assuring quality analysis. It's important for us to be able to answer the following questions about our analysis:
* What changes have been made to our project?
* When were those changes made?
* Why were those changes made?
* Who made those changes?

Version control software, like Git, records the answers to these questions throughout the development of a project. We can detail our development decisions alongside changes, using commit messages and Pull or Merge Requests. In turn, this keeps our analysis workspace clean of commented-out old code and numerous files containing previous versions.

```{figure} https://imgs.xkcd.com/comics/documents.png
---
width: 30%
name: file_names
alt: Comic demonstrating poor file naming, like "Untitled 138 Copy.docx".
---
Documents, from [xkcd](https://xkcd.com/1459/)
```

When used effectively, version control helps us to identify which changes have negatively impacted our work and remove them. Furthermore, a detailed audit trail allows us to refer to specific versions of our code that have been used to produce outputs, which is important for reproducing our analysis.

Git is invaluable when recording and combining changes from multiple developers, as merging allows us to resolve conflicts between overlapping changes. Using a remote Git repository maintains a single source of truth, even when multiple individuals are working on a project. Additionally, version control platforms, like GitHub and GitLab, can make it easier to track and review ongoing work. This avoids duplication of effort and keeps review embedded in the development workflow.


## What should I version control?

Ideally, you should include any code that is required to run your analysis. In a public repository, you may need to omit confidential or sensitive aspects of the project.

```{caution}

You should **not** include the following in your code repository:
* passwords, credentials or keys
* configuration files that are environment-dependent (e.g. containing file paths)
* code that contains sensitive information
  * for example, code that describes a method for fraud detection
  * or code that contains references to personally identifiable data
  * or code that might compromise security protocols
* data, except for small example datasets
```

See [](excluding-from-git) for details on how to mitigate the risk of including sensitive information in a Git repository.

You might include example (configuration)[configuration.md] files, or documentation describing how configuration is applied. However, the exact configuration for a particular run of your code should be recorded by logging for reproducibility purposes.

The data we use for analysis is often unreleased or sensitive. Unpublished, sensitive or disclosive data should never be shared in a code repository. As a rule of thumb, only small dummy/example datasets should be included. It is still important to version the data that we use for our analyses, but this should be done independently to our code.


## Git

Git is a distributed version control system. This means that all users have access to a complete and self-contained history of changes to a given project. The software can be used to record local changes, with the option of then synchronising these changes with a central, remote repository. Remote repositories are typically hosted on a platform like GitHub or GitLab.

The following sections describe useful concepts for using Git to version control your analysis. We use examples of Git commands throughout, but do not provide detailed descriptions of Git usage. If you are not familiar with using command line tools, or Git specifically, you should first look into [introductory training](git-learning).


### Appropriate use of branching

Branches are independent copies of a project's history, copied from a parent branch at a specific point in its history. A new branch is typically created to make a change to your code, which might be building a new feature or fixing a bug in your analysis. Multiple branches can be created from a single parent branch when multiple changes are being worked on independently. This is especially useful when when multiple analysts are working collaboratively and are making changes in parallel.

Once changes have been implemented and sufficiently quality assured (think documentation and testing), the branch containing the changes can be merged onto another branch. The target branch for this merging is typically the parent branch, which the branch was created from. During merging any overlapping or "conflicting" changes between the current and target branches are resolved.

It is important to note that your approach to branching within a project should be proportional to number of collaborators and the complexity of the development work. For example, if working alone on a project you might only be working a single development branch at any moment in time, or might choose to track all changes on a single `main` branch. The `main` or `master` branch is the default highest level branch in a Git repository.

```{figure} ./_static/git_main.png
---
width: 70%
name: git_master
alt: Commits along a single "main" Git branch.
---
Commits along a single "main" Git branch.
```

More complex projects may warrant using branching. When using branches, the `main` branch should be considered as the most "stable" branch in the repository - meaning that the code on this branch builds successfully and executes as expected. When making changes to our code, these changes may initially be less stable or reliable. As such, we make these changes on a new branch so that the code on our `main` branch is unaffected. As the changes to our code are refined, it becomes safer to merge these changes onto a higher level branch such as `main`. For example, when the code has been reviewed and suitably tested. You should aim to only merge onto a more stable branch when you don't expect it to break the working code on the target branch.

```{figure} ./_static/git_feature.png
---
width: 70%
name: git_feature
alt: Creating a feature branch from a main branch.
---
Working on changes on a single `feature` branch.
```

Here we show a single `feature` branch being created from the `main` branch. The work is initially quite experimental, but is refined over a few commits. The complete, working feature is finally merged back onto the `main` branch.

Many small scale projects iteratively work on individual feature or development branches in this way. The [GitHub flow branching strategy](https://guides.github.com/introduction/flow/) uses this approach in combination with [Pull Requests](pull-requests), to incorporate peer review into the development workflow.


```{figure} ./_static/git_develop.png
---
width: 90%
name: git_develop
alt: Iteratively creating and merging a develop branch from a main branch.
---
Iteratively working on a `develop` branch.
```

We can create multiple branches from our `main` branch at any point. We might do this when multiple features are being developed in parallel, or perhaps when multiple analysts wish to make changes to the same piece of code independently.

```{figure} ./_static/git_multiple_features.png
---
width: 90%
name: git_multiple_features
alt: Two features branches created from a single main branch.
---
Working on multiple parallel branches.
```

Here we create two feature branches from `main`. Work on each feature is carried out independently of the other feature and can be merged onto `main` once it is complete. If changes from separate branches affect the same files, merging these branches to `main` may lead to merge conflicts. In these cases you should ensure that you resolve the conflicts to keep the desired overall change.

In addition to having multiple branches from our `main` branch, we can also create deeper branches from our other branches. You might create additional sub-branches when the development of a feature involves multiple sub-tasks, or when a problem needs to be fixed during development of a feature. Deeper branching usually reflects work that is less stable and more exploratory or experimental.

```{figure} ./_static/git_deeper_branching.png
---
width: 90%
name: git_deeper_branching
alt: A sub branch being created from a feature branch.
---
Using deeper branching to manage sub-tasks.
```

In this example, we have created a `feature` branch. Early in development of the feature we want to fix a bug that has been created, but this work can be carried out independently to the remaining development of the feature. As such, we create another, deeper branch to carry out the bug fix. Once the bug is fixed, we merge the `bug-fix` onto our `feature` branch. And finally, the finished `feature` can be merged back onto `main`.

The [Git flow branching strategy](https://nvie.com/posts/a-successful-git-branching-model/) describes an alternative to progressively merging our changes onto `main`. Development work is instead branched from a `develop` branch. Merges from `develop` onto the `main` branch are only used to release a new version of the code. This approach can be useful when code from the `main` branch is deployed directly into production, however, analysts should opt to use the most simple and beneficial approach to branching depending on their project.

```{note}
Although we have used very simple branch names in the examples above, it's important that you use informative names for your branches in practice. If using an issue tracker (e.g. GitHub Issues or Jira), it can be useful to include the issue number in branch names (e.g. `#155-fix-index-aggregation`). This makes it easy to trace the branch back to the associated issue or task. Otherwise, aim to use meaningful names that describe the feature or bug that the changes will be focussed on. 
```

### Commit standards

Commits are collections of changes to one or more files in a repository. Every commit is attributed to the author of the changes, providing an audit trail. Each commit has a unique hash - or identifier - associated with it, which has a long (e.g. `121b5b4f18231e4ee32c9c61f6754429f9572743`) and short version (e.g. `121b5b4`). These hashes allow us to refer to specific changes, but each commit also has an associated message that is used to describe the changes.

Most commit messages are short and informative, but in some cases you may want to provide more detail. Common standards follow this model commit message from ["A note about Git commit messages"](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html):

```{code-block} text
Capitalized, short (50 chars or less) summary

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of an email and the rest of the text as the body. The blank
line separating the summary from the body is critical (unless you omit
the body entirely); tools like rebase can get confused if you run the
two together.

Write your commit message in the imperative: "Fix bug" and not "Fixed bug"
or "Fixes bug". This convention matches up with commit messages generated
by commands like git merge and git revert.

Further paragraphs come after blank lines.

- Bullet points are okay, too

- Typically a hyphen or asterisk is used for the bullet, followed by a
  single space, with blank lines in between, but conventions vary here

- Use a hanging indent
```

A concise summary of a your change is usually sufficient, but remember that it is these commit messages that will be used in the future to understand what changes have been made to your project. You might rely on these to identify where an error has been introduced, so it is important that you write these messages clearly and informatively.

How often you commit when working on a project should depend on what benefit you would like to get from versioning your work. When reviewing code that has been version controlled, the commit history provides a chronological summary of the changes that have been made to a project. To make this audit trail useful, commits should be made whenever a discrete unit of work has been completed. When useful commit messages have been used to describe these units of work, it is easier for others to understand steps taken to develop the analysis to its current state.

You should also make commits whenever you have made changes that you might wish to revisit or undo. As Git allows us to easily `revert` changes by referring to a commit's hash, you should try to ensure that each commit only contains changes to single aspect of your analysis. Consider a commit that includes changes to several files. We discover that one change in this commit has created a bug in our analysis code. We can revert the commit to remove the bug, but this also removes the other changes that were made in the same commit. As such, we'll need to invest more effort to work out which change causes the bug and selectively undo that change.

### Versioning large files

When versioning your repository, Git stores compressed copies of all previous versions of each file. Despite the file compression, this means that versioning very large or binary files quickly increase the size of your repository's history, especially if there are multiple versions of them. The size of your Git history determines how long it takes to `clone` or `pull` and `push` changes to and from your remote repository. This includes when a continuous integration platform downloads your repo to run tests and other checks. Therefore, storing large files in Git typically slows down your development workflow.

[Git Large Files Storage (LFS)](https://git-lfs.github.com/) is a Git extension that allows you to version large files, but without storing the files in your repository history. Large files in your repository's history are instead replaced with a small text-based pointer. This pointer references versions of the actual files, which are stored in a separate part of your remote repository (e.g. GitHub or GitLab). When you `pull` a repository including large files, only the current version of the file is retrieved from the remote server, rather than its whole history. This reduces the size of your local repository and the time taken to `push` and `pull` changes. [Git-LFS integrates well with a normal Git workflow](https://www.youtube.com/watch?v=uLR1RNqJ1Mw) and can be used for specific files, or even all files of a particular type within your repository.

Other tools, including [git-annex](https://git-annex.branchable.com/) can be used for a similar purpose.

Despite this support for large files, we recommend that remote Git repositories are not used to store data. Versioning of your data could instead be handled independently to your code; the version of your code should not be influenced directly by changes in the data and vice versa. This separation can be achieved using a tool like [DVC](https://dvc.org/), which allows you to specify where data versions are store (locally or on the cloud).


### Releases (tagging)

Regularly `commit`ing changes using Git helps us to create a thorough audit trail of changes to our project. However, there may be discrete points in the history of the project that we want to mark for easier future reference. Let's face it, commit hashes like `121b5b4` don't exactly stick in your mind.

To reference specific points in project's history, Git allows us to create "tags". These tags essentially act as an alias for a particular commit hash, allowing us to refer to it by an informative label. In analytical projects, we might use tags to mark a particular model version or an important stage of our analysis. For example, we might tag code that has been used to generate a particular output so that it can easily be accessed to reproduce that output in future. If developing a package as part of your analysis, these tags are also commonly used to indicate new package versions.

By default, tags will reference the current position in history (i.e. the latest commit or `HEAD`). An annotated tag might be created for a new model version like so:

```{code-block}
git tag -a v0.2.7 -m "Model version 0.2.7"
git push origin v0.2.7
```

You can also retrospectively tag an older commit, by providing that `commit`'s hash:

```{code-block}
git tag -a v0.1.0 -m "Model version 0.1.0" 9fceb02
git push origin v0.1.0
```

Once tags have been created, these locations in the projects history can be easily recovered by either checking out:

```{code-block}
git fetch --all
git checkout tags/v0.1.0 -b new_branch_name
```

or cloning the tag:

```{code-block}
git clone https://github.com/<user>/<repo>.git --branch=v0.1.0
```


(excluding-from-git)=
## Avoid commiting sensitive information to Git repositories

Code itself is very rarely sensitive, so we should be open to sharing it. However, analysts may often want to use sensitive information in their analysis. This might be in the form of credentials, used to access a service, or data that contains personally identifiable information.

In these cases, we need to minimise the risk of inadvertently sharing this information with our code. This subsection suggests how you might mitigate this risk in your analysis.


### .gitignore files

As mentioned above, you may not want to include all of the files associated with your project in a Git repository. A `.gitignore` file allows you to exclude folders or files from being tracked by Git. Within this file, you provide a list of text patterns. If a folder matches one of your `.gitignore` file patterns, none of the files or folder below that folder will be tracked. If an individual file matches one of the patterns, this file will not be tracked.


Given this example `.gitignore` file:

```
secrets.yaml
sandpit/
*.csv
```

The first pattern tells Git to ignore any file with the exact name `secrets.yaml`. The second will ignore all files within a folder named `sandpit/`. Note that if there are multiple folders in the project named `sandpit/`, they will all be excluded. You should make the pattern more specific if you want to exclude particular directories. The third pattern will exclude all `.csv` files, regardless of which folder or sub-folder they are in.

```{caution}
Files that are already being tracked by Git will not be excluded when you add new patterns to your `.gitignore` file. Ensure that you set up your `.gitignore` before adding files to be tracked in your repository.
```

Please see the [Git documentation for `.gitignore`](https://git-scm.com/docs/gitignore) for more details.

While we may not want to share files containing credentials, configuration and data, it can be useful to provide dummy examples of these files. Demonstrating the format of these files can help others to reuse your code, without sharing sensitive information.


### Using environmental variables

If your code depends on credentials of some kind, these should not be explicitly written in your code. They can be stored in configuration files, which should be excluded from version control, or better still in local environment variables.

Environment variables are variables which are available in a particular environment. In this context, our environment is the user environment that we are running our code from.

In Unix systems (e.g. Linux and Mac), environment variables can be set in the terminal using `export` and deleted using `unset`:

```
export SECRET_KEY="mysupersecretpassword"
unset SECRET_KEY
```

In Windows, the equivalent to this is:

```
setx SECRET_KEY "mysupersecretpassword"
reg delete HKCU\Environment /F /V SECRET_KEY
```

Once stored in environmental variables, these variables will remain available on your machine until they are removed. You can access this variable in your code like so:

````{tabs}

```{code-tab} py
import os

my_key = os.environ.get("SECRET_KEY")
```

```{code-tab} r R
my_key <- Sys.getenv("SECRET_KEY")
```

````

Note that you may need to open a new terminal to show that a variable has been removed.


### Excluding `.Rdata` and `.Rhistory` files

R saves objects from your workspace (working environment) into an `.RData` file at the end of your session. This can save time when reloading large objects into R, when returning to a project. Depending on your settings, it may prompt you to ask if you would like to save them or it may do this automatically. If you have read sensitive information into R during the session, this information may be saved to the `.RData` file. As such, we must be careful not to include this information in commits to remote repositories.

Similarly, R stores a record of all commands executed in your session in a `.Rhistory` file. If you have referenced sensitive information directly in your R commands, then this information will be written to the `.Rhistory` file.

To handle this, we can exclude these files (via `.gitignore`) or prevent R from generating them. If you do not use these files, it is safest not to generate them in the first place.

In Rstudio, you can disable writing of these files via `Tools > Global options`:
* Under Workspace, select `Never` for `Save workspace to .RData on exit:`.
* Under History, deselect `Always save history`.


### Jupyter Notebooks

By default, Jupyter Notebooks save a copy of the data that is used to create cell outputs. For example, the data used to generate a chart, table or print a section of a dataframe. This can be useful for sharing your code outputs without others needing to re-execute the code cells.

If you work with sensitive datasets in notebooks, this means that your notebooks may store sensitive data to display cell outputs. If these notebooks are subsequently shared in code repositories, you may be making sensitive data available to unauthorised individuals.

It is [not currently possible to prevent the notebooks from retaining cell outputs](https://github.com/ipython/ipython/issues/1280).

The best way to handle this situation is to clear the outputs from your notebooks before commiting them to Git repositories. This can be done from the notebook itself, by going to the menu `Cell > All > Output > Clear` and then saving your notebook. Alternatively, this can be done from the command line, by running this command with your notebook file path:

```
jupyter nbconvert --ClearOutputPreprocessor.enabled=True --inplace <notebook_file_path>
```

This approach is quite laborious and could easily be forgotten. As such, we advise you to automate the cell clearing using [Git filter](https://git-scm.com/docs/gitattributes/#_filter) as follows. First, we tell Git to set an attribute on our notebook files by creating a `.gitattributes` file in the root of our repo:

```
*.ipynb filter=jupyternotebook
```

We can then define our cleaning instructions in a `.gitconfig` configuration file:

```
[filter "jupyternotebook"]
    clean = jupyter nbconvert --ClearOutputPreprocessor.enabled=True --inplace %f
    required
```

You can add this to your global `.gitconfig` or create a local `.gitconfig` file in the root of your repo. If you create a new config in your repo, you must run the following in that repo:

```
git config --local include.path ../.gitconfig
```

This configuration will now run the notebook output cleaning command every time a change to an `.ipynb` file is added to your local repo. You will need to then `git add` the file a second time to add the changes from the cleaning. This should prevent notebook outputs from being included in commits to this repository.

If you adjust the filter at any point, you should run this command to apply the changes to existing files:

```
git add --renormalize .
```

This approach has been borrowed from this [blog post by Yury Zhauniarovich](https://zhauniarovich.com/post/2020/2020-10-clearing-jupyter-output-p3/).


### Access control

Access control management of your projects should ultimately be guided by your department. Note that access control should not be used instead of the good practices outlined above. It can help to limit the risk of releasing sensitive information, but we should primarily aim to use the version control tools correctly.

Remote version control platforms, like GitHub, have a [visibility setting](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/setting-repository-visibility) that represents whether the repo can be viewed publicly or only by owners of the project. We find that some analytical teams choose to develop code in the open, as per guidance from the [Government Service Manual](https://www.gov.uk/service-manual/service-standard/point-12-make-new-source-code-open). Others work in a private repositories, and then might either change to public or migrate to a fresh public repository to make the code open.

Organizations provide an area for collating multiple repos that are associated with a particular team or department. Within Organizations, Teams can be also created to manage view and contribution permissions for projects within the Organization. External collaborators can also be added to projects, to allow direct contribution from those outside of the Organization.

Despite the visibility status of a repo, only Organization members and collaborators may make direct contributions to the code in the repo. Others can contribute by Pull Request.

Detailed setup and management of Organizations and Teams are described in the [relevant section of the GitHub Docs](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams).


### Handling data breaches via Git

If unauthorised individuals may access sensitive information through their accidental inclusion in a remote repository, this is a data breach. For example, pushing credentials or sensitive data to a remote public GitHub repository.

To handle a data breach, you should:

1. Prevent further access to the information.
   * Restrict access of the repository to only those who should have access to the information. For example, make a public repository private.
   * Remove the offending files from the **commit history** of the repository. Note that commiting the deletion of the information alone will not suffice.
   * If credentials (passwords or keys) have been revealed, you should change these credentials.
   * Ask anyone that you know has access to the repository to delete their local copy and re-clone the cleaned remote repository.
2. Report the incident.
   * Record roughly how long the information was available for and the potential impact of releasing this information.
   * Inform the data owner of the breach.
   * Discuss the breach with the Data Protection Officer in your department. They should be able to advise you on the steps you should take as well, according to your departments data security policy.
   * If the breach includes pre-publication statistics, you must also report the breach to the [Office for Statistics Regulation](https://osr.statisticsauthority.gov.uk/).

The [Pro Git book section on rewriting history](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History) details methods for editing and deleting files from your repository's commit history. The [BFG repo-cleaner tool](https://rtyley.github.io/bfg-repo-cleaner/) can be a simpler alternative to standard Git commands.


## GitHub

A number of version control platforms extend the functionality of Git, to further improve collaborative working.

Here we describe some of the beneficial features supported by [GitHub](https://github.com/), the world's leading software development platform. GitHub provides additional tools for better management of collaborative work. Many of these tools are also discussed in detail on the [GitHub features page](https://github.com/features/project-management/), but we will describe how they may be applied in analytical workflows here.

### Efficient use of issues

Issues offer a method for requesting or recording tasks, including enhancements and bug fixes in a project. They act as a collaborative todo list, which users and developers can easily contribute to.

The basic elements of an issue are the:
* Title and description, provided by the person that submitted the issue
* Labels that categorise the issue (e.g. enhancement or bug)
* Comments section where the issue can be discussed
* Assigned developers that are working on resolving the issue

Within an issue's description and comments, you can reference other issues both within (e.g. `#12`) and between repos, and tag individuals to notify them of your comments (`@whatstheirface`). Similarly, issues can be linked to specific [changes that will be merged to resolve or help to solve the issue](pull-requests). This makes them useful for discussing bugs and new features within a team.

When a GitHub repo is publicly visible, the issues are also open and can be contributed to by others, including users. Open source projects benefit from this transparency by providing users with a platform to highlight which changes to the project will be most beneficial for them. In turn, developers in the community can then address these issues to improve the project.

Analytical projects might use issues to plan and discuss the steps involved in developing the project. Where additional help is required, collaborators might be tagged or assigned to the task. If your analysis code is widely useful, others that use your code may also suggest improvement and offer to contribute to the project via these issues.

[Setting issue templates](https://docs.github.com/en/free-pro-team@latest/github/building-a-strong-community/configuring-issue-templates-for-your-repository) for your project can be an effective way of encouraging users and collaborators to use informative descriptions when creating issues. For example, a bug issue should include simple instructions to help maintainers reproduce the problem. While feature requests might include information on how the user expects the new feature to work and details what problem it will help them to overcome.



(pull-requests)=
### Making the most of Pull Requests

Issues provide a useful forum for proposing and discussing changes to a project. Once these changes have been implemented, Pull Requests (PRs) provide a useful interface for including those changes in the main project. They are typically used to merge a development branch onto a more stable branch in the main project. The development branch here may be within the same project, or from a separate project or [Fork](forking).

Much like issues, PRs can be linked to other issues and PRs and contributors can be assigned or tagged in discussions.

Pull requests support good branching. They provide an opportunity for review, before code is merged onto a more stable branch. This further reduces the likelihood of merging breaking changes onto our higher level branches.

A pull request lets you describe changes that you have made to a repo. The request is based on the difference between a target branch (usually `dev` or `master`) and a source branch, where you have implemented changes. The source branch can the in the same repository, or might be in a Fork (below) of the repository if you are not a member of the project.

Pull requests create an interface for discussion and review of your changes. Once a project maintainer is happy with your changes, they can merge them onto the target branch. After merging, pull requests preserve a record of the changes and associated discussion.

You can label pull requests a draft to indicate they are still a work in progress. This prevents them from being merged prematurely. This can be useful when you would like to request advice or early feedback on the changes you are making.


(forking)=
### Forking

Forking a repository takes a complete copy of a project's current state, including its history and all existing branches and tags. Any changes made to a fork do not affect the code on the original repo, and *vice versa*.

You might fork a repository when you want to:
* Contribute to a project as an external collaborator
* Make changes to a project for your own use, or to maintain a copy that is independent to the original

In the first case, lets consider that you've found

Note that forks do not automatically synchronise with the original repo. This means that changes to the original repo, after you create a fork, need to be manually synchronised if you want to include them in your repo. When you would like to offer to contribute your changes to the original project (see [Pull Requests](pull-requests)), you should ensure that you synchronise your branch with any new changes first.

See the GitHub Docs for [instructions on forking a repo and keeping your fork up to date](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/fork-a-repo) with a project and also [working with forks](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/working-with-forks).

### Other GitHub features


[Project boards](https://docs.github.com/en/free-pro-team@latest/github/managing-your-work-on-github/about-project-boards) offer project management features through a [Kanban board](https://en.wikipedia.org/wiki/Kanban_board) interface.

These boards can be used to track assignment and progress of specific tasks. This is aided by linking tasks to specific issues and pull requests.

Wiki
GitHub Pages for public documentation (link to documentation)

(continuous-integration)=
## Continuous integration

Continuous integration (CI) describes the practice of frequently committing changes to your code. This subsection relates to CI tools, which primarily help to automate routine quality assurance tasks. These include verifying that your code successfully builds or installs and that your code tests run successfully. As the execution environment is defined in the CI workflow configuration, running of tests in this way should be reproducible.

Automation of routine tasks in this way reduces the effort required to merge changes onto the existing code base. This encourages frequent merging of small changes. As such, conflicts between multiple contributions should be minimal and that review of these changes is simpler.

CI is often linked to:
* Continuous delivery - ensuring that your code is fit for use after each integration
* Continuous deployment - automatically deploying working code into production

GitHub provides a CI service via [GitHub Actions](https://github.com/features/actions). However, many other CI tools can be integrated with version control platforms, including GitHub. Other commonly used tools/services include:
* Jenkins
* Travis
* CircleCI
* AppVeyor


### Testing example

Below is an example configuration file, for use with GitHub actions. The `YAML` file, which is used here, is common for CI tools. Other CI tools may use different file types, but these likely have a similar overall structure.

```
name: Test python package

on:
  push:
    branches:
      - master
  pull_request:


jobs:
  build:

    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.6, 3.7, 3.8, 3.9]

    steps:
    - uses: actions/checkout@v2

    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install pytest 
        pip install -r requirements.txt
        
    - name: Test with pytest
      run: |
        pytest
```

The first section of this example describes when our workflow should be run. In this case, we're running the CI workflow whenever code is `push`ed to the `master` branch or where any Pull Request is created. In the case of Pull Requests, the results of the CI workflow will be report on the request's page. If any of the workflow stages fail, this can block the merge of these changes onto a more stable branch. Subsequent commits to the source branch will trigger the CI workflow to run again.

Below `jobs`, we're defining what tasks we would like to run when our workflow is triggered. We define what operating system we would like to run our workflow on - the Linux operating system `ubuntu` here. The `matrix` section under `strategy` defines parameters for the workflow. The workflow will be repeated for each combination of parameters supplied here - in this case the 4 latest Python versions.

The individual stages of the workflow are defined under `steps`. `steps` typically have an informative name and run code to perform an action. Here `uses: actions/checkout@v2` references [existing code](https://github.com/actions/checkout) that will retrieve the code from our repo. The subsequent `steps` will use this code. The next step provides us with the a working Python version, as specified in the `matrix`. Then we install dependencies/requirements for our code and the `pytest` module. Finally, we run `pytest` to check that our code is working as expected.

This workflow will report whether our test code ran successfully for each of the specified Python versions.


### Documentation example

This book uses the following GitHub Actions configuration to build and deploy the HTML content:

```
name: Build and deploy book

on:
  push:
    branches:
      - main
      - master
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2

    - name: Set up Python 3.7
      uses: actions/setup-python@v1
      with:
        python-version: 3.7

    - name: Install dependencies
      run: |
        pip install -r requirements.txt

    - name: Build the book
      run: |
        jb build book -W -v --keep-going && touch ./book/_build/html/.nojekyll

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: startsWith( github.ref, 'refs/tags/v')
    steps:
    - name: "Deploy book to GitHub Pages"
      uses: peaceiris/actions-gh-pages@v3.6.1
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./book/_build/html
```

This workflow runs whenever a pull request is create or changes are pushed directly to the main branch. It has two jobs - one that builds the book's HTML content and another that deploys the content to this website.

As with the previous example, we start the workflow by setting up an environment with Python. We install the dependencies for the project, which includes `jupyter-book` to build to the book.

Our workflow then builds the book's HTML content, where the workflow will fail if warnings or errors are raised.

In the second job, the book (including the new changes) is deployed to the site that you are reading now. This job needs the build job to have completed successfully before it will run. It will only run if a new Git tag has been created, to indicate a new version of the book. This allows us to accumulate changes on the main branch, before releasing a collection of changes in the next version. This deployment step requires authentication, which is managed by a secret/token that is accessed from the Action's environment.

You might use a similar approach to this to deploy your code's HTML documentation.


### Complex example

You can see a detailed example of CI in practice in the `jupyter-book` project.
A recent version of the [`jupyter-book` CI workflow](https://github.com/executablebooks/jupyter-book/blob/6fb0cbe4abb5bc29e9081afbe24f71d864b40475/.github/workflows/tests.yml) includes:
* Checking code against style guidelines, using [pre-commit](https://pre-commit.com/)
* Running code tests over
  * a range of Python versions
  * multiple versions of specific dependencies (`sphinx` here)
  * multiple operating systems
* Reporting test coverage
* Checking that documentation builds successfully
* Deploying a new version of the `jupyter-book` package to [the python package index (PyPI)](https://pypi.org/)
