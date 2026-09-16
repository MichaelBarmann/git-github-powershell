# Creating a Local Git Repository and Linking It to GitHub

This note summarizes the basic workflow for creating a local Git repository and connecting it to a new GitHub repository.

## Overview

The basic sequence is:

1. Create a local project folder.
2. Initialize Git locally.
3. Add an initial file.
4. Stage and commit the file.
5. Create an empty repository on GitHub.
6. Link the local repository to the GitHub repository.
7. Push the local repository to GitHub.
8. Confirm that everything is connected.

---

## 1. Create the local project folder

Navigate to the folder where you want the project to live:

```powershell
cd C:\path\to\where\you\want\the\project
```

Create the project folder:

```powershell
mkdir my_project
cd my_project
```

For example:

```powershell
cd C:\Users\mbarm\Documents\Documents\01_GradSchool\03_code\projects
mkdir collective_cell_migration
cd collective_cell_migration
```

---

## 2. Initialize Git locally

Inside the project folder, initialize Git:

```powershell
git init
```

Rename the default branch to `main`:

```powershell
git branch -M main
```

Check the repository status:

```powershell
git status
```

You should see something like:

```text
On branch main

No commits yet

nothing to commit
```

---

## 3. Add an initial file

A common first file is a `README.md` file:

```powershell
"# my_project" | Out-File README.md -Encoding utf8
```

For example:

```powershell
"# collective_cell_migration" | Out-File README.md -Encoding utf8
```

Check that the file exists:

```powershell
ls
```

Then check Git status:

```powershell
git status
```

You should see `README.md` listed as an untracked file.

---

## 4. Stage and commit the file

Stage the README file:

```powershell
git add README.md
```

Commit it:

```powershell
git commit -m "Initial commit"
```

Then check the status:

```powershell
git status
```

You should see:

```text
On branch main
nothing to commit, working tree clean
```

---

## 5. Create an empty repository on GitHub

On GitHub:

1. Click **New repository**.
2. Choose a repository name.
3. Choose **Private** or **Public** visibility.
4. Do **not** initialize the repository with a README, `.gitignore`, or license if you already created and committed files locally.

For GitHub repository names, hyphens are often nicer than underscores. For example, you might use:

```text
collective-cell-migration
```

while the local folder is named:

```text
collective_cell_migration
```

The local folder name and the GitHub repository name do not have to match.

---

## 6. Link the local repository to the GitHub repository

From inside your local project folder, add the GitHub repository as the remote named `origin`:

```powershell
git remote add origin https://github.com/YOUR-USERNAME/my-project.git
```

For example:

```powershell
git remote add origin https://github.com/MichaelBarmann/collective-cell-migration.git
```

Check that the remote was added correctly:

```powershell
git remote -v
```

You should see something like:

```text
origin  https://github.com/YOUR-USERNAME/my-project.git (fetch)
origin  https://github.com/YOUR-USERNAME/my-project.git (push)
```

---

## 7. Push the local repository to GitHub

Push the local `main` branch to GitHub:

```powershell
git push -u origin main
```

The `-u` option sets the upstream branch, so future pushes can usually be done with just:

```powershell
git push
```

---

## 8. Confirm everything is connected

After pushing, check the status:

```powershell
git status
```

You want to see:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Also confirm on GitHub that the repository contains your `README.md` file.

---

## Normal workflow after setup

Once the local repo is connected to GitHub, the usual workflow is:

```powershell
git status
```

Stage changes:

```powershell
git add .
```

Commit changes:

```powershell
git commit -m "Describe what changed"
```

Push changes to GitHub:

```powershell
git push
```

---

## Common issue: SSH permission error

If GitHub gives you a command like this:

```powershell
git remote add origin git@github.com:YOUR-USERNAME/my-project.git
```

but you see an error like:

```text
Permission denied (publickey).
fatal: Could not read from remote repository.
```

then your computer is trying to connect to GitHub using SSH, but your SSH key is not set up.

The simplest fix is to use the HTTPS remote instead.

Check your current remote:

```powershell
git remote -v
```

Replace the SSH remote with the HTTPS remote:

```powershell
git remote set-url origin https://github.com/YOUR-USERNAME/my-project.git
```

Then push again:

```powershell
git push -u origin main
```
