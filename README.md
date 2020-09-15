# Git basics

Git is a very powerful tool with a _lot_ of commands. Here is the small subset of commands that I use a million times a day. We'll add to our toolbelt as time goes on:

 1. To initialize a repository in a folder:
 
    ```
    git init
    ```
    
 1. To take a snapshot of your work (all of the files and subfolders), use the following two commands from the root folder of the repository:

    ```
    git add -A
    git commit -m "Title of your snapshot"
    ```

 1. Try to make the title somewhat descriptive of what you did since the last snapshot, but it's more important that you just **make lots of commits**. So if you must just say `git commit -m "WIP"` (for "work in progress"), that's fine; we can clean it up later.
 1. Our golden rule: **A**lways **B**e **C**ommitting. As long as you do that, everything will be okay; we can recover from anything with git as long as you commit early and often. You cannot _over_commit but you can most certainly _under_commit.
 1. To see the current status of the repository (are there any changed files since the last commit? etc):

    ```
    git status
    ```
 
 1. To see the line-by-line changes since the last commit:

    ```
    git diff
    ```

 1. To start a new version, or **branch**:

    ```
    git checkout -b fancy-new-version-name
    ```
 1. Again, branch early and often. There's no cost to it and there's lots of benefits to it. In particular, it's good to start a new branch for each _task_ or _feature_. Or just a different approach to the same task, if you want to start over.
 1. To list all branches:

    ```
    git branch
    ```

 1. To switch to another existing branch:

    ```
    git checkout branch-name
    ```

 1. Sometimes, if you have made edits to files that you haven't committed yet, it won't let you switch to another branch. Which is good; you have to make a decision about those changes first — do you want to save them or not? Your options:

    - Make a commit first, and then you can switch to the other branch.
    - If you don't want to pollute your current branch, you can make a new branch, commit the changes, and then switch to the other branch.
    - You can quickly "stash" the changes with `git add -A; git stash`. This puts the changes into a randomly named branch that will eventually be deleted after a few weeks, but until then you can get the changes back if you want them. This is the equivalent of the above, but saves you the trouble of having to think of a name for the new branch.
    
    I usually just think of `git add -A; git stash` as a convenient way to discard all the changes since my most recent commit, so that I can start afresh; but, there _is_ a way to get those changes back if I want them (this only happens about once a year).
 
 1. To see the history of your current branch:

    ```
    git log
    ```

    You will see the author, date, and title of each commit preceded by a long sequence of letters and numbers known as the "SHA-1 hash" of the commit. This is a unique fingerprint of the snapshot.

 1. (Advanced) To jump back to a prior commit:

    ```
    git checkout [SHA of commit you want to go back to]
    ```

    Find the SHA by looking at the output if `git log`. Just the first 7 or so digits of the SHA are enough; you don't need the whole thing.

    If you jump to a commit like this, you'll be in a "detached" state — i.e., not on any branch. This is okay for browsing, but it's best not to make any changes.

    If you want to start a new branch from this point, though, that's perfectly fine — I do that all the time when I decide I want to try a new approach. Just `git checkout -b new-branch-name` as usual.

 1. When you're ready to send the work you've done on a branch back to GitHub.com:

    ```
    git push
    ```

    - The very first time you `push` to a branch, it may ask you to do something like:
        
        ```
        git push --set-upstream origin your-branch-name
        ```
    - The very first time you `push` from Gitpod, it may ask you to give it permission to do so from GitHub. Go ahead and do so. 

 1. To retrieve the freshest version of the branch you're on from GitHub.com, in case there have been any updates:

    ```
    git pull
    ```

 1. When you're ready to start on a new task, the best practice is to first switch back to the `master` branch, get the freshest version, and then to start a new branch from there:

    ```
    git checkout master
    git pull
    git checkout -b my-next-task
    ```

 1. **Never** make commits directly to the `master` branch. Always work on your own, experimental branch. We'll talk about why this is soon.

I know this is a lot, but our main focus today is just getting our feet wet with git. Please wave over an instructor as you experiment with these commands and ask questions; remember:

> Questions are places in your mind where answers fit. If you haven’t asked the question, the answer has nowhere to go. — Clay Christensen

## Merging branches

When you're reading to bring the changes from one branch (usually from one of your experimental branches, e.g. `your-name-first-branch`) into another branch (usually into the `master` branch), we have a couple of options:

 1. Fundamentally, we use Git's `merge` command at the command-line.
 1. In the early days, I recommend pushing your branch to GitHub and using GitHub's interface for merging.

### Use GitHub's interface to merge

To merge changes from e.g. `your-name-first-branch` into `master` using GitHub's interface:

 1. Push your branch to GitHub:
 
    ```
    git checkout your-name-first-branch
    git push
    ```
    
 1. [Create a Pull Request](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request).
 1. [Merge the PR](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/merging-a-pull-request). You're given a few strategies to choose from on how to do so; choose [Squash and merge](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-request-merges#squash-and-merge-your-pull-request-commits). This will combine all of your work-in-progress commits into just one commit; now's your chance to craft an informative Git commit for posterity.
 1. Switch back to `master` on your machine and fetch the merged version:
 
    ```
    git checkout master
    git pull
    ```

### git merge command

To merge changes from e.g. `your-name-first-branch` into `master` using the command-line:

 1. First switch to `master` and make sure it is up to date:
 
    ```
    git checkout master
    git pull
    ```
    
 1. Switch back to your feature branch and "rebase interactively" onto `master`. This means, essentially, sync up the feature branch with any changes that may have occurred on `master` since they time you created the branch:
 
    ```
    git checkout your-name-first-branch
    git rebase -i
    ```
    
 1. A text editor will pop up with a list of all of the commits you made along the way on your feature branch. Replace `pick` for all but one of them with `s` (for `squash`). Save and close the file.
 1. Another text editor will open where you can craft [a wonderful commit message](https://chris.beams.io/posts/git-commit/) to communicate the WHY of your changes (the WHAT is told by the diff).
 1. Now that all your messy work-in-progress commits have been ironed out, we can merge:
 
    ```
    git checkout master
    git merge your-name-first-branch
    ```
 
 1. That's it — verify with `git log`. It's as if you made one, beautiful commit directly to `master`.
 
 
