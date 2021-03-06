---
title       : Undo
description : >-
  Since Git saves all the changes you've made to your files, you can
  use it to undo those changes. This chapter shows you several ways to
  do that.

--- type:BulletConsoleExercise key:54325e15f1
## How can I commit changes selectively?

You don't have to put all of the changes you have made recently into the staging area at once.
For example,
suppose you are adding a feature to `analysis.R`
and spot a bug in `cleanup.R`.
After you have fixed it,
you want to save your work.
Since the changes to `cleanup.R` aren't directly related to the work you're doing in `analysis.R`,
you should save your work in two separate commits.

The syntax for staging a single file is `git add path/to/file`.

If you make a mistake and accidentally commit a file you should have, you can unstage the additions with `git reset HEAD` and try again.

*** =pre_exercise_code
```{python}
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-01,bicuspid\n')
with open('dental/data/eastern.csv', 'a') as writer:
    writer.write('2017-11-02,molar\n')
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('git status')
```

*** =type1: ConsoleExercise
*** =key1: 387ad1a970

*** =xp1: 50

*** =instructions1

From the output of `git status` on the right, you'll see that two files were changed; `data/northern.csv` and `data/eastern.csv`.
Stage only the changes made to `data/northern.csv`.

*** =hint1

Use `git add` with a file path to stage the changes of this file.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git add data/northern.csv
```

*** =sct1
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(
        expr = 'git diff --name-only --staged | grep data/northern.csv',
        output = 'data/northern.csv',
        strict = True,
        incorrect_msg = "It seems that `data/northern.csv` was not added to the staging area. Have you used `git add data/northern.csv`?"
    ),
    check_not(
        has_expr_output(
            expr = 'git diff --name-only --staged | grep data/eastern.csv',
            output = 'data/eastern.csv',
            strict = True
        ),
        incorrect_msg = "It seems that `data/eastern.csv` was added to the staging area. Unstage it with `git reset HEAD` then try again."
    )
)
```

*** =type2: ConsoleExercise
*** =key2: 381b4ed025

*** =xp2: 50

*** =instructions2

Commit those changes with the message "Adding data from northern region."

*** =hint2

Use `git commit` with `-m "message"`.

*** =sample_code2
```{shell}
```

*** =solution2
```{shell}
git commit -m "Adding data from northern region."
```

*** =sct2
```{python}
msg = "It seems that the changes to `data/northern.csv` you staged in the previous instruction weren't committed. Have you used `git commit` with `-m \"message\"`?"
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr='git diff HEAD~ --name-only | grep data/northern.csv',
                    output = 'data/northern.csv',
                    incorrect_msg=msg)
)
not_committed_msg = "It seems that the staged changes to `data/northern.csv` weren't committed. Have you used `git commit` with `-m \"Adding data from northern region.\"`?"
bad_message_msg = 'It seems the commit message was incorrect. You can amend it with `git commit --amend -m "new message"`.'
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_or(
        has_expr_output(
            expr='git diff HEAD~ --name-only | grep data/northern.csv',
            output = 'data/northern.csv',
            strict=True,
            incorrect_msg=not_committed_msg
        ),
        has_code(r'\s*git\s+commit', incorrect_msg = not_committed_msg)            
    ),
    has_expr_output(
        expr='git log -1 | grep --only-matching "Adding data from northern region"',
        output = 'Adding data from northern region',
        strict=True,
        incorrect_msg=bad_message_msg
    )
)
Ex().success_msg("Well done! Let's have a look at re-staging files.")
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:70a86d3080
## How do I re-stage files?

People often save their work every few minutes when they're using a desktop text editor.
Similarly,
it's common to use `git add` periodically
to save the most recent changes to a file to the staging area.
This is particularly useful when the changes are experimental
and you might want to undo them without cluttering up the repository's history.

*** =pre_exercise_code
```{python}
append = '''

Fehrenbach: "Dental Anatomy Coloring Book" (2e), 2013.
'''
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-01,bicuspid\n')
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('git add data/northern.csv')
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-02,bicuspid\n')
```

*** =type1: ConsoleExercise
*** =key1: b5a9b33d2e

*** =xp1: 50

*** =instructions1

You are in the `dental` repository.
Use `git status` to check the status of the repository.

*** =hint1

