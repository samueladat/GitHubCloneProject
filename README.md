# GitHubCloneProject

## Objective
Practice creating branches, making changes, merging, and resolving merge conflicts using Git and GitHub.

## Steps Summary
- Create a feature branch and add a file.
- Make a different change on the main branch.
- Merge and resolve any conflicts.
- Push the result to GitHub.

Modification in main branch.
# ====== SET YOUR REPO URL ======
$REPO_URL = "https://github.com/samueladat/GitHubCloneProject.git"

# ====== CLONE & ENTER FOLDER ======
git clone "$REPO_URL"
if ($LASTEXITCODE -ne 0) { throw "Clone failed. Check the repo URL or your network." }
Set-Location -Path "GitHubCloneProject"

# ====== ENSURE WE'RE ON MAIN ======
git rev-parse --quiet --verify main *> $null
if ($LASTEXITCODE -eq 0) {
  git checkout main
} else {
  # If default branch is not main, normalize to main
  $current = (git symbolic-ref --short -q HEAD) 2>$null
  if ($current -and $current -ne "main") {
    git branch -M main
  } else {
    git checkout -b main
  }
}

# ====== CREATE README IF MISSING ======
if (-not (Test-Path README.md)) {
@'
# GitHubCloneProject

## Objective
Practice creating branches for feature development, merging branches, and resolving merge conflicts to simulate a common collaborative workflow on Git and GitHub.

## Skills Practiced
- Branching and navigating between branches
- Making changes in different branches and committing those changes
- Merging branches and resolving conflicts
'@ | Set-Content -Encoding UTF8 README.md
  git add README.md
  git commit -m "Add README with task objectives"
}

# ====== FEATURE BRANCH WORK ======
git checkout -b feature-greeting

@'
This is a new feature.
'@ | Set-Content -Encoding UTF8 feature.txt

# Make README.md different on feature branch (to force a conflict later)
@'
# GitHubCloneProject

README updated in feature-greeting (this line will conflict with main).

## Objective
Practice creating branches for feature development, merging branches, and resolving merge conflicts to simulate a common collaborative workflow on Git and GitHub.

## Skills Practiced
- Branching and navigating between branches
- Making changes in different branches and committing those changes
- Merging branches and resolving conflicts
'@ | Set-Content -Encoding UTF8 README.md

git add feature.txt README.md
git commit -m "Add feature.txt and update README on feature-greeting"

# ====== MAIN BRANCH WORK ======
git checkout main

@'
# GitHubCloneProject

README updated in main (this line will conflict with feature-greeting).

## Objective
Practice creating branches for feature development, merging branches, and resolving merge conflicts to simulate a common collaborative workflow on Git and GitHub.

## Skills Practiced
- Branching and navigating between branches
- Making changes in different branches and committing those changes
- Merging branches and resolving conflicts
'@ | Set-Content -Encoding UTF8 README.md

git add README.md
git commit -m "Update README on main"

# ====== MERGE & RESOLVE CONFLICT ======
git merge feature-greeting
if ($LASTEXITCODE -ne 0) {
  Write-Host "Merge conflict detected. Resolving by integrating both versions..."
@'
# GitHubCloneProject

# Merge-Resolved README

This README integrates changes from **main** and **feature-greeting**.

- From main: README updated in main (this line will conflict with feature-greeting).
- From feature-greeting: README updated in feature-greeting (this line will conflict with main).

## Objective
Practice creating branches for feature development, merging branches, and resolving merge conflicts to simulate a common collaborative workflow on Git and GitHub.

## Skills Practiced
- Branching and navigating between branches
- Making changes in different branches and committing those changes
- Merging branches and resolving conflicts

## Files
- README.md (merge-resolved)
- feature.txt (This is a new feature.)
'@ | Set-Content -Encoding UTF8 README.md

  git add README.md
  git commit -m "Resolve merge conflict between main and feature-greeting by integrating both changes"
}

# ====== PUSH TO GITHUB ======
git push -u origin main
git push -u origin feature-greeting

Write-Host "`nDone! ✅ Changes pushed to GitHub."
git --no-pager log --oneline --graph -10


