---
date: '2026-04-08T09:32:27+05:00'
draft: false
title: 'Intro to Git and Github'
featureimage: "git-feature.png"

heroStyle: "background"

showTableOfContents: true
---
# Git and GitHub: History, Introduction, and Essential Commands

Git and GitHub are essential tools in modern software development. They enable developers to track code changes, collaborate efficiently, and manage software projects of any size.

This guide introduces version control, explores the history of Git and GitHub, and covers the essential Git commands every developer should know.

---

## What is Version Control?

Version control is a system that records changes to files over time. It allows developers to:

- Track modifications in code
- Revert to previous versions
- Collaborate with other developers
- Manage multiple versions of a project

Instead of manually saving files as `project_v1`, `project_v2`, and so on, version control systems automatically maintain a complete history of changes.

---

## Brief History of Git

Git was created in **2005 by Linus Torvalds**, the creator of the Linux kernel.

Before Git, the Linux project relied on a proprietary version control system called **BitKeeper**. When access to BitKeeper was revoked, Linus Torvalds developed Git within a few weeks to meet the needs of Linux kernel development.

Git was designed with four primary goals:

- Speed
- Distributed development
- Data integrity
- Efficient branching and merging

Today, Git is the **most widely used version control system** in the software industry.

---

## What is GitHub?

**GitHub** is a cloud-based platform for hosting Git repositories and collaborating on software projects.

Founded in **2008**, GitHub rapidly became the leading platform for open-source software development. In **2018**, it was acquired by Microsoft for **$7.5 billion**.

GitHub offers features such as:

- Repository hosting
- Pull requests
- Issue tracking
- Continuous Integration and Continuous Deployment (CI/CD)
- GitHub Actions
- Project management tools

GitHub does not replace Git—it extends Git by providing collaboration, hosting, and automation capabilities.

---

## Git vs GitHub

| Feature | Git | GitHub |
|----------|-----|---------|
| Type | Version Control System | Repository Hosting Platform |
| Runs on | Local machine | Cloud platform |
| Purpose | Track code changes | Host and collaborate on repositories |
| Developed by | Linus Torvalds | GitHub Inc. |

In simple terms:

- **Git** → A version control tool
- **GitHub** → A platform built around Git

---

## Installing Git

### Linux
Open your Terminal and write the following: 
```bash
sudo apt install git
```
Once Installation is done verify it by:

```bash
git --version
```

Example output:

![output](/version.png)

---

## Configuring Git

Before using Git for the first time , configure your username and email.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

And then view your configuration by writing the following in the terminal:
![output](/config.png)


---

## Creating a Git Repository

After Configuring your GIt initialize a repository inside your project directory:

```bash
git init
```

This creates a hidden `.git` directory that stores your project's version history.

---

## Understanding the Git Workflow

Before going ahead you must understand the basic Git workflow. It consists of three stages:

```
Working Directory
        │
        ▼
   Staging Area
        │
        ▼
    Git Repository
```
---

## Some Essential Git Commands

### Check Repository Status

```bash
git status
```

Displays modified, staged, and untracked files.

---

### Add Files to the Staging Area

Stage a single file:

```bash
git add filename
```

Stage every modified file:

```bash
git add .
```

---

### Commit Changes

```bash
git commit -m "Initial commit"
```

Creates a snapshot of the staged changes.

---

### View Commit History

```bash
git log
```

Displays the repository's commit history.

---

### Clone a Repository

Download an existing repository:

```bash
git clone https://github.com/username/repository.git
```

---

### Connect a Local Repository to GitHub

Add a remote repository:

```bash
git remote add origin https://github.com/username/repository.git
```

---

### Push Changes to GitHub

```bash
git push -u origin main
```

Uploads local commits to GitHub.

---

### Pull Latest Changes

```bash
git pull
```

Downloads and merges updates from the remote repository.

---

### Create and Switch Branches

Create a new branch:

```bash
git branch feature-branch
```

Switch to the branch:

```bash
git checkout feature-branch
```

Or create and switch in a single command:

```bash
git checkout -b feature-branch
```

---

### Merge Branches

```bash
git merge feature-branch
```

Merges the specified branch into the current branch.

---

## Why Developers Use Git and GitHub

Git and GitHub have become industry standards because they provide:

- Reliable version tracking
- Efficient collaboration
- Safe branching and merging
- Remote backup of source code
- Integration with CI/CD pipelines
- Automation through GitHub Actions

---

## Conclusion

Git and GitHub are fundamental tools for software development. Whether you're building personal projects, contributing to open-source software, or working in a professional development team, understanding Git's workflow and commands is an essential skill.

By mastering these fundamentals, you'll be able to collaborate more effectively, maintain a clean project history, and build software with confidence.