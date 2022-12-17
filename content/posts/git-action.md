---
title: "Getting started with Github Actions"
date: 2022-12-17
draft: false
---

Github Actions is a CI/CD platform. Full software development life cycle can be managed using github actions.

To understand this tool we will go through an example implementation. All releated concepts will be described along the way. 

## Project Goal  
Implement a github actions workflow that builds an exe from the project code and upload the exe file as a release artifact to the github repository. This workflow runs whenever the git repository owner push a new `tag` to the git repository. 

## Project Structure
We start with a tiny hello world python project.
```
github-action-tutorial/
├─ main.py
├─ README.md
├─ requirements.txt

```
`main.py` python script takes a name as input and print out `hello <name>`. README file has this tutorial written. And `requirments.txt` file is there to list out the python dependencies. All codes and files are [here](https://github.com/kBashar/github-action-tutorial).  


Github Actions has following Ideas, these are discussed serially(as they appear in a workflow file) with code snippet. Full code is added at [the end](#code).

- [Project Goal](#project-goal)
- [Project Structure](#project-structure)
  - [Workflows](#workflows)
  - [Event](#event)
  - [Runner](#runner)
  - [Job](#job)
  - [Code](#code)

### Workflows
* Workflows are *yml* file residing in `.github/workflows` path of a repository. 

* defines operations to run when an event related to the repository is triggered, also it describes the computing platform to execute those operations.

* There can be more than one workflows in a single github repository triggered at different events.

* Example of events, [push, pull]. [All supported events](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows).
    
We will create a workflow file titled `exe-releaser.yml` inside `.github/workflows/`. Now our project structure is as follows:  
```
github-action-tutorial/
├─ .github/
│  ├─ workflows/
│  │  ├─ exe-releaser.yml
├─ main.py
├─ README.md
├─ requirements.txt

``` 
Start the workflow file with giving it a name and a run-name.
```yml
name: learn-github-actions
run-name: ${{ github.actor }} is learning GitHub Actions
```
### Event  
Workflows are triggered to run when a specific event happens in the repository. For example we can run a workflow if there is a push event in the *main* branch. Following yml code would do the job.

```yml
on:
  push:
    branches:
      - 'main'
```
To know more about the available github action events see this [link](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows).

However our project workflow should run whenever a `tag` is pushed. Git has a provision to mark a point in the commit history by adding a tag. We will use `tag` to mark release commits in our project. You can learn more about `git tag` from [here](https://git-scm.com/book/en/v2/Git-Basics-Tagging). 
Our event configuration is as follows

```yml
on:
  push:
    tags:
      - "*"
```   
Now we need to add the jobs. Multiple jobs can run in a single workflow. We will talk about jobs in detail in a bit but before that let's learn about virtual computing platforms on which the jobs run on. These are called `runner` in github actions context. Each workflow gets a runner of its own, a fresh virtual machine. `jobs` segment in the workflow gets to define the `runner` it will run on. 

### Runner
* Github provides runners that run on Windows, Ubuntu(Linux), MacOS.  
* Runners can be self-hosted.

In our case we will use a Windows machine to build the exe.
```yml
    jobs:
      build-release-exe: 
        runs-on: windows-latest
```   
### Job  
* A Job is defined by a series of steps to run  
* Steps are described using *actions* and/or *shell* command  
* Actions are predefiend github actions. Available from github [marketplace](https://github.com/marketplace).   
* A step must have either **uses** or **run** 
    * name[Optional] -- Name of the Step   
    * uses -- link of a action  
    * run -- command to run
  
**Job steps**  
1. Checkout the repository using a github actions  
```yml
    steps:
      - name: Repo-Checkout
        uses: actions/checkout@v3
```
2. Our project is coded in python, it's time to install Python in the windows `runner`. We use the official github action of version 4 ( `actions/setup-python@v4` ) to install Python.  
There is a new key `with` here, this is used to provide Input parameters to the action. In our case there are 3 such input parameters, all of them are optionals and github has ways to resolve the values in case no input is provided. We are writing them to make our build environment predictable and matching with the development enevironment.  

```yml
    - name: Setup Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'
        cache: 'pip'
        architecture: 'x64'
```  
3. Project dependencies are to be isntalled from requirements file. 
```yml
    - name: Install Requirement
      run: pip install -r requirements.txt
```  
4. Now on to build the exe using [PyInstaller](https://pyinstaller.org/en/stable/). 
```yml
    - name: Build exe
      run: PyInstaller --onefile main.py
```
5. Now that our exe is ready to release. We create a release in Github and upload the exe as an artifact of the release. Till now we have used `actions` provided by Github. This time we will use a community made actions, [ncipollo/release-action](https://github.com/ncipollo/release-action).
Here build artifact is the exe generated in previous step. This action will add latest pushed tag to the release. 
```yml
    - name: Create Release
      uses: ncipollo/release-action@v1
      with:
        artifacts: 'dist/main.exe'
        artifactContentType: application/zip
        removeArtifacts: true
``` 
### Code

Complete workflow
```yml
name: learn-github-actions
run-name: ${{ github.actor }} is learning GitHub Actions
on:
  push:
    tags:
      - "*"
jobs:
  build-release-exe:
    runs-on: windows-latest
    steps:
      - name: Repo Checkout
        uses: actions/checkout@v3
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.10"
          cache: "pip"
          architecture: "x64"
      - name: Install Requirements
        run: pip install -r requirements.txt
      - name: Build exe
        run: PyInstaller --onefile main.py
      - name: Create Release
        uses: ncipollo/release-action@v1
        with:
          artifacts: "dist/main.exe"
          artifactContentType: application/zip
          removeArtifacts: true
```
Let's see if this work
**Thanks For Reading.**
