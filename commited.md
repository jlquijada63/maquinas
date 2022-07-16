

I locate the .git directory in the VM attached (/home/ubuntu/commited/commited.zip). I decompress the file
```
ubuntu@thm-comitted:~/commited$ unzip commited.zip
```
I go to the .git directory

```
ubuntu@thm-comitted:~/commited/commited/.git$
```
I can see the branches we have in our local repository with:
```
ubuntu@thm-comitted:~/commited/commited/.git$ ls refs/heads/
dbint  master
```
As we can see we have two branches:
1. master
2. dbinit

# commits in master branch

```
ubuntu@thm-comitted:~/commited/commited/.git$ git log
commit 28c36211be8187d4be04530e340206b856198a84 (HEAD -> master)
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:49:32 2022 -0800

    Finished

commit 9ecdc566de145f5c13da74673fa3432773692502
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:40:19 2022 -0800

    Database management features added.

commit 26bcf1aa99094bf2fb4c9685b528a55838698fbe
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:32:49 2022 -0800

    Create database logic added

commit b0eda7db60a1cb0aea86f053816a1bfb7e2d6c67
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:30:43 2022 -0800

    Connecting to db logic added

commit 441daaaa600aef8021f273c8c66404d5283ed83e
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:28:16 2022 -0800

    Initial Project.
```
I gonna make a tiny script to look inside the commits authomatically

```
ubuntu@thm-comitted:~/commited/commited/.git$ git log | grep "commit" | cut -d " " -f 2 | xargs git show

```
But nothing interesting appears

# commits in dbint brach

I gonna look inside the other branch called dbint
```
ubuntu@thm-comitted:~/commited/commited/.git$ git log dbint
commit 4e16af9349ed8eaa4a29decd82a7f1f9886a32db (dbint)
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:48:08 2022 -0800

    Reminder Added.

commit c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:46:39 2022 -0800

    Oops

commit 3a8cc16f919b8ac43651d68dceacbb28ebb9b625
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:45:14 2022 -0800

    DB check

commit 6e1ea88319ae84175bfe953b7791ec695e1ca004
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:43:34 2022 -0800

    Note added

commit 9ecdc566de145f5c13da74673fa3432773692502
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:40:19 2022 -0800

    Database management features added.

commit 26bcf1aa99094bf2fb4c9685b528a55838698fbe
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:32:49 2022 -0800

    Create database logic added

commit b0eda7db60a1cb0aea86f053816a1bfb7e2d6c67
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:30:43 2022 -0800

    Connecting to db logic added

commit 441daaaa600aef8021f273c8c66404d5283ed83e
Author: fumenoid <fumenoid@gmail.com>
```
I found something interesting: a commit with a comment "Oops". I gonna see inside

```
buntu@thm-comitted:~/commited/commited/.git$ git show c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5
commit c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:46:39 2022 -0800

    Oops

diff --git a/main.py b/main.py
index 54d0271..0e1d395 100644
--- a/main.py
+++ b/main.py
@@ -4,7 +4,7 @@ def create_db():
     mydb = mysql.connector.connect(
     host="localhost",
     user="root", # Username Goes Here
-    password="flag{HERE_THE_FLAG}" # Password Goes Here
+    password="" # Password Goes Here
     )
 
     mycursor = mydb.cursor()
@@ -16,7 +16,7 @@ def create_tables():
     mydb = mysql.connector.connect(
     host="localhost",
     user="root", #username Goes here
-    password="flag{HERE_THE_FLAG}", #password Goes here
+    password="", #password Goes here
     database="commited"
     )
 
@@ -29,7 +29,7 @@ def populate_tables():
     mydb = mysql.connector.connect(
     host="localhost",
     user="root",
-    password="flag{HERE_THE_FLAG}",
+    password="",
     database="commited"
     )
```
Oops we got the flag!!!!!!

