# Add an Automated PDF Release to an Overleaf-Synced Repository

1. Enter and update the notes repository:

   ```powershell
   cd path\to\project-notes
   git pull origin main
   git status
   ```

2. Identify the main LaTeX file—the file containing `\documentclass`.

3. Create the GitHub Actions workflow:

   ```powershell
   New-Item -ItemType Directory -Force .github\workflows
   notepad .github\workflows\main.yml
   ```

4. Copy the workflow from:

   ```text
   collective-migration-notes/.github/workflows/main.yml
   ```

5. In the copied workflow, replace:

   - `_main.tex` with the new main `.tex` filename.
   - Both occurrences of `_main.pdf` with the generated PDF filename.
   - The release-description text with the new project name.

6. Commit and push the workflow:

   ```powershell
   git add .github/workflows/main.yml
   git commit -m "Add daily PDF release workflow"
   git push origin main
   ```

7. On GitHub, open **Actions → Build and release PDF** and wait for a green checkmark.

8. Open the repository’s **Releases** section and verify:

   - The title is `Daily Build - <current date>`.
   - The compiled PDF appears under **Assets**.
   - The release tag is `latest-pdf`.

9. If the notes repository is a submodule, update the parent repository:

   ```powershell
   cd path\to\research-project
   git -C .\notes pull origin main
   git add notes
   git commit -m "Update notes submodule for PDF workflow"
   git push
   ```

Each subsequent push to the notes repository’s `main` branch will rebuild the PDF and replace the asset in the existing `latest-pdf` release.