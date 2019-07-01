---
layout: post
title: Project Manager Part 2: Printing In Haskell
date: 2019-06-09
category: blog
published: true
---

Continuing from **Project Manager Part 1**- this time purely in Haskell-

## Tell Me Which Account Each Is From

The account name resides in the file `repository_name/.git/config`. So for each repository, I construct the path `<repository_name>/.git/config`, grep the line with the word `url`, and parse the given url of the form "https://<service>.com/<accountName>/<repo name>.git".

### Parsing The String

Haskell has the `split` library that has to be installed.

```haskell
import Data.List.Split

main :: IO ()
main = do
    let url = "https://github.com/AdLucem/project-manager"
    print $ splitOn "/" url
```

Python features simple string manipulation functions as default.

```python
url = "https://github.com/AdLucem/project-manager"
parts = url.split("/")
print(parts)
```

The third element of each list is `<service>.com`. Use a further split on it and take the first element and we have the name of the service. This bit is basically the same in both languages.

### Reading The `.git/config` File In Each Repository

```haskell
getConfigs :: FilePath -> IO (String)
getConfigs reponame =
    let
        filename = reponame ++ "/.git/config"
    in
      do
        contents <- readFile filename
        return contents
```

Dividing it into lines with `lines contents`, and then removing the tabs from each line- returning an empty string where the line does not start with tabs, and then filtering it to find the line with `url = "..."`


```haskell
coerceMaybe :: Maybe String -> String
coerceMaybe value = case value of
    Nothing -> ""
    Just x  -> x

getURL :: String -> String
getURL content = head $ filter (isSubsequenceOf "url =") cleanLines
    where
      cleanLines = map (coerceMaybe . stripPrefix "\t") (lines content)
```

Putting everything together:

```haskell
findServiceName :: FilePath -> IO String
findServiceName repo = do
    configs <- getConfigs repo
    return $ getService $ getURL configs
```

Similar functions to find the name of the repository and the name of the user for each url.

And displaying it in a neat format using the `Text.Printf` library- that allows us to use C's `printf` string formatting:

```haskell
import Text.Printf

showAsTable :: [String] -> [String] -> [String] -> String
showAsTable repos services usernames =
    join $ zipWith3 showCell repos services usernames
    where
      showCell x y z = printf "%-50s %-9s %s\n" x y z

main :: IO ()
main = do
    home <- getHomeDirectory
    dirs <- listAllRepos home
    repos <- mapM findRepoName dirs
    users <- mapM findUserName dirs
    serv <- mapM findServiceName dirs
    putStrLn $ showAsTable repos serv users
```
