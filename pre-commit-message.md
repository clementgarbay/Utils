# Git hook for commit message

## What the script do ?

This script allow you to add automatically the ticket number in your commit message.
Works for these types of branch name structure : 

- LAB-440/new-feature
- type/LAB-440/new-feature 
- LAB-440-new-feature 
- type/LAB-440-new-feature

## How it works ?

To allow the script to fill your commit message, you must use this keyword at the beginning of your commit message "$ ".

### Example : 

You're on a branch called : LAB-440/new-feature

    $ git commit -m "$ Update Changelog"

The commit message will become "[LAB-440] Update Changelog"

## How to use it ?

In the root folder of your repository :
    
    $ cd .git/hooks/
    $ mv prepare-commit-msg.sample prepare-commit-msg
    $ vim prepare-commit-msg

And write this snippet on it:

    #!/bin/bash
    
    TYPES=["feature","fix","bugfix","QA"]

    FULL_BRANCH_NAME=$(git symbolic-ref --short HEAD)
    TICKET=$(echo $FULL_BRANCH_NAME | cut -d / -f 1 | cut -d - -f 1,2)

    if [[ " ${TYPES[*]} " == *"$TICKET"* ]]; then
        TICKET=$(echo $FULL_BRANCH_NAME | cut -d / -f 2 | cut -d - -f 1,2)
    fi

    CURRENT_COMMIT_MESSAGE=$(cat $1)
    FIRST_CHARACTERS="$(echo $CURRENT_COMMIT_MESSAGE | head -c 2)"
    CURRENT_COMMIT_MESSAGE=$(echo $CURRENT_COMMIT_MESSAGE | cut -c 3-)

    if [ -n "$TICKET" ] && [ "$FIRST_CHARACTERS" == '$ ' ]; then
        echo "[$TICKET] $CURRENT_COMMIT_MESSAGE" > $1
    fi
    
Then you should give the file executable using a ```$ chmod +x prepare-commit-msg```