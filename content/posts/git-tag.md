---
title: "Git Tag"
date: 2022-11-24T10:08:45+06:00
draft: true
---

# git tag  

git tag marks a specific commit in the source repository.  
 A common use case of the `git tag` is to denote software release versions
 by marking the points of commit history where the software was stable to release. 

 1. `git tag` command gives the list of all tags.  
 2. `git tag <tagname>` creates a tag named **tagname**. This tag points to the commit that was the head of the repo at the time of the tag creation.  
 3. Tags can be made to point to another commit other than the HEAD.  
 4.  Tags are not pushed to the remote by default. `git push <remotename> <tagname>`  
 5.  To push all the tags at once use `git push <remotename> --tags`
 6.  `git tag -d <tagname>` to delete a tag from the repo.  
 7.  There are two types of tags. lightweight and annotated tags. lightwieght tags have only a name whereas annotated tags contains some more metadata e.g. name, email of the author of the tag, an annotation message. By default command `git tag <tagname>` creates a lightweight tag. To create an annotated tag use `git tag -a -m "annotation msg" <tagname>`.




