# Set Up an Existing Overleaf Project as a Git Submodule

This guide explains how to:

1. Connect an existing Overleaf project to a new GitHub repository dedicated to notes.
2. Create a separate GitHub repository for the complete research project.
3. Add the notes repository to the research repository as a Git submodule named `notes`.
4. Verify the complete Overleaf → notes repository → research repository workflow.

The finished structure will be:

```text
research-project/                       Main research repository
├── notes/                              Git submodule
│   ├── main.tex
│   ├── references.bib
│   └── figures/
├── code and notebooks
├── README.md
└── .gitmodules
```

The two GitHub repositories have different responsibilities:

| Repository | Purpose |
| --- | --- |
| `project-notes` | Contains only the files used by Overleaf: `.tex`, `.bib`, and associated figures. |
| `research-project` | Contains the complete research project, including code, notebooks, documentation, and a pointer to the notes repository. |

## Names Used in This Guide

This guide uses the following concrete example names:

```text
Overleaf project: Project Notes
Notes repository: project-notes
Research repository: research-project
GitHub username: MichaelBarmann
```

When applying the guide to a real project, substitute its actual names. For example, `project-notes` could be `polarization-models-notes`, while `research-project` could be `polarization-models`.

## Part 1: Prepare the Existing Overleaf Project

First, confirm that the Overleaf project contains only the material that belongs in the notes repository:

- `.tex` files
- bibliography files such as `references.bib`
- figures used by the LaTeX document
- any LaTeX style, class, or configuration files required to compile it

Do not place research code, notebooks, simulation videos, or unrelated generated output in this Overleaf project.

### If the Overleaf project is not linked to GitHub

Use the existing project directly and continue to Part 2.

### If the Overleaf project is already linked to the wrong GitHub repository

An Overleaf project's GitHub connection generally cannot simply be reassigned to a different repository. Make an independent copy instead:

1. From the Overleaf project dashboard, open the project's menu.
2. Select **Copy Project**.
3. Give the copy a temporary name such as:

   ```text
   Project Notes - New Sync
   ```

4. Open the copy and confirm that it contains all required `.tex`, `.bib`, style, and figure files.

The copied project has its own integration state and can therefore be connected to the new notes repository.

## Part 2: Create the Notes Repository Through Overleaf

In the Overleaf project that you intend to keep:

1. Open **Integrations**.
2. Select **GitHub** or **Sync with GitHub**.
3. Choose the option to create a new GitHub repository.
4. Name the repository:

   ```text
   project-notes
   ```

5. Choose the desired visibility, such as **Private**.
6. Complete the initial push from Overleaf.

On GitHub, open `project-notes` and verify that it contains the expected LaTeX source, bibliography, and figures.

If you created an Overleaf copy because the original project had the wrong connection:

1. Rename the original project:

   ```text
   Project Notes - OLD - DO NOT SYNC
   ```

2. Rename the correctly connected copy:

   ```text
   Project Notes
   ```

Do not push from the old project. After the entire workflow has been tested, the old project can be moved to Overleaf's trash.

## Part 3: Create and Clone the Main Research Repository

On GitHub:

1. Select **New repository**.
2. Name it:

   ```text
   research-project
   ```

3. Choose the desired visibility.
4. Optionally initialize it with a `README.md`.
5. Create the repository.

In PowerShell, navigate to the directory in which you keep GitHub repositories and clone it:

```powershell
git clone https://github.com/MichaelBarmann/research-project.git
cd .\research-project\
```

Confirm that you are in the correct repository and inspect its current state:

```powershell
git status
git remote -v
```

If the research repository already exists locally, do not clone it again. Navigate into the existing local repository and confirm that its working tree is in a safe state with `git status`.

## Part 4: Add the Notes Repository as a Submodule

From the root of the local `research-project` repository, first confirm that a file or directory named `notes` does not already exist:

```powershell
Test-Path .\notes
```

The expected result is:

```text
False
```

Add the notes repository as a submodule named `notes`:

```powershell
git submodule add https://github.com/MichaelBarmann/project-notes.git notes
```

Git will:

- clone `project-notes` into the local `notes` directory;
- create a `.gitmodules` file; and
- stage both `.gitmodules` and the submodule reference.

Check the result:

```powershell
git status
Get-Content .gitmodules
```

The `.gitmodules` file should contain:

```ini
[submodule "notes"]
        path = notes
        url = https://github.com/MichaelBarmann/project-notes.git
```

Commit and push the new submodule:

```powershell
git commit -m "Add notes as Git submodule"
git push origin main
```

On GitHub, the main `research-project` repository should now show an entry similar to:

