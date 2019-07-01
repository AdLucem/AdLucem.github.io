---
layout: post
title: Quick and Dirty Prototyping: Haskell vs. Python (Also Project Manager Part 1)
date: 2019-06-09
category: blog
published: true
---

I have a problem. I have far too many projects spread out over three different systems- a remote machine, my laptop, and my PC in my lab, and I can't keep track of what's updated and in sync with what. Complicating the matter is the fact that I have two primary git accounts- one on gitlab and one on github.

I have a first-iteration solution to that. I want a program that:

(1) Can detect all of my git repositories on a PC
(2) Tell me which account they're from
(3) Run a `git pull` on all of them every time I ask it to

I know how to implement all three of these- by scanning the `.git` file in each git repository. And I want this done in a couple of hours so I can get back to actual productivity.

So basically, I want a quick and dirty prototype that implements these three simple requirements. Now let's go through the process of implementing one in two different languages *in parallel* - Haskell and Python3.

## Project Directory And Build Systems

For Python I'm using the new `pipenv` tool, even though I haven't seen it be as widely adopted as the old-style directory + `requirements.txt`:

```bash
$ mkdir project_manager && cd project_manager
$ pipenv --three shell
```

Now I can install any requirements using `pipenv install <package name>`, and it will automatically add it to my pipfile. Warning: `pipenv` takes a while to set up a virtual environment; you might want to take a short break during this process.

For Haskell I'm using the tried-and-tested `stack` build tool, which builds me a project directory with all the required files using this one command:

```bash
$ stack new project-manager
$ cd project-manager
$ stack setup
```

Warning: running `stack setup` for the first time on your computer within a stack project will make it take a while to set up the environment for the project. You might want to go, say, finish off some sewing during this process.

## Reading Files and Directories

I want to read all directories to see if they have a `.git` directory immediately under them, starting from my home directory and going recursively. Submodules, etc. are not read- the program stops to note down the name of a directory if it's a git repository.

### Home Directory

Python has the available-by-default library `os` for tasks like this. So, starting with getting the name of my home directory:

```python
import os

home = os.path.expanduser("~")

# test print
print(home)
```

This prints `/home/ciel` when the python file is run.

Haskell has directory-manipulation functionality in its `System.Directory` library, which I can install within my project environment by adding `directory` to the `build-depends` section of the project cabal file. It'll install when I compile the project.

The library function `getHomeDirectory` runs within the IO monad, as you can see by the type: `getHomeDirectory :: IO FilePath`. I'm running it directly in the `main` function of the Haskell project right now, so:

```haskell
module Main where

import System.Directory

main :: IO ()
main = do
    home <- getHomeDirectory
    -- test print
    putStrLn home
```

I run the entire project using `stack run project-manager`, and get `/home/ciel`.

### All The Directories

Now let's try to recursively read all the directories that branch off from a given directory, and test it by making the program produce a list of said directories.

The pythonic way to do this- note, I'm not using `os.walk` but creating my own recursive function because I need to be able to stop the recursion using my own conditions later:

```python
def abspath_listdir(path):

    contents =  list(map(
                     lambda x: os.path.join(path, x),
                     os.listdir(path)))
    return contents


def list_all_dirs(root):

    contents = abspath_listdir(root)
    subdirs = list(filter(
                   lambda x: os.path.isdir(x),
                   contents))

    if subdirs == []:
        return []
    else:
        subsubdirs = []
        for subdir in subdirs:
            subsubdirs.extend(list_all_dirs(subdir))
        return (subdirs + subsubdirs)
```

Here's the test, using a test directory that I made:

```python
for i in list_all_dirs("/home/ciel/Projects/active/test"):
    print(i)
```

Here's the same thing in Haskell:

```haskell
listAllDirs :: FilePath -> IO ([FilePath])
listAllDirs root = do
    dirContents <- listDirectory root
    subdirs <- filterM doesDirectoryExist (map (\x -> root ++ "/" ++ x) dirContents)
    case subdirs of
        [] -> return []
        otherwise -> do
            rec <- mapM listAllDirs subdirs
            return $ subdirs ++ (concat rec)
```

