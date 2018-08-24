
# Introduction

This book covers advanced programming concepts using multiple paradigms. It is assumed that you are already comfortable writing and testing large amounts of code.

It is based on a long-running module at Coventry University called Web API Development.

By the end of this book you will:

- Have learned cutting-edge JavaScript skills
- Be capable of developing powerful, complex APIs using NodeJS, a version of JavaScript that runs on the server
- Be able to employ industry-standard skills
- Be able to write complex automated testing suites
- Have mastered the key aspects of version control

So why learn about developing web APIs?

- Businesses rely on Web APIs.
- All modern websites and smartphone apps rely on APIs to communicate.
- JavaScript is the most popular language in the world.
- NodeJS is the most important language for server-side programming.

## Book Structure

The book is divided into three parts. In part (chapters 1-3) one we cover the principles behind the latest JavaScript (ECMA) language. In part two (chapters 4-6) we cover how to design RESTful APIs and how to build these. In the final part we cover a range of advanced topics to help you when you start writing more complex API.

## How to Use This Book

Each chapter is divided into two sections. Part 1 covers the core features which you will absolutely need to know whilst Part 2 covers the more advanced features that you will find useful.

The learning revolves around reading and understanding code then testing your knowledge. Rather than printing out these scripts in their entirety, the book will highlight key concepts and give you a reference to the full code which is available on GitHub.

If you are an experienced programmer feel free to skim over the part 1 content and focus on the advanced stuff in part 2.

If you do not have lots of experience stick with the part 1 content then come back later, when you have more experience and tackle the content in part 2.

### Cloning the Sample Code

In each chapter you will be working on real code samples and these are available through GitHub.

Assuming you are running a *NIX system you can clone the materials. This command will create a `nodejs/` directory in your `Documents/` directory containing all the sample code. The sample code can be found inside the `exercises/` directory where you will find a directory corresponding to each chapter.
```
git clone https://github.coventry.ac.uk/304CEM-1718SEPJAN/TEACHING-MATERIALS.git ~/Documents/nodejs
```
Whilst you could simply download the sample code from GitHub it is recommended that you clone this. The benefits are that as bugs are found and the exercise files improved, you can pull these changes into your local copy of the code.

## Coding Style

One of the features (or problems) with JavaScript is that there are a lot of ways to write code. This book uses the following style:

- no semicolons at the end of lines. This is a much debated point but the author prefers to leave these out for a less-cluttered style.
- tabs, not spaces. Without going into the tabs v spaces flame wars, the author feels that with tabs, the indentation can be customised by the end user through the editor settings.
- single quotes. These have been chosen over double quotes for a cleaner feel to the code.

Obviously if you have your pown preferences you can impose these but ensure you update the _ESLint configuration file_ when you get to chapter 4!

## Your System

All the examples and exercises in this book are based on an Ubuntu-based system however if you are not using this platform you have several choices:

1. You can take the plunge and install Ubuntu (either on its own or dual-book). There are plenty of online tutorials that cover this.
2. You can run Ubuntu as a virtual machine. One option is to use [VirtualBox](https://www.virtualbox.org) which is a free download and available for all major platforms.
3. Use a different flavour of Linux or Unix such as Fedora or MacOS (most of the commands will work just fine).
4. Stick with Windows and work around the issues.

## Installing NodeJS

Let's get started by installing the latest version of NodeJS. Whilst you would need to download and install a separate install for each version from the [NodeJS downloads page](https://nodejs.org/en/download/current/), because we are running a Linux distribution (you did read the previous section didn't you?) we can install the [Node Version Manager](https://github.com/creationix/nvm) and use this to install whatever version of Node we want. Lets fire up the Terminal. We need to use the cURL command to download the installation shell script and pipe it to the `bash` command to run it.
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```
The installation script has added a new path to your shell configuration and this needs to be loaded again. You have two choices, either log out then log in again or reload the shell using `source ~/.bashrc`.

Now we can use this tool to check for the versions of NodeJS available. The latest version (8.0.0 at the time of writing) will be at the end of the list.
```
nvm list-remote
         ...
        v7.8.0
        v7.9.0
        v7.10.0
        v8.0.0
```
Finally we can install the current (latest) version (v8.0.0) at the time of writing.
```
$ nvm install 8.1.0
Downloading and installing node v8.1.0...
Downloading https://nodejs.org/dist/v8.1.0/node-v8.1.0-linux-x64.tar.xz...
######################################################################## 100.0%
Computing checksum with sha256sum
Checksums matched!
Now using node v8.1.0 (npm v5.0.3)
```
Finally we can check which version is installed.
```
$ node -v
  v8.1.0
```

Over time you will update the version of NodeJS and, as a result will end up with multiple versions installed on your computer. You can check which version are currently install using.
```
$ nvm ls
           v5.1.0
  ->       v7.3.0
           v8.1.2
           system
  default -> 8.1.2 (-> v8.1.2)
  node -> stable (-> v8.1.2) (default)
  stable -> 8.1 (-> v8.1.2) (default)
  iojs -> N/A (default)
```
As you can see there are three versions of nodejs installed and the one currently in use is v7.3.0.We can switch to a different version with `nvm use 5.1.0` for example. In the example above we are using v7.3.0 with the default is set to 8.1.2. The default version can be set using.
```
$ nvm alias default 8.1.2
```
If we no longer need one of the installed versions it can be removed.
```
$ nvm uninstall 5.1.0
  Uninstalled node v5.1.0
```
When you update your node version you will also need to update any packages to run under the new version.
```
$ nvm reinstall-packages 8.1.2
```

## Installing a Code Editor

There are plenty of choices when it comes to editors. The examples in this book will use the [Visual Studio Code](https://code.visualstudio.com) editor which is available for all major platforms. If you are looking at other editors then choose one that supports JavaScript and plugins.

Note that you may need to update the configuration database before Visual Studio Code will install.
```
sudo apt-get update && sudo apt-get install libgconf2-4
```

### Launching Visual Studio Code from the Terminal

You will be spending a lot of time in the terminal so here is a useful tip. If you have visual studio code installed you can launch it with the contents of a specified directory.

- `code ~/Documents/bookshop/` opens Visual Studio Code with the contents of the directory specified.
- `code .` opens Visual Studio Code with the contents of the _current directory_.

<<<<<<< HEAD
If you are using Ubuntu this works 'out of the box' but if you are using MacOS you will need to edit your `.bash_profile` file located in your home directory. Since this is hidden by default you can open it in **nano** using `nano ~/.bash_profile`. Now add the following line, save the changes and exit.
```
code () { VSCODE_CWD="$PWD" open -n -b "com.microsoft.VSCode" --args $* ;}
```
Close the terminal and re-launch. Now you can open Visual Studio Code using the same commands as Ubuntu.
=======
### Using a Mac for Development

Computers running MacOS are popular with NodeJS developers. MacOS is a flavour of Unix just like Linux and so all of the commands used in this book will work just fine. The biggest issue is the lack of a decent package manager such as the `apt` package manager found on Ubuntu. To get round this you should install the [Homebrew](https://brew.sh) package manager.
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
>>>>>>> b1b6e3b0242f7c6118d7fe9f5491bd523556dddc