Run the command as shown.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git status
```

*** =sct1
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_correct(
        has_expr_output(),
        has_code(r'\s*git\s+status\s*', incorrect_msg = "Have a look at the status of your repository with `git status`.")
    )
)
```

*** =type2: ConsoleExercise
*** =key2: 481636c0fc

*** =xp2: 50

*** =instructions2

It appers that some changes to `data/northern.csv` have already been staged, but there are new changes that haven't been staged yet.
Use `git add` to stage these latest changes to `data/northern.csv` again.

*** =hint2

Remember, you can use either the name of the file or the name of a directory as an argument to `git add`.

*** =sample_code2
```{shell}
```

*** =solution2
```{shell}
git add data/northern.csv
```

*** =sct2
```{python}
msg1 = "Have you used `git status data/northern.csv` to stage the latest changes to `data/northern.csv` as well?"
msg2 = "It appears there are still changes to be staged. " + msg1
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr='git diff --name-only --staged | grep data/northern.csv',
                    output='data/northern.csv', strict=True, incorrect_msg=msg1),
    has_expr_output(expr='git diff --name-only | wc -w', output='0', incorrect_msg=msg2)
)
Ex().success_msg("Alright! Let's expore how you can undo some of your work.")
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:7ff1cc0a0e
## How can I undo changes to unstaged files?

Suppose you have made changes to a file,
then decide you want to **undo** them.
Your text editor may be able to do this,
but a more reliable way is to let Git do the work.
The command:

```
git checkout -- filename
```

will discard the changes that have not yet been staged.
(The double dash `--` must be there to separate the `git checkout` command
from the names of the file or files you want to recover.)

*Use this command carefully:*
once you discard changes in this way,
they are gone forever.

*** =pre_exercise_code
```{python}
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-01,bicuspid\n')
with open('dental/data/eastern.csv', 'a') as writer:
    writer.write('2017-11-02,molar\n')
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('git add data/*.csv')
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-02,bicuspid\n')
repl.run_command('git status')
```

*** =type1: ConsoleExercise
*** =key1: 9a5bde4d0b

*** =xp1: 100

*** =instructions1

You are in the `dental` repository, where all changes to `.csv` files in `data` were staged.  `git status` shows that `data/northern.csv` was changed again after it was staged.

Use a Git command to undo the changes to the file `data/northern.csv`.

*** =hint1

Use `git checkout` with `--` to separate the command from the name(s) of file(s),
and then the name(s) of file(s).

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git checkout -- data/northern.csv
```

*** =sct1
```{python}
msg = "After running your command, there should be no changes that can be staged. Have you used `git checkout` on `data/northern.csv` correctly? Make sure to use a `--` to separate the command from the name of the file."
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr='git diff --name-only | wc -w', output='0', incorrect_msg=msg)
)
Ex().success_msg("Good job! This was about undoing changes that weren't staged yet. What about undoing changes that you staged already with `git add`? Find out in the next exercise.")
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:fba584b9f1
## How can I undo changes to staged files?

At the start of this chapter you saw that `git reset` will unstage files that you previously staged using `git add`. By combining `git reset` with `git checkout`, you can undo changes to a file that you staged changes to. The syntax is as follows.

```
git reset HEAD path/to/file
git checkout -- path/to/file
```

(You may be wondering why there are two commands for re-setting changes.
The answer is that unstaging a file and undoing changes are both special cases
of more powerful Git operations that you have not yet seen.)

*** =pre_exercise_code
```{python}
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-01,bicuspid\n')
with open('dental/data/eastern.csv', 'a') as writer:
    writer.write('2017-11-02,molar\n')
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('git add data/*.csv')
repl.run_command('git status')
```

*** =type1: ConsoleExercise
*** =key1: d0aa935274

*** =xp1: 50

*** =instructions1

Use `git reset` to unstage the file `data/northern.csv`
(and *only* that file).

*** =hint1

Use `git reset` with the symbolic name of the most recent revision (which is always `HEAD`)
and the name of the file to be restored.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git reset data/northern.csv
```

*** =sct1
```{python}
msg1 = "The checker expected the file `data/eastern.csv` to still be staged. Make sure to re-add it with `git add data/eastern.csv`."
msg2 = "The checker expected `data/northern.csv` to no longer be staged. Have you used `git reset ___ data/northern.csv` (fill in the blank) correctly?"
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr='git diff --name-only --staged | grep data/eastern.csv',
                    output='data/eastern.csv', strict=True, incorrect_msg=msg1),
    has_expr_output(expr='git diff --name-only | grep data/northern.csv',
                    output='data/northern.csv', strict=True, incorrect_msg=msg2)
)
```