And to test it:

```haskell
main :: IO ()
main = do
    dirs <- listAllDirs "/home/ciel/Projects/active/test"
    print dirs
```

For honesty's sake, I'll point out that each function took me about half an hour of testing all those individual components- listing all directories within a directory, filtering the list, and in Haskell there was the additional overhead of me being unfamiliar with `mapM` and `filterM`.

Figuring out usage of the monadic list functions was the main time-consumer in Haskell; lack of suitable list functions (leading me to have to chain a lot together and typecast the results to lists) was the main time-consumer in Python.

### Conditional: Stop Recursing If There's A `.git` Inside

I make a separate function to check if there's a `.git` inside the subdirectories of a directory, and add it as a base condition in the recursive portion of my subdirectory-listing functions:

```python
def is_git_repo(dir):

    if ".git" in os.listdir(dir):
        return True
    else:
        return False

def list_all_dirs(root):

    # condition added here
    if is_git_repo(root):
        return []
    ######################

    contents = abspath_listdir(root)
    subdirs = list(filter(
                   lambda x: os.path.isdir(x),
                   contents))

    if subdirs == []:
        return []
    else:
        subsubdirs = []
        for subdir in subdirs:
            subsubdirs.extend(list_all_dirs(subdir))
        return (subdirs + subsubdirs)
```

```haskell
isGitRepo :: FilePath -> IO Bool
isGitRepo dir = do
    dirContents <- listDirectory dir
    return $ ".git" `elem` dirContents


listAllDirs :: FilePath -> IO ([FilePath])
listAllDirs root = do
-- conditional added at the start of the function
-- and encases the entire rest of the function within it
    isItAGitRepo <- isGitRepo root
    case isItAGitRepo of
        True -> return []
        False -> do
            dirContents <- listDirectory root
            subdirs <- filterM
                         doesDirectoryExist
                         (map (\x -> root ++ "/" ++ x) dirContents)
            case subdirs of
                [] -> return []
                otherwise -> do
                    rec <- mapM listAllDirs subdirs
                    return $ subdirs ++ (concat rec)
```

### Now Stop Making It Return All The Subdirectories

I just want to make the program *look* at all the subdirectories, not return them (in preparation of having it return only the repositories).

In haskell, a function has to have a return value. So, if the subdirectory is non-empty, I keep calling the function on all the directories within it, but I make the program return an empty list.

```haskell
            case subdirs of
                [] -> return []
                otherwise -> do
                    rec <- mapM listAllDirs subdirs
                    -- modified
                    return $ []
```

The pythonic way to do this is much simpler- I simply, well, remove the return statement on the last conditional of the program. Since I'm not returning a list now, I also remove the accumulator list:

```python
    else:
        for subdir in subdirs:
            list_all_dirs(subdir)
```

But note- you can't iterate over the result of this program, because it's not returning anything!

### Now Make It Return Only The Repositories

This has two steps- first, make the "is it a git repo?" condition return the filename (in Haskell, make it return the filename as a list):

```haskell
    case isItAGitRepo of
        True -> return [root]
```

Then, modify the last conditional to return an *empty list* plus the result of the recursively called function- because we only want a member to be added to the list when it fits the "is it a git repo" conditional.

```haskell
                otherwise -> do
                    rec <- mapM listAllDirs subdirs
                    return $ [] ++ (concat rec)
```

Note that I'm doing it in Haskell first because the function is type-safe- at each step of recursion, if my function returns anything that's not some sort of list, the compiler will catch that! This fact allows me to test my logic without worrying about returning a `None` or non-list value at some stage of recursion which doesn't concatenate with a list.

Copying the same series of steps into python:

```python
    if is_git_repo(root):
        return [root]
```

We have to modify the recursive appending logic a little- the accumulator list is initialized *within* the last condition to make for some tail recursion:

```python
    else:
        ls = []
        for subdir in subdirs:
            ls.extend(list_all_dirs(subdir))
        return ls
```

Now I simply call each program with the `home` directory as argument, to get a list of all the git repositories on my computer. For reference, I also modify each function name to `listAllRepos` and the snake-case version of that.




