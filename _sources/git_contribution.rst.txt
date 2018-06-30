Contributing to another's GitHub Project
========================================

1: Set up a working copy on your computer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Fork the project which will create a copy of the repo in your own GitHub account

Then, you need a copy of it locally, so find the "SSH clone URL" in the right hand column and use that to clone locally using a terminal:

::
	
	$ git clone git@github.com:'username'/'project.git'
	
Change into the new project's directory:

::
	
	$ cd project

Set up a new remote that points to the original project so you can grab any changes and bring them to your local copy. 

::

	$ git remote add upstream git@github.com:'project username'/'project name.git'
	
Now you have two remotes for this project on disk:

	1. *origin* which points to your GitHub fork of the project. You can read and write to this remote.
	2. *upstream* which points to the main project's GitHub repo. You can only read from this remote.
	
2: Contribute!
^^^^^^^^^^^^^^

Create a branch.
The *NUMBER 1 RULE* is to put each piece of work on its own branch. 

:: 
	
	$ git checkout master
	$ git pull upstream master && git push origin master
	$ git checkout -b 'branch name'
	
First we have to make sure we're on the master branch. The git pull command will sync our local copy with the upstream project and git push syncs it to our forked GitHub project.
Then, we make our new branch, which can be named whatever you want.

3: Create a Pull Request
^^^^^^^^^^^^^^^^^^^^^^^^

To push a new branch:

:: 

	$ git push -u origin 'branch name'
	
This will create the branch on your GitHub project. The -u flag links this branch with the remote one, so in the future all you need to type is: git push origin.

Go to the GitHub site and press the "Create pull request" button.
