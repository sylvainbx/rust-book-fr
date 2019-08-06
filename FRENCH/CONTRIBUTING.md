# Contributing to translate

## Guidelines

### Translation terms

We need to coordinate us with the main English terms translations.

In this purpose, please refer to the file `/FRENCH/src/translation-terms.md`
when you need to translate a technicial term.

*(PS : see next Process `Add a translation term` on this same page)*

### Translation of Rust code

We should translate only the comments and the string text in the rust code.

All the remaining code (and terminal outputs) should stay in English (variables,
methods/functions, instructions, ...).

### Files

The name of all the files should not be translated, so just keep them in
English.

Please limit each line of Markdown file to 80 characters (including spaces). You
can write your file as you want, but it would be nice to use a tool like
[https://www.dcode.fr/text-splitter](https://www.dcode.fr/text-splitter) on your
translated paragraphs before commiting.

## Processes

### Translate flow

*NB : the following `main translate repository` refers to
https://github.com/Jimskapt/rust-book-fr*

01. Open or edit an GitHub issue in the *main translate repository* to report to
    other that you are working on `ch00-00-introduction`.
      - if someone has reported to work on the same page you want to translate :
        - if its works is *active* : you should work on another page.
        - else : you can fork its repo in the following step 02 (instead of the
          *main translate repository*), please mention it in the existing issue.
      - else : just follow the next instructions.
02. Fork the *main translate repository* in GitHub (in your account)
03. `git clone https://github.com/<your_fork>.git`
    (copy your fork on your hard-disk). You should be on the `french-release`
    branch by default.
04. `cd <your_fork>` (go inside your fork folder)
05. `git checkout -b <your_new_working_branch_name>` (create your new working
    branch)
06. Copy the file you want to translate : `/src/ch00-00-introduction.md` into
    `/FRENCH/src/ch00-00-introduction.md`
07. Add the text for the link to this file in the `/FRENCH/src/SUMMARY.md`.
08. Comment each English paragraphs of this file. The goal is to keep an
    *invisible* version of the English book, in order to easily reflect the
    changes of the English source (english-book) when they occur later, and to
    translate them.
09. Write each of your translated paragraph under each commented English
    paragraph.
      - Please quickly read following `Guidelines` currently on this page.
      - A little tip : the [deepl.com](https://www.deepl.com/) translator.
10. (optionnal) Limit each line of your translation to 80 characters, thank to a
    tool like
    [https://www.dcode.fr/text-splitter](https://www.dcode.fr/text-splitter).
11. (optionnal) `cd FRENCH && mdbook build && cd ..` (build the book in
    `/FRENCH/book`). Open its index.html file, and check its correctness. It
    also should help you for next task.
12. (optionnal) proofreading you work thanks to services like
    [bonpatron.fr](https://bonpatron.com).
13. `git add -A && git commit -m "<Description of your work>"` (committing your
    work)
14. (optionnal) `git rebase -i HEAD~<the number of commits you need to merge>`
    (squash all your commits into one commit)
15. `git push origin` (pushing your work on your fork)
16. In GitHub, create a new pull request from your fork to the main translation
    repository, in order to mark your work ready for a proofreading.
17. After proofreading (and eventualy some edits from others), it would be
    merged on `french-release` branch.

### Update from English book

01. `git remote add --track master english-book https://github.com/rust-lang/book.git`
    (Add source of the *English main repository*)
02. `git fetch english-book` (fetching the latest changes from the *English main
    repository*)
03. `git merge english-book/master` (merging latest changes from *English main
    repository* on current branch)

### Add a translation term

*(PS : see previous Guideline `Translation terms` on this same page)*

01. `git checkout -b <your_new_working_branch_name>` (create your new working
    branch)
02. Edit the `/FRENCH/src/translation-terms.md` file with your new technical
    term translation.
03. Then do a *Push Request (PR)* in order discuss it with the team.
04. While PR is being accepted and merged, you would better use the English word
    in your work instead of your translation, and then edit it when the team is
    OK with the translation.