*** =type2: ConsoleExercise
*** =key2: dc8ce69579

*** =xp2: 50

*** =instructions2

Use `git checkout --` to undo the changes sicne the last commit for `data/northern.csv`.

*** =hint2

Use `git checkout` with two dashes and the name of the file to be restored.

*** =sample_code2
```{shell}
```

*** =solution2
```{shell}
git checkout -- data/northern.csv
```

*** =sct2
```{python}
msg = "After running your command, there should be no changes that can be staged. Have you used `git checkout` on `data/northern.csv` correctly? Make sure to use a `--` to separate the command from the name of the file."
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr='git diff --name-only | wc -w', output='0', incorrect_msg=msg)
)
Ex().success_msg("That's how it's done!")
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:61872a66b5
## How do I restore an old version of a file?

You previously saw how to use `git checkout` to undo the change that you made since the last commit. This command can also be used to go back even further into a file's history and restore versions of that file from a commit. In this way, you can think of committing as saving your work, and **checking out** as loading that saved version.

The syntax for restoring an old version takes two arguments: the hash that identifies the version you want to restore, and the name of the file.

For example, if `git log` shows this:

```
commit ab8883e8a6bfa873d44616a0f356125dbaccd9ea
Author: Author: Rep Loop <repl@datacamp.com>
Date:   Thu Oct 19 09:37:48 2017 -0400

    Adding graph to show latest quarterly results.

commit 2242bd761bbeafb9fc82e33aa5dad966adfe5409
Author: Author: Rep Loop <repl@datacamp.com>
Date:   Thu Oct 16 09:17:37 2017 -0400

    Modifying the bibliography format.
```

then `git checkout 2242bd report.txt` would replace the current version of `report.txt` with the version that was committed on October 16. Notice that this is the same syntax that you used to undo the unstaged changes, except `--` has been replaced by a hash.

Restoring a file doesn't erase any of the repository's history.
Instead, the act of restoring the file is saved as another commit, because you might later want to undo your undoing.

One more thing: there's another feature of `git log` that will come in handy here. Passing `-` then a number restricts the output to that many commits. For example, `git log -3 report.txt` shows you the last three commits involving `report.txt`.

*** =pre_exercise_code
```{python}
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('cat data/western.csv')
```

*** =type1: ConsoleExercise
*** =key1: 8962ae79b3

*** =xp1: 25

*** =instructions1

The current contents of `data/western.csv` are shown in teh terminal. Use `git log -2` to list the last two changes to that file.

*** =hint1

Use `git log -2` with the name of the file as an argument.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git log -2 data/western.csv
```

