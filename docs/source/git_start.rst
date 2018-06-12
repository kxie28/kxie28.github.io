Git Notes
=========

Create local repository::
	
	mkdir ~/MyProject
	
	cd ~/MyProject
	
Next use a git command::
	
	git init
	
This code tells the computer to recognize this directory as a local Git repository. This Git directory is a hidden file inside the dedicated repo.

Create a readme::
	
	touch Readme.txt
	
This just creates a text file with nothing inside.

If you type in: ::
	
	git status
	
You'll find that it says:

# On branch master

# Untracked files:

# (use "git add ..." to include in what wil lbe committed)

#

# 		Readme.txt

Now, to make Git notice that the file (*Readme.txt*) is there, type: ::

	git add Readme.txt
	
	git commit -m "Add Readme.txt"
	
Now to connect your local repo to your github repo. We have to add it to Git's knowledge, just like how Git didn't acknowledge out files until *git add* was used. ::

	git remote add origin https://github.com/username/myproject.git
	
Git now sees there's a remote repo and it's where you want your local repo changes to go, to confirm: ::
		
	git remote -v
	
To push changes to GitHub repo: ::
	
	git push
	
If the push doesn't work because of "no upstream branch" ::

	git push -f origin master

Update head to master ::

	git update-ref HEAD master

Error: Updates were rejected because the tip of your current branch is behind its remote counterpart. 
Go to the main repo and ::
	
	git pull -rebase
	
Go back to the branch and ::
	
	git push -f origin 'branch'


