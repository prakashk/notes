Title: Recover files eaten by git
Date: Thu Apr 10 13:54:21 2014
Tags: git

Git ate my files. This is how it happened.

I have a bunch of files that I wanted to create a git repo out of and push to github, before I continue to work on them. So, I did the equivalent of the following:

    $ cd my-project
    $ cat > local.txt
    local
    ^D
    $ ls
    local
    $ git init
    Initialized empty Git repository in ...

Then I created a new repo on github, and initialized it with a README.md. Let's call the repo 'testing1'. I added this repo as my remote:

    $ git remote add origin git@github.com:prakashk/testing1

    # there is probably a better way to do this
    $ cat >> .git/config
    [branch "master"]
        remote = origin
        merge = refs/heads/master

Then I added the local file to git.

    $ git add local.txt
    $ git status
    # On branch master
    # [...]
    #	new file:   local.txt

The next command is probably the culprit for the impending disaster.

    $ git pull origin master
    remote: Counting objects: 3, done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Unpacking objects: 100% (3/3), done.
    From github.com:prakashk/testing1
     * branch            master     -> FETCH_HEAD

    $ git status
    # On branch master
    # Untracked files:
    #   (use "git add <file>..." to include in what will be committed)
    #
    nothing added to commit but untracked files present (use "git add" to track)
    $ ls
    README.md

Huh? Where's the local.txt that added earlier? Should I have not added before pulling from remote? Should I have committed it? Not sure what went wrong, and what I should have done differently. Figuring this out is an exercise for later. First I have to find out how to recover the lost file, if I can.

Quick googling took me to [this post](http://stackoverflow.com/questions/10362425/stage-files-then-remote-add-then-pull-and-my-files-are-gone) on StackOverflow. I followed the steps outlined in the [accepted answer](http://stackoverflow.com/a/10368052).

    $ git fsck --lost-found
    Checking object directories: 100% (256/256), done.
    dangling blob 40830374235df1c19661a2901b7ca73cc9499f3d
    $ ls -l .git/lost-found/other/
    total 4
    -rw-r--r-- 1 xxxx yyyy 6 Apr 10 13:47 40830374235df1c19661a2901b7ca73cc9499f3d
    $ cat .git/lost-found/other/40830374235df1c19661a2901b7ca73cc9499f3d
    local

Phew! At least the file is there, although with a hashed name.

Lesson: Do not pull from new repo without committing local files.

[Another answer](http://stackoverflow.com/a/10369892) to the same SO question sheds some light on this:

> I received the following reply on the git mailing list from Junio Hamano:

> ----

> It is an unanticipated corner case interfering our attempt to be too
> nice that backfired.

> For a long time, having no history and asking to pull was forbidden,
> because "git pull" is is about combining two (or more) histories
> together and pulling when you have no history is a nonsense --- you only
> have one history (the history from the other side) and there is nothing
> to combine.

> Later we tried to be nicer, as some new users triggered an error when
> doing "git init" in an empty directory followed by "git pull", by
> redefining "merge" into no history to mean resetting to the other
> history.

> This solved "git init && git pull", but we did not anticipate anybody
> would do a "git init && git add && git pull" sequence, to which there is
> no sane outcome other than just erroring out.
