Changing Your Git Repo
======================

Let's say your username is `someuser` and your project is called `someproject`.

Then your project's URL will be ::
        
        git@github.com:someuser/someproject.git

If you rename your project, it will change the `someproject` part of the URL, e.g. ::

        git@github.com:someuser/newprojectname.git

Your working copy of `git` uses this URL when you do a `push` or `pull`.

So after you rename your project, you will have to tell your working copy of the new URL.

You can do that in two steps:

First, cd to your local git directory and find out what remote name(s) refer to that URL ::

        $ git remote -v
        origin git@github.com:someuser/someproject.git

Then, set the new URL::

        $ git remote set-url origin git@github.com:someuser/newprojectname.git
