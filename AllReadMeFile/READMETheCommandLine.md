
<h3 align="center">  Command Line</h3>

##### Terminal Cheat Sheet
<h3 align="center">  Basic Navigation!</h3>


<table>
<tr>
<th>pwd</th>
<th>ls</th>
<th>cd</th>
<th>~ (tilde)</th>
<th>. (dot)</th>

</tr>
<tr>
<td>
Print Working Directory - ie. Where are we currently.
</td>
<td>
List the contents of a directory.
</td>
<td>
Change Directories - ie. move to another directory.
</td>
<td>
Used in paths as a reference to your home directory (eg. ~/Documents ).
</td>
<td>
Used in paths as a reference to your current directory (eg. ./bin ).
</td>

</tr>
</table>


<h3 align="center"> File Manipulation!</h3>
<table>
<tr>
<th>mkdir</th>
<th>rmdir</th>
<th>touch</th>
<th>rm </th>
<th>mv <destination></th>


</tr>
<tr>
<td>
create new directory
</td>
<td>
remove directory
</td>
<td>
Create a blank file.
</td>
<td>
Move the source file to the destination.
</td>
<td>
Remove a file or directory.
</td>

</tr>
</table>



## Working with Git

### Quick Start
### git clone <url> 					
# Clone directory
###git checkout -b <new-branch> 		
 Create new local branch
### git push -u origin <new-branch> 	
 Sync local branch with remote
### git push origin <branch> 			
 Push branch to remote

 ### git branch -d <branchname>   	
  deletes local branch
### git push origin :<branchname>	
 deletes remote branch

### git subtree push --prefix docs origin gh-pages  
push docs as subtree to gh-pages



### Clone Directory
git clone <url>



### Create Project
cd project/
### git init                    
initializes the repository
### git add .                  
add those 'unknown' files
### git commit                  
 commit all changes, edit changelog entry
### git rm --cached <file>...   
 ridiculously complicated command to undo, in case you forgot .gitignore