*** =sct1
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(incorrect_msg = "Have you used `git log -2 ___` (fill in the blank)?")
)
```

*** =type2: ConsoleExercise
*** =key2: a03a79d2de

*** =xp2: 25

*** =instructions2

Use `git checkout` with the first few characters of a hash
to restore the version of `data/western.csv` with message `"Adding fresh data for western region."`.

*** =hint2

- Use `git checkout` with the hash and the name of the file as arguments (in that order).
- The hash starts with `8`.

*** =sample_code2
```{shell}
```

*** =solution2
```{shell}
# Replace the commit hash with the penultimate hash from git log
git checkout 8188eb data/western.csv
```

*** =sct2
```{python}
msg = "The checker expected changes to `data/western.csv` to be staged. Have you used `git checkout ___ data/western.csv` (fill in the blank) to checkout the previous version correctly?"
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_or(
        has_expr_output(
            expr='git diff --name-only --staged | grep data/western.csv',
            output = 'data/western.csv',
            strict=True, 
            incorrect_msg = msg
          ),
        # to satisfy validator (as strict as possible)
        has_code(r'\s*git\s+checkout\s+8188eb+\s+data/western\.csv\s*')
    )
)
```

*** =type3: ConsoleExercise
*** =key3: 00df157d59

*** =xp3: 25

*** =instructions3

Use `cat` to display the updated contents of `data/western.csv`.

*** =hint3

Call `cat`, passing the filename.

*** =sample_code3
```{shell}
```

*** =solution3
```{shell}
cat data/western.csv
```

*** =sct3
```{python}
msg = "It seems that the changes to `data/western.csv` that were staged with the `git checkout` command weren't committed. Have you used `git commit` with `-m \"any message\"`?"
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(incorrect_msg = 'Did you print the contents of `data/western.csv` using `cat`?')
)
```

*** =type4: ConsoleExercise
*** =key4: 9dcac5fce4

*** =xp4: 25

*** =instructions4

Commit the restored version of `data/western.csv`.

*** =hint4

Use `git commit` as usual.

*** =sample_code4
```{shell}
```

*** =solution4
```{shell}
git commit -m "Restoring"
```

*** =sct4
```{python}
msg = "It seems that the changes to `data/western.csv` that were staged with the `git checkout` command weren't committed. Have you used `git commit` with `-m \"any message\"`?"
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_or(
        multi(
            has_expr_output(expr='git diff HEAD~ --name-only | grep data/western.csv', output='data/western.csv', incorrect_msg=msg),
            has_expr_output(expr='git diff --name-only --staged | wc -w', output='0', incorrect_msg=msg)
        ),
        # to satisfy validator (as strict as possible)
        has_code(r'\s*git\s+commit\s+-m\s+')
    )
)
Ex().success_msg("Solid, but there's more undoing to be done!")
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:d45eca9a34
## How can I undo all of the changes I have made?

So far, you have seen how to undo changes to a single file at a time using `git reset HEAD path/to/file`.
You will sometimes want to undo changes to many files.


One way to do this is to give `git reset` a directory. For example, `git reset HEAD data` will unstage any files from the `data` directory. Even better, if you don't provide any files or directories, it will unstage everything. Even even better, `HEAD` is the default commit to unstage, so you can simply write `git reset` to unstage everything.

Similarly `git checkout -- data` will then restore the files in the `data` directory to their previous state. You can't leave the file argument completely blank, but recall from [Introduction to Shell for Data Science](https://www.datacamp.com/courses/introduction-to-shell-for-data-science) that you can refer to the current directory as `.`. So `git checkout -- .` will revert all files in the current directory.

*** =pre_exercise_code
```{python}
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-01,bicuspid\n')
with open('dental/data/eastern.csv', 'a') as writer:
    writer.write('2017-11-02,molar\n')
with open('dental/report.txt', 'a') as writer:
    writer.write('\n(Because funding is the most important part of any project.)\n')
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('git add .')
repl.run_command('git status')
```

*** =type1: ConsoleExercise
*** =key1: 5964997653

*** =xp1: 50

*** =instructions1

Use `git reset` to remove all files from the staging area.

*** =hint1

Just call `git reset`.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git reset
```

*** =sct1
```{python}
instr=" Have you used `git reset`?"
msg1 = "It appears that there are still some staged files, while there shouldn't be any."+instr
msg2 = "The checker expected to find three changed files that are not staged yet."+instr
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr = 'git diff --name-only --staged | wc -w', output='0', incorrect_msg=msg1),
    has_expr_output(expr = 'git diff --name-only | wc -w', output='3', incorrect_msg=msg2)
)
```

*** =type2: ConsoleExercise
*** =key2: 3070c1d680

*** =xp2: 50

*** =instructions2

Use `git checkout` to put those files back in their previous state.
Use the directory name `.` to mean "all of the files in or below this directory",
and separate it from the command with `--`.

*** =hint2

As before,
use `git checkout` with `--` to separate the command from the names of files or directories,
and '.' as the name of the current working directory.

*** =sample_code2
```{shell}
```

*** =solution2
```{shell}
git checkout -- .
```

*** =sct2
```{python}
instr=" Have you used `git checkout -- .`?"
msg1 = "It appears that there are some staged files, while there shouldn't be any."+instr
msg2 = "The checker expected to find no files that still had changes to stage."+instr
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr = 'git diff --name-only --staged | wc -w', output='0',
                    incorrect_msg=msg1),
    has_expr_output(expr = 'git diff --name-only | wc -w', output='0',
                    incorrect_msg=msg2)
)
Ex().success_msg("Well done! This concludes chapter 3 on undoing your work. Where Git gets really interesting, though, is in its branching abilities. Find out all about it in the next chapter!")
```
