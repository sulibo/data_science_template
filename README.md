Data Science project template (Work in Progress)
==============================

A GitHub/Git repository template for data science projects for my personal use.

Features:
- A logical, reasonably standardardize, but flexible structure for data science projects
- A Makefile to facilitate initial setup, python virtual environment creation, code execution, etc.
- Automatic code linting and reformatting

Built on [Cookiecutter Data Science](https://github.com/drivendata/cookiecutter-data-science)

Usage
------------

### Create the GitHub repository
* On GitHub, navigate to the main page of [the template repository](https://github.com/sulibo/data_science_template)
* Above the file list, click **Use this template**.
![Use this template button](https://i.ibb.co/JpyP904/Use-this-template-button.png "use_this_template_button")
* Type a name for your repository, and an optional description.
![type repo name](https://i.ibb.co/wycwGPs/type-repo-name.png "type repo name")
* Choose repository visibility as **Private** or **Public**.
![private or public repo](https://i.ibb.co/HPgk9ZQ/private-public.png "private or public repo")
* Select **Include all branches** to include the directory structure and files from all branches in the template, and not just the default branch.
![include all branches](https://i.ibb.co/NSWDxbp/include-all-branches.png "include all branches")
* Click **Create repository from template**.

### Initial setup

* Clone the repository to local machine and navigate to the repository

    ```sh
    git clone git@github.com:sulibo/<repository_name>.git
    cd <repository_name>
    ```

* Change the project name in the `Makefile` file to your project name.

    Replace `data_science_template` in the line `PROJECT_NAME = data_science_template` in `Makefile` with your project name.

* Commit and push your changes
    ```sh
    git add Makefile
    git commit -m "initial setup"
    git push origin main
    ```
* Set up the pre-commit hooks for code style check and create the `data` folder.
    ```sh
    make init
    ```
    The pre-commit hooks using flake8 and black will be created to enforce PEP8 code style before every commit.

    The `data` folder to store the data will be created. But, it is not tracked by Git as the data usually contains large files which are not suitable to be version controlled by Git/GitHub. It is recommended to use [DVC](https://dvc.org) - an open source version control system for machine learning projects to manage the data, model and also the experiments.

    The structure of the constructed repository is shown in the Project Organization section.

    **Tip**: The Makefile alos provides `make create_environment` and `make requirements` to facilitate virtual environment creatation and requirement mangement. Type `make help` for more options.

Git workflow
------------
This workflow is proposed for feature development or bug fixes. It is constructed based on [Github flow](https://guides.github.com/introduction/flow/) with [Interactive Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing).

The main concepts of GitHub flow are:

* Anything in the `main` branch is deployable.
* To work on something new or fix a bug, create a descriptively named branch off of `main`.
* Commit to that branch locally and regularly push your work to the same named branch on the server.
* When you need feedback or help, or you think the branch is ready for merging, open a [pull request](http://help.github.com/send-pull-requests/).
* After some else has reviewed and signed off on the feature, you can merge it to `main`.
* Once it is merged and pushed to `main`, you can and _should_ deploy immedidately.

See more [here](http://scottchacon.com/2011/08/31/github-flow.html).

The main steps are as follows:

* Clone the repository from GitHub. **For subsequent features/changes this step should be ignored.**

    ```sh
    git clone git@github.com:IXP-Team/<repository_name>.git
    cd <project directory>
    ```

* Create the python virtual environment. **For subsequent features/changes this step should be ignored.**

    ```sh
    make create_environment
    ```
    A python virtual environment named after your project name will be created. If you have conda installed, the environment is created by Conda, otherwise virtualenv and virtualenvwrapper will be installed to create the environment.

* Activate the virtual environment
    ```sh
    conda activate <project_name>
    ```
    or
    ```sh
    workon <project_name>
    ```

* Install the required Python modules. **For subsequent features/changes this step should be ignored, except when the requirements.txt is changed.**
    ```sh
    make requirements
    ```

* Checkout a new feature/bug-fix branch.
    ```sh
    git checkout -b <branchname> main
    ```

* Make changes and add the files.
    ```sh
    git add <file1> <file2> ...
    git commit
    ```
    _Why:_
    > `git add <file1> <file2> ... ` - you should add only files that make up a small and coherent change.

    > `git commit` will start an editor which lets you separate the subject from the body.

    _Tip:_
    > You could use `git add -p` instead, which will give you chance to review all of the introduced changes one by one, and decide whether to include them in the commit or not.

* Sync with remote to get changes you’ve missed.
    ```sh
    git checkout main
    git pull
    ```

    _Why:_
    > This will give you a chance to deal with conflicts on your machine while rebasing (later) rather than creating a Pull Request that contains conflicts.

* Update your feature branch with latest changes from develop by interactive rebase.
    ```sh
    git checkout <branchname>
    git rebase -i --autosquash main
    ```

    _Why:_
    > You can use --autosquash to squash all your commits to a single commit. Nobody wants many commits for a single feature in develop branch. [read more...](https://robots.thoughtbot.com/autosquashing-git-commits)

* If you don’t have conflicts, skip this step. If you have conflicts, [resolve them](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)  and continue rebase.
    ```sh
    git add <file1> <file2> ...
    git rebase --continue
    ```
* Push your branch. Rebase will change history, so you'll have to use `-f` to force changes into the remote branch. If someone else is working on your branch, use the less destructive `--force-with-lease`.
    ```sh
    git push -f -u origin <branchname>
    ```

    _Why:_
    > When you do a rebase, you are changing the history on your feature branch. As a result, Git will reject normal `git push`. Instead, you'll need to use the -f or --force flag. [read more...](https://developer.atlassian.com/blog/2015/04/force-with-lease/)

    _Note:_
    > Before pushing, make sure your feature branch builds successfully and passes all tests (including code style checks).

    > The `-u` option is only needed for the first push.

* Make a Pull Request.
* Pull request will be accepted, merged and closed by a reviewer.
* Remove your local feature branch if you're done.

  ```sh
  git branch -D <branchname>
  ```
  to remove all branches which are no longer on remote
  ```sh
  git fetch -p && for branch in `git branch -vv --no-color | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```
 * Deactivate virtual environment if all work is done.

   Conda:

   ```sh
   conda deactivate
   ```

   or

   Virtualenvwrapper:
   ```sh
   deactivate
   ```

Project Organization
------------

    ├── LICENSE
    ├── Makefile           <- Makefile with commands like `make data` or `make train`
    ├── README.md          <- The top-level README for developers using this project.
    ├── data
    │   ├── external       <- Data from third party sources.
    │   ├── interim        <- Intermediate data that has been transformed.
    │   ├── processed      <- The final, canonical data sets for modeling.
    │   └── raw            <- The original, immutable data dump.
    │
    ├── docs               <- A default Sphinx project; see sphinx-doc.org for details
    │
    ├── models             <- Trained and serialized models, model predictions, or model summaries
    │
    ├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
    │                         the creator's initials, and a short `-` delimited description, e.g.
    │                         `1.0-jqp-initial-data-exploration`.
    │
    ├── references         <- Data dictionaries, manuals, and all other explanatory materials.
    │
    ├── reports            <- Generated analysis as HTML, PDF, LaTeX, etc.
    │   └── figures        <- Generated graphics and figures to be used in reporting
    │
    ├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
    │                         generated with `pip freeze > requirements.txt`
    │
    ├── setup.py           <- makes project pip installable (pip install -e .) so src can be imported
    ├── src                <- Source code for use in this project.
    │   ├── __init__.py    <- Makes src a Python module
    │   │
    │   ├── data           <- Scripts to download or generate data
    │   │   └── make_dataset.py
    │   │
    │   ├── features       <- Scripts to turn raw data into features for modeling
    │   │   └── build_features.py
    │   │
    │   ├── models         <- Scripts to train models and then use trained models to make
    │   │   │                 predictions
    │   │   ├── predict_model.py
    │   │   └── train_model.py
    │   │
    │   └── visualization  <- Scripts to create exploratory and results oriented visualizations
    │       └── visualize.py
    │
    ├── .pre-commit-config.yaml
    │                      <- Pre-commit configuration file
    │
    └── tox.ini            <- tox file with settings for running tox; see tox.readthedocs.io


--------

<p><small>Project based on the <a target="_blank" href="https://drivendata.github.io/cookiecutter-data-science/">cookiecutter data science project template</a>. #cookiecutterdatascience</small></p>
