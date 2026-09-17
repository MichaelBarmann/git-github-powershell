# Overleaf-to-GitHub Workflow

## Repository structure

- `bone-remodeling-notes` contains the `.tex`, `.bib`, and manuscript-figure files synchronized with Overleaf.
- `bone-remodeling` contains the code and simulation outputs, plus a `notes` submodule that points to a specific commit in `bone-remodeling-notes`.

## 1. Edit in Overleaf

Edit the `.tex` and `.bib` files normally. Overleaf saves these changes automatically within the Overleaf project.

## 2. Push the notes from Overleaf to GitHub

When you reach a useful checkpoint:

1. Open **Integrations** in Overleaf.
2. Select **Sync with a GitHub repository**.
3. Click **Push Overleaf changes to GitHub**.
4. Enter a descriptive commit message.
5. Complete the push.

At this point, `bone-remodeling-notes` contains the latest version of the notes.

Because the notes are being edited exclusively in Overleaf, there is normally no need to select **Pull GitHub changes into Overleaf**.

## 3. Update the `notes` submodule in `bone-remodeling`

Open PowerShell in the root of the local `bone-remodeling` repository and run:

```powershell
git -C .\notes pull origin main
git add notes
git commit -m "Update notes submodule"
git push origin main
```

This updates the `notes` link in `bone-remodeling` so that it points to the newest commit in `bone-remodeling-notes`.

## How often to update

The Overleaf project can be pushed to `bone-remodeling-notes` as often as desired. The `notes` submodule in `bone-remodeling` does not need to be updated after every small edit. It can instead be updated at meaningful checkpoints, such as the end of a work session.

Until the submodule pointer is updated:

- `bone-remodeling-notes` still contains the latest notes;
- `bone-remodeling/notes` points to the most recently recorded notes checkpoint.

## Cloning the project onto another computer

To clone `bone-remodeling` together with the `notes` submodule, run:

```powershell
git clone --recurse-submodules https://github.com/MichaelBarmann/bone-remodeling.git
```
