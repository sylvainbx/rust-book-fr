# Contributing to translate

## Translate process

*NB : the following `main translate repository` refers to https://github.com/Jimskapt/rust-book-fr*

01. Open or edit an issue in *main translate repository* to report to other that you are working on `ch00-00-introduction`. If someone has reported to work on the same page you want to translate, you should wait/skip if its works is *active*, or else you should fork its repo instead of the *main translate repository* in the following step 02, then jump to step 08 (the `git checkout`).
02. Fork the *main translate repository* in GitHub (in your account)
03. `git clone https://github.com/<your_fork>.git` (copy your fork on your hard-disk, you should be on the `french-release` branch by default)
04. `git remote add --track master english-book https://github.com/rust-lang/book.git` (Add source of the *English main repository*)
05. `git fetch english-book` (fetching the latest changes from the *English main repository*)
06. `git merge english-book/master` (merging latest changes from *English main repository* on current branch)
07. `git checkout -b <your_new_working_branch_name>` (create your new working branch)
08. Copy the file you want to translate : `/src/ch00-00-introduction.md` to `/FRENCH/src/ch00-00-introduction.md`
09. Comment each English paragraphs of this file. The goal is to keep an *invisible* version of the English book, in order to easily reflect the changes of the English source (english-book) when they occur later, and to translate them.
10. Write each translated paragraph under each commented english paragraph.
11. `git add -A && git commit -m "<Description of your work>"` (committing your work)
12. `git push origin` (pushing your work on your fork)
13. (optionnal) `git rebase -i HEAD~<the number of commits you need to merge>` (squash all your commits into one commit)
14. In GitHub, create a new pull request from your fork to the main translation repository, in order to mark your work ready for a proofreading.
15. After proofreading (and eventualy some edits from others), it would be merged on `french-release` branch.

## Guidelines

### Translation of Rust code

We should translate only the comments. All the remaining code should be stay in English (variables, methods/functions, ...).

### Terms

We need to coordinate us with the main English terms translations.

In this goal, please refer to the file `/FRENCH/src/translation-terms.md`.

Complete it on the branch `terms` when you translate a new technical term (for example : *borrowing*, *garbage-collector*, ...). (see `Translate process` part), and do a *Push Request (PR)* in order discuss it with the team. While PR is being accepted, please use the English word in your work instead of your translation.

### Files

The name of Markdown files should not be translated, so just keep them in English.

Please limit each line of Markdown file to 80 characters (including spaces). You can write your file as you want, but please use a tool like [https://www.dcode.fr/text-splitter](https://www.dcode.fr/text-splitter) on translated paragraph before commiting.
