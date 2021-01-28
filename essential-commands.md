# Essential commands

## Manipulating files

- diff and create patch files

```
diff -u file1  file2 | tee patchfile
--- file1       2021-01-28 21:13:55.299168645 +0000
+++ file2       2021-01-28 21:14:05.560070820 +0000
@@ -1 +1,2 @@
 I'm file1
+I'm file2

patch -p0  < patchfile
```


- sed `s/something/new/` 
- sed `1d`

## Running commands in parallel

- password of the user must be the same, or use ssh-keys

```
parallel-ssh  -h hosts.txt -A -i "hostname"
Warning: do not enter your password if anyone else has superuser
privileges or access to your account.
Password:
[1] 21:26:12 [SUCCESS] sgm@lfce-client
lfce-client
[2] 21:26:12 [SUCCESS] sgm@lfce-server
lfce-server
```
 