```text
notes @ abc1234
```

Clicking that entry should open the corresponding commit in `project-notes`.

## Part 5: Test the Complete Workflow

### 1. Make a harmless test edit in Overleaf

Add a comment to the main `.tex` file:

```latex
% GitHub submodule synchronization test
```

### 2. Push from Overleaf to the notes repository

In Overleaf:

1. Open **Integrations**.
2. Select **GitHub** or **Sync with GitHub**.
3. Select **Push Overleaf changes to GitHub**.
4. Use a commit message such as:

   ```text
   Test new Overleaf sync
   ```

On GitHub, confirm that the commit appears in `project-notes`.

### 3. Update the local submodule

From the root of the local `research-project` repository, run:

```powershell
git -C .\notes pull origin main
```

Then inspect the parent repository:

```powershell
git status
```

It should report:

```text
modified:   notes (new commits)
```

This does not mean that all files inside `notes` are being copied into the parent repository. It means that the parent repository's recorded submodule commit needs to be updated.

### 4. Record the updated submodule commit

Stage only the submodule pointer:

```powershell
git add notes
git status
```

Commit it:

```powershell
git commit -m "Update notes submodule"
```

Before pushing, incorporate any newer changes from the remote parent repository:

```powershell
git pull --rebase origin main
```

Then push:

```powershell
git push origin main
```

Finally, refresh `research-project` on GitHub. Its submodule entry should display the newer short commit hash. Click the entry and verify that the test comment appears in the main `.tex` file.

## If `git push` Is Rejected with `fetch first`

A rejection such as this means that the remote parent repository has a commit that is not present locally:

```text
! [rejected] main -> main (fetch first)
```

Do not immediately force-push. First inspect the situation:

```powershell
git fetch origin
git log --oneline --decorate --graph --all -n 10
```

If the remote and local commits are ordinary compatible changes—for example, the remote changed `README.md` while the local commit updated the `notes` pointer—rebase and push:

```powershell
git rebase origin/main
git push origin main
```

Use a force-push only when you have intentionally decided to replace the remote history and understand what will be discarded. Prefer the safer form:

```powershell
git push --force-with-lease origin main
```

## Ongoing Overleaf-to-GitHub Workflow

After editing in Overleaf:

1. In Overleaf, push the changes to `project-notes` through **Integrations → GitHub**.
2. From the local parent repository, run:

   ```powershell
   git -C .\notes pull origin main
   git add notes
   git commit -m "Update notes submodule"
   git pull --rebase origin main
   git push origin main
   ```

The Overleaf push updates the notes repository itself. The PowerShell commands then update the main research repository so that its `notes` submodule points to the latest notes commit.

If you edit the LaTeX and bibliography files exclusively in Overleaf, there is normally no reason to pull GitHub changes into Overleaf. Avoid editing the same notes files independently in multiple places unless you deliberately manage the resulting two-way synchronization.

## Cloning the Research Repository on Another Computer

To clone the parent repository and initialize its submodule in one command:

```powershell
git clone --recurse-submodules https://github.com/MichaelBarmann/research-project.git
```

If the parent repository was already cloned without its submodule contents, run this from its root:

```powershell
git submodule update --init --recursive
```

To update the notes submodule later:

```powershell
git -C .\notes pull origin main
```

## Important Safety Rules

- Use only the Overleaf project connected to `project-notes`.
- Do not push from an old Overleaf project that remains connected directly to `research-project`.
- Run parent-repository Git commands from the root of `research-project`.
- Use `git -C .\notes ...` when a command is intended for the notes repository.
- Use `git add notes` to stage only the submodule pointer. Avoid `git add .` when unrelated or untracked files are present.
- A submodule stores a commit reference. Updating `project-notes` does not automatically update the pointer recorded by `research-project`.
- Do not force-push merely to solve an ordinary non-fast-forward rejection. Fetch and inspect the histories first.

## Final Checklist

- [ ] The active Overleaf project is linked to `project-notes`.
- [ ] `project-notes` contains only the LaTeX project and its supporting files.
- [ ] `research-project` exists as the main research repository.
- [ ] `research-project/.gitmodules` identifies `notes` and the correct notes-repository URL.
- [ ] GitHub displays an entry such as `notes @ abc1234` in the main repository.
- [ ] Clicking the submodule entry opens the correct notes repository and commit.
- [ ] A test edit successfully traveled from Overleaf to `project-notes`.
- [ ] The parent repository was updated to point to that new notes commit.
- [ ] Any obsolete Overleaf project is clearly marked as old or has been moved to the trash.
