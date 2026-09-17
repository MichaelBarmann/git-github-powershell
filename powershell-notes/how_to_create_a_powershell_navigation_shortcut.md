# Create a PowerShell Navigation Shortcut

## 1. Check whether the PowerShell profile exists

```powershell
Test-Path $PROFILE
```

## 2. Open the PowerShell profile

```powershell
notepad $PROFILE
```

## 3. Add the navigation function

Add the following code to the profile:

```powershell
function github_repos {
    cd "C:\Users\mbarm\Documents\Documents\01_GradSchool\03_code\github_repos"
}
```

Save and close the profile.

## 4. Reload the PowerShell profile

```powershell
. $PROFILE
```

There must be a space between the period and `$PROFILE`.

## 5. Use the shortcut

```powershell
github_repos
```

This navigates directly to:

```text
C:\Users\mbarm\Documents\Documents\01_GradSchool\03_code\github_repos
```

The shortcut will be available automatically in future PowerShell sessions.