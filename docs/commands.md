# Useful Terminal/PowerShell Commands

Depending on your operating system, open either terminal (Mac) or PowerShell (Windows). There are some similarities and some differences between Terminal and PowerShell commands. One important one is the usage of slashes (/) in case of Mac, and backslashes (\) in case of Windows, when specifying a path. 


## 1. List files in the current working directory

```bash
ls
```

## 2. Change directory
### 2.1 Relative path
```bash
cd ./Downloads
```
the . before slash (/) means current directory
```bash
cd ..
```
.. means parent directory
### 2.2 Absolute path 

=== "Mac"

    ``` bash
    cd Users/Peter/Downloads
    ```

=== "Windows"

    ``` bash
    cd C:\Users\Peter\Downloads
    ```

## 3. Create directory
``` bash
mkdir example
```
``` bash
ls
```
You should now see a directory called "example" in your current working directory. 

## 4. Create a .txt file
=== "Mac"

    ``` bash
    cat > test.txt
    ```

=== "Windows"

    ``` bash
    New-Item test.txt
    ```

Write something in the file (at least two lines). 

## 5. Head and Tail

When working with large files, we encourage you examine them. Since files are often too big
to be loaded into Excel, head and tail commands are good alternatives to get a good idea of
what the file looks like (this only works for unzipped files, i.e. non-binary).
=== "Mac"

    ``` bash
    head -10 test.txt
    ```

=== "Windows"

    ``` bash
    Get-Content test.txt -Head 10
    ```

You can replace the word head with tail to see the end of the file. 

## 6. Docker 
Docker is a tool designed to make it easier to create, deploy, and run applications by using
containers. Containers allow a developer to package up an application with all of the parts it
needs, such as operating system, libraries and other dependencies, and ship it all out as one
package.

By running the following command, you create a docker container from an image that
includes Ubuntu distribution and QIIME2.

``` bash
docker run --rm -v ${pwd}:/data/ -w /data/ -it quay.io/qiime2/core:2023.5
```

After running this command, you should be in the container. This is indicated by the following text in your terminal (qiime2-2023.5). Once you list the files in the
current working directory the files on your local computer should be reflected in the printed list. 

We encourage you to check the help command and identify what each of the options mean
(e.g. --rm, -it, -v, -w).

``` bash
$ docker run --help
```
Files that you create while running qiime2 in the docker container will be reflected in your
local directory after running the workflow commands. 

<!-- ## Some useful commands 

- `pwd` (print working directory)
- `ls` (list)
- `nano` (basic editor for creating small text files)
- `rm` (remove files)
- `mkdir` (make a directory)
- `cd` (change directory)
- `mv` (rename or move files)
- `less` (view files)
- `man` (manual)
- `cp` (copy)
- `head` (see first 10 lines of a file)
- `tail` (see last 10 lines of a file) 
- `wc` (count newlines) -->