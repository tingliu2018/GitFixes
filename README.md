# GitFixes

This repository's purpose is to serve as a place to find fixes to common Git/GitHub error messages and other problems that arise as students (or others) use these tools as part of their workflow.  It is not intended to be a complete manual for Git or GitHub but only as a place to look for solutions to common problems.

Please feel free to suggest new problems (and ideally fixes) by opening an Issue or a Pull Request on this repository.

## Configuration of Git at the command line

When you first use Git on a new computer or with a new account, it tries to figure out who you are and what your email address is based on information it can find on that computer and from your account.  When this happens and you try to issue a `commit`, you might get a message such as:

<pre>
[master 3c4fbf4] Hi.
 Committer: Joe Cool <jcool@noreaster.teresco.org>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly. Run the
following command and follow the instructions in your editor to edit
your configuration file:

    git config --global --edit

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 2 insertions(+)
</pre>

You probably want these to be set correctly, so it would make sense to issue these commands at your prompt:

<pre>
git config --global user.name "jcool"
git config --global user.email "jcool@teresco.org"
</pre>

Of course, replacing these with your actual username and email.

## Can't `push` because of rejected updates

Perhaps the most common error message occurs when trying to `push` some commits from your clone of a repository back up to GitHub, but there have been changes on the `origin` repository on GitHub that have not yet been brought into your clone.  The message usually looks something like this:

<pre>
To https://github.com/jteresco/GitFixes.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/jteresco/GitFixes.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
</pre>

The message might be a little scary and a bit cryptic for the novice user, but the solution is in there.  You need to `pull` the changes on GitHub to your clone before you can `push` your clone's changes to GitHub.  So do it:

<pre>
git pull
</pre>

At this point, you might encounter the messages in the next section.

## Merging with `git pull`

When you issue a `git pull` command, there are a few things that might happen when, depending on whether Git can merge your changes in the clone with the changes on GitHub automatically, or if it encounters any *conflicts* that you will need to help it to resolve.

If Git can merge the changes you are trying to `pull` from the origin on GitHub automatically, you will see a message like this:

<pre>
Auto-merging README.md
</pre>

That would be good news, and it means you should then be able to complete your `push` operation.  A common complication, however, is that Git will want a commit message to attach to the merge.  In this case, it will likely open up a text editor to edit a file whose contents are initially something like this:

<pre>
Merge branch 'master' of https://github.com/jteresco/GitFixes

# Please enter a commit message to explain why this merge is necessary,
# especially if it merges an updated upstream into a topic branch.
#
# Lines starting with '#' will be ignored, and an empty message aborts
# the commit.
</pre>

You can add a custom `commit` message here, save the file, exit your editor and then you should see something like this:

<pre>
Merge made by the 'recursive' strategy.
 README.md | 34 ++++++++++++++++++++++++++++++++++
 1 file changed, 34 insertions(+)
</pre>

A further complication, getting stuck in the `vim` editor, is discussed in a section later in this document.


However, in many sitations, Git cannot merge automatically, and you will see a message like this:

<pre>
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/jteresco/GitFixes
   20c44ed..4526407  master     -> origin/master
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
</pre>

In this case, your task is then to look at the file(s) that had conflicts and make the version in your clone the "good" version.  You can identify the conflicting sections in your files, as they will have one of the changes shown between a line that looks like

<pre>
<<<<<<< HEAD
</pre>

and a line that looks like

<pre>
=======
</pre>

and the other change between that same line and one that looks like

<pre>
>>>>>>> 4526407d6f436dbf82addcb14e7a09d3043500e2
</pre>

In your "fixed up" files, you will delete those delimiter lines and keep whatever combination of lines in between you would like to make the new "good" version.

Once you're done with that, Git will require that you do a full commit, not a partial commit.  If you attempt a partial commit, you will get this message:

<pre>
fatal: cannot do a partial commit during a merge.
</pre>

A full commit is one that includes the `-a` flag to indicate "all files" and cannot specify individual file names on your command line:

<pre>
git commit -a -m "Resolved merge conflicts"
</pre>

After that command, you can then `push` the version in your clone up to the origin on GitHub.

## Can't `pull` because of uncommitted local changes

If you try to `pull` down changes from the origin on GitHub to your clone, and you have uncommitted local changes in a file that would be changed by the `pull`, you might see a message like this:

<pre>
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/jteresco/GitFixes
   7c0ab10..79e7c72  master     -> origin/master
Updating 7c0ab10..79e7c72
error: Your local changes to the following files would be overwritten by merge:
	README.md
Please commit your changes or stash them before you merge.
Aborting
</pre>

In this situation, there are two things you might want to do.

If the changes in your clone are ones you want to keep, you'll need to `commit` them before you can complete the `pull`.

<pre>
git commit -m "My commit message" README.md
</pre>

Where `README.md` would be replaced with the file or files you need to `commit` to be able to do the `pull`.  Once you do that, reissue the `pull` command:

<pre>
git pull
</pre>

At that point, the merge will either be successful or not, as shown

If your clone changes were inadvertent or would otherwise no longer be needed once the origin's version is pulled down, you can revert the file back to the last committed version with a command like:

<pre>
git checkout README.md
</pre>

Of course, you'd replace `README.md` with the name of the file that prevented your original `pull` from completing.  After the `checkout`, you should then be able to complete the `pull`.

## Stuck in the `vim` editor

Any time Git would like a `commit` message but you did not provide one, such as by using the `-m` flag, it will launch a text editor to allow you to specify that message.  In many cases, the editor used defaults to the powerful but ancient and cryptic programmer's editor called `vim`.  If this happens, you might see a screen that looks something like this:

<pre>
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Committer: Joe Cool <jcool@noreaster.teresco.org>
#
# On branch master
# Your branch is up to date with 'origin/master'.
#
# Changes to be committed:
#       modified:   README.md
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
                                                              12,1          All
</pre>

When the unsuspecting Git user is placed in `vim` and starts typing, strange things will happen.  If you get into this situation, the easiest way out is to save your file unmodified by forcing `vim` to exit without saving any changes.  You can do this by typing a colon (`:`) followed by a 'q', followed by an exclamation point ('!'), and then type Enter.  Yes, really.  `vim` users are perfectly happy to do this, but if you're reading this page, you're probably not a `vim` user, and you likely don't ever want to become one.  In that case, you really want to avoid getting into this situation in the first place.  You can do that by telling Git to use a different default editor when it feels the need to have you edit a file.

Most systems that have the `vim` editor are also likely to have the much more intuitive (though still text-based) `nano` editor.  You can tell Git to use `nano` all the time by issuing this command:

<pre>
git config --global core.editor "nano"
</pre>

When you get placed into `nano` to edit a file, you will see a set of commands across the bottom of your window:

<pre>
^G Get Help  ^O Write Out ^W Where Is  ^K Cut Text  ^J Justify   ^C Cur Pos
^X Exit      ^R Read File ^\ Replace   ^U Uncut Text^T To Spell  ^_ Go To Line
</pre>

The important thing to be aware of here is that the `^` followed by a letter means to hold the Control key and press that letter.  When you're done adding your commit message to the editor, you can save with Control-o, taking the default file name provided (something like `COMMIT_EDITMSG`), then Control-x to exit.
