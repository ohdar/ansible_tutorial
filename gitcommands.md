
git config --list --local

git config --list

git config -l

git config --global --list

git config --list --show-origin

# Git configuration variables can be stored at three different levels. Each level overrides values at the previous level.

1. System level (applied to every user on the system and all their repositories)

to view, 
    git config --list --system (may need sudo)
to set, 
    git config --system color.ui true
to edit system config file, 
    git config --edit --system

2. Global level (values specific personally to you, the user).

to view, 
    git config --list --global
to set, 
    git config --global user.name xyz
to edit global config file, 
    git config --edit --global

3. Repository level (specific to that single repository)

to view, 
    git config --list --local
to set, 
    git config --local core.ignorecase true (--local optional)
to edit repository config file, 
    git config --edit --local (--local optional)

How do I view all settings?
Run 
git config --list, 
showing system, global, and (if inside a repository) local configs

Run 
git config --list --show-origin, 
also shows the origin file of each config item

How do I read one particular configuration?
Run 
git config user.name to get user.name, for example.
You may also specify options --system, --global, --local to read that value at a particular level.


---------------#Configure your Git username/email#----------------------
You typically configure your global username and email address after installing Git. However, you can do so now if you missed that step or want to make changes. After you set your global configuration, repository-specific configuration is optional.

Git configuration works the same across Windows, macOS, and Linux.

#To set your global username/email configuration:
Open the command line.

Set your username:
    git config --global user.name "FIRST_NAME LAST_NAME"

Set your email address:
    git config --global user.email "MY_NAME@example.com"


#To set repository-specific username/email configuration:
From the command line, change into the repository directory.

Set your username:
    git config user.name "FIRST_NAME LAST_NAME"

Set your email address:
    git config user.email "MY_NAME@example.com"

Verify your configuration by displaying your configuration file:
    cat .git/config