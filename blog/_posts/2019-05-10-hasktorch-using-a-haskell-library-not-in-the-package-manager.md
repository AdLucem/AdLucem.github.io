---
layout: post
title: Hasktorch- Using a Haskell Library Without The Package Manager
date: 2019-05-10
category: blog
cssfile: post.css
published: true
---

Hasktorch is a library for tensors and neural networks in Haskell. It is, essentially, Haskell's pure functional answer to PyTorch. Since the library is in active development, the latest work on the library is not on Hackage and cannot be installed using `cabal-install`, so all work/examples using the library can be done in one of two ways:

* Within Hasktorch's `examples` folder, that's built using the top-level `cabal.project` file.
* By using a top-level `cabal.project` file, and building Hasktorch's various libraries as a sub-project of your project.

The above, however, requires a bit of work- because building Hasktorch at this stage requires a bit of work- so here's a little script- and cabal.project file- that I made to install Hasktorch without the package manager.

## `cabal.project` File

Add the following lines to your Haskell project's top-level `cabal.project` file, under the `packages:` heading:

```
	hasktorch/ffi/codegen/*.cabal
   
    hasktorch/ffi/types/th/*.cabal
    hasktorch/ffi/types/thc/*.cabal
  
    hasktorch/ffi/ffi/tests/*.cabal
    hasktorch/ffi/ffi/th/*.cabal
    hasktorch/ffi/ffi/thc/*.cabal
  
    hasktorch/signatures/types/*cabal
    hasktorch/signatures/partial/*cabal
    hasktorch/signatures/support/*cabal
    hasktorch/signatures/*cabal
  
    hasktorch/indef/*cabal
  
    hasktorch/hasktorch/*cabal
  
    hasktorch/zoo/*cabal
    
	hasktorch/examples/*.cabal
```

## Installing Hasktorch In Your Project: The Script

Run this bash script from your top-level directory- the one where your aforementioned `cabal.project` file is.

```
#!/bin/bash

git clone https://github.com/AdLucem/hasktorch.git
cd hasktorch/
git submodule update --init --recursive
cd ffi/deps/aten
mkdir build 
cd build/
cmake .. -DNO_CUDA=true -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ -DCMAKE_CC_COMPILER=gcc -DCXX=g++ -DCC=gcc -Wno-dev -DCMAKE_INSTALL_PREFIX=.
make install
cd ../../../../..
ln -fs hasktorch/cabal/project.freeze-8.4.2 cabal.project.freeze
./make_cabal_local.sh
```

And to link the source library (Aten) to the executable, execute the following commands:

```bash
$ cd hasktorch
$ source setenv
```

And your project should be able to import Hasktorch from the top-level project directory! For an example of a project that uses Hasktorch, you can see <a href="">my fashion-mnist example.</a> There are more examples in the `hasktorch/examples` folder, but this is, of course, a shameless self-plug.

