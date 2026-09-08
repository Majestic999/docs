---
hide:
  - toc
---
# Contributing to Our Documentation Project

Thank you for your interest in contributing to the STEMAIDE Manuals documentation project! Your contributions play a vital role in helping us maintain accurate, high-quality, and user-friendly resources that empower learners and educators alike. Whether you're fixing a typo, enhancing existing content, or adding new sections, your efforts are deeply valued and contribute to the growth of our community.

This guide provides a step-by-step walkthrough for contributing to the project. If this is your first time contributing to a GitHub project, no worries—we’ve got you covered with simple and beginner-friendly instructions!

---

## Table of Contents

1. [Fork the Repository](#1-fork-the-repository)
2. [Clone the Repository](#2-clone-the-repository)
3. [Create a New Branch](#3-create-a-new-branch)
4. [Make Your Changes](#4-make-your-changes)
5. [Update the Changelog](#5-update-the-changelog)
6. [Push Your Changes](#6-push-your-changes)
7. [Create a Pull Request](#7-create-a-pull-request)
8. [Wait for Review](#8-wait-for-review)

---

## 1. Fork the Repository

Forking creates your own copy of the project that you can work on.

1. Go to the main repository page.
2. Click on the **Fork** button (located at the top-right corner of the page).

---

## 2. Clone the Repository

Once you’ve forked the repository, you’ll need to clone it to your local machine.

1. Open your terminal (or Git Bash).
2. Run the following command (replace `your-username` with your GitHub username):
   ```bash
   git clone https://github.com/your-username/docs.stemaide.com.git
   ```
3. Navigate to the directory you just cloned:
   cd docs.stemaide.com

## 3. Create a New Branch

Creating a new branch helps keep your changes organized and separate from the main project.

1. Create a new branch with a descriptive name
   ```bash
   git checkout -b update-readme
   ```
   Replace 'update-readme' with a name that describes your changes (e.g., fix-typo, add-section)

## 4. Make Your Changes

Now it’s time to edit the documentation files! We use Markdown for all documentation files.

1. Open the project in your text editor (e.g., VSCode, Sublime Text).
2. Locate the file you want to edit (e.g., README.md or a file in the docs/ folder).
3. Make your changes or add new content in Markdown.

   Markdown Example
   Here’s a quick Markdown example to help you:

   # Heading 1
   Use a single hash(#) symbol for this effect.
   ## Heading 2
   Use a double hash(##) for th1is effect.
   ### Heading 3
   Use a triple hash(###) for this effect.

   - Bullet point: Use hyphen(-) symbol.

   **Bold Text**: Wrap your text between two asteriks(**).

   [Find the markdown cheatsheet here](../../assets/markdown_cheat_sheet_opensource.com_.pdf).

4. Save your changes when you are done.

## 5. Update the Changelog

Every change you make should be recorded in the CHANGELOG.md file.

1. Open the CHANGELOG.md file.
2. Add an entry under the appropriate version heading. If no version heading exists, create one:

## 6. Push Your Changes

Now that you’ve made and saved your changes, you’ll need to push them to your forked repository.

1. Stage your changes:
   ```bash
   git add .
   ```
2. Commit your changes with a meaningful message:
   ```bash
   git commit -m "Added contribution guide and updated changelog"
   ```
3. Push your changes to your fork:
   ```bash
   git push origin update-readme
   ```

## 7. Create a Pull Request

A pull request (PR) is how you ask to merge your changes into the main repository.

1. Go to your forked repository on GitHub.
2. Click on the Pull Requests tab.
3. Click on the New Pull Request button.
4. Select your branch (e.g., update-readme) and compare it with the main repository’s branch (usually main or master).
5. Add a title and description for your PR:
    - Title: Added Contribution Guidelines
    - Description: Describe the changes you made, e.g., “Added detailed steps for contributing and updated the changelog.”
6. Click Create Pull Request.

## 8. Wait for Review

The project maintainers will review your pull request. Here’s what might happen:

1. Approved: Your changes will be merged into the main project!
2. Requested Changes: Maintainers might ask for modifications. Follow their feedback and update your PR.

3. To update your PR after feedback:

    - Make the required changes in your local branch.
    - Commit and push the changes:
      ```bash
      git add .
      git commit -m "Fixed feedback issues"
      git push origin update-readme
      ```
      
## Additional Tips

    - Ask for Help: If you’re stuck, feel free to ask for help by commenting on your pull request or opening an issue.
    - Test Your Changes: Ensure that your changes look correct by previewing them in your text editor or GitHub.

Congratulations! 🎉 You’ve successfully contributed to the project. Thank you for making this documentation better for everyone!
