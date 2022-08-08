---
title: "Quick Introduction to the Command Line"
teaching: 30
exercises: 5
questions:
- "What is the command line?"
- "How can I navigate files and directories on the command line?"
---

<center>
<div id="adobe-dc-view" style="width: 1000px; height: 565px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
	document.addEventListener("adobe_dc_view_sdk.ready", function(){ 
		var adobeDCView = new AdobeDC.View({clientId: "f00519a3790840b6bb21ad58eab57ecf", divId: "adobe-dc-view"});
		adobeDCView.previewFile({
			content:{location: {url: "https://msse-2022-bootcamp.github.io/lessons/files/day-1-topics.pdf"}},
			metaData:{fileName: "day-1-topics.pdf"}
		}, {embedMode: "SIZED_CONTAINER"});
	});
</script>
 </center><br><br>

In this course, we will be navigating files and using git using the command line. 
The Linux command line is a text interface to your computer. 
When you use the command line, you use something called a shell.
You can access the command line, or shell, using a *terminal*. 
If you are using WSL, the only type of interface you have to your Linux operating system is a command line, or terminal.

In scientific computing, you will need to use the Linux command line on high performance computing (HPC) servers.
Knowing the command line will also allow you to perform repetitive tasks quickly through shell scripting.
You will learn more about the command line and scripting in Chem 274 - Introduction to Programming Languages.
For this course, we will focus on basic navigation, file creation, and using git from the command line.

Most modern operating systems have graphical user interfaces, or GUIs (often pronounced "gooey"), that are used to interact with the computer.
However, you can also interact with the computer using text only.

[Open your terminal](https://towardsdatascience.com/a-quick-guide-to-using-command-line-terminal-96815b97b955). 
On Mac, you should be able to find a terminal application.
If you are using WSL, open your Linux distribution.
You can use this interface to issue commands to your computer using text.

## Viewing Directory Contents

The first command we will discuss is the command `pwd`. 
`pwd` stands for "**p**rint **w**orking **d**irectory." 
This command gives the name of the folder you are currently in.
In Linux, "directory" means the same thing as "folder".

Note that you should not type the dollar sign `$` at the beginning of the command.
It is shown with the command because the dollar sign `$` is often used to indicate the beginning of the command prompt.

~~~
$ pwd
~~~
{: .language-bash}

~~~
/YOUR/PATH
~~~
{: .output}

When you open a terminal initially, you will be in your home directory. 
The path displayed as your output will be whatever your home directory is if you type `pwd` immediately after opening your terrminal.

The `ls` command shows you the contents of the directory you are in. 
`ls` stands for "list", and the command shows you the contents of the directory you are in.

~~~
$ ls
~~~
{: .language-bash}

If you want to see contents of another directory, you can follow `ls` with the path to that directory. 
In the command below, you should substitute a directory you can see from the previous `ls` command.

~~~
$ ls DIRECTORY_NAME
~~~
{: .language-bash}

> ## Clearing the screen
> If you'd like to make room on your screen, you can use the `clear` command too get a fresh terminal.
> Pressing `ctrl+L` on your keyboard will also clear the screen.
{: .callout}

## Creating and navigating directories
We will make a directory to keep our work in for the course.
For the sake of uniformity, these directions will tell you how to create a folder in your home directory.
If you have another preference for where you would like to store your files and you are able to navigate files, you can use that location.

The command to **m**a**k**e a **dir**ectory is `mkdir`.

~~~
$ mkdir chem_280
~~~
{: .language-bash}

> ## Check Your Understanding
> What command that you have learned so far could you use to see that your newly created folder is in your current location?
> 
>> ## Solution
>> You could use the `ls` command to confirm that there is now a  folder called `chem_280` in your home directory.
> {: .solution}
{: .challenge}

This has created an empty folder, or directory, named `chem_280` in your home directory.
To navigate to be inside of that directory, we need to **c**hange the **d**irectory we are in using the `cd` command.

> ## Spaces in file and directory names
> In general, you will notice that most file and directory names created on systems where the command line is used do not contain spaces.
>
> On the command line, many applications and scripts may not work with file names or directories containing spaces. 
> It is best to use underscores `_` or dashes `-` to separate words in file names.
{: .callout}

~~~
$ cd chem_280
~~~
{: .language-bash}

We will create a file that has a description of what is in this folder. 

You can verify what folder you are in using the `pwd` command.

~~~
$ pwd
~~~
{: .language-bash}

Open a text file in your text editor of choice. 
If you installed [VSCode](https://code.visualstudio.com/download)  you will be able to open a file called `README.md` using VSCode with the following command. **Note** If you are on MacOS it will be necessary to [add VSCode to your path manually](https://code.visualstudio.com/docs/setup/mac).

~~~
$ code README.md
~~~
{: .language-bash}

The Visual Studio Code text editor will open with a file called `README.md`. 
Type some information in the file and save it.

~~~
# Chem 280
This folder contains files and directories associated with Chem 280 - Foundations of Programming and Software Engineering.
~~~
{: .language-markdown}

This file uses something called [Markdown](https://www.markdownguide.org/), which a mark-up, or text formatting language.
This is often used for README files, and we will use `Markdown` throughout the bootcamp. 
The hashtag (`#`) followed by the space results indicates a title.
Save this file and exit.

Now, when you type `ls`, you will see that you have a file called `README.md` in your directory.

To navigate out of this folder, you can use `..` as the file location. 

~~~
$ cd ..
~~~
{: .language-bash}

This will move you back to your home directory. 

> ## Changing to the home directory from anywhere
> If you use the `cd` command followed by no path, you will always be returned to your home directory.
{: .callout}


Other commands which will be useful to you are `mv` which is used for moving files from one place to another, and `cp` for copying files from one place to another. For example, you can create a copy of your README.md file:

~~~
$ cp chem_280/README.md .
~~~
{: .language-bash}

This command will create a copy of the README.md file in your current directory. 
The dot (`.`) is a short-cut for your current directory. 
In this case, the created file will have the same name as the one you copied it from.
You could also give the file another name:

~~~
$ cp chem_280/README.md README_copy.md
~~~
{: .language-bash}

The `mv` command behaves the same way except that the original file is removed.

You can remove a file using the `rm` command.
Let's get rid of those copies we just made:

~~~
$ rm README.md
$ rm README_copy.md
~~~
{: .language-bash}


> ## Challenge
> Navigate to your `chem_280` directory and list the directory contents.
>> ## Solution 
>> The commands you should execute are 
>> ~~~
>> $ cd chem_280
>> ~~~
>> {: .language-bash}
>> The command above will change your working directory to your `chem_280` directory.  
>> Next, list the directory contents using the `ls` command:
>> ~~~
>> $ ls
>> ~~~
>> {: .language-bash}
> {: .solution}
{: .challenge}