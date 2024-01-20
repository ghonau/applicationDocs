#Git
It is the most commonly used version control
It is because of its distributed nature
#Subversion
will need a Subversion server software and Subversion client on the developer's machine

# You will need to initialize an empty git repository on your local machine

1- git init: files have not been tracked yet but there is a .git folder in the directory

# Add files to Staging Area

2- git add: will add your file into Staging Area and now they are tracked by Git, you can call your files are staged

Just changing the file

#How to go back to a previous commit
git checkout {commit_id}
#How to show all the commit
git log --all
#Unstage file
#In order to attach head to main branch
git checkout main
#How to unstage and keep the changes in the working directory
git rm --cached "az400/Material/Source control.md"
#How to create a new branch
git checkout -b featureB
#Remote repositories

You could use GitHub or Azure repos for the remote repository and developer can clone the code and then make some changes and merge the changes back to the main repository

#Pushing to GitHub
1- you will need to create a remote repository
2- add remote origin to your local git repository
3- push your change to the remote repository
#Fast forward
Make some changes onto feature brach and main branch will point to the ONLY latest commit on the feature branch (we do not want the history of commits on the feature branch).
