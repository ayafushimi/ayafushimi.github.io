---
layout: post
title:  "How to customize Terminal's prompt"
date:   2016-10-11 21:33:30 +0000
---

In this article:
* What is prompt?
* How to change text
* How to change color
* How to save these changes
* How to show GitHub branch


PC environment: mac OS X El Capitan Version 10.11.6

Terminal's shell: bash

## What is prompt?
A prompt is text or symbols which is displayed in front of input area in terminal.

![](https://www.evernote.com/shard/s51/sh/d7ac0445-c026-4426-94f8-01a31677c0ad/866b59398d31ffff/res/37f78276-cda6-4ef1-983e-b36d62c095ec/skitch.png)


This article's goal is: 

![](https://www.evernote.com/shard/s51/sh/917b9dc1-8266-4c1d-a8e0-25bf083a590c/e9d88254f3304d24/res/b4c7e807-1479-4616-817d-ae94f48d63c7/skitch.png)

## How to change text
First, let's check what's the text now.

In Terminal, input "set" to display prompt setting.
```
$ set
```
In the response, there should be 'PS1='.

```
...
PROMPT_COMMAND=update_terminal_cwd
PS1='\h:\W \u\$ '
PS2='> '
PS4='+ '
PWD=/Users/Guest
...
```

The string '\h:\W \u\$ ' is the value that is set as prompt now.

You can customize this string by using any characters, symbols and the following escape sequences.


Escape Sequences:

|value|meaning|
| -------- | -------- |
| \d | Current date |
| \h | Host name up to the first ‘.’|
| \H | Host name |
| \n | New line |
| \t | Current time in 24 hour HH:MM:SS format |
| \T | Current time in 12 hour  HH:MM:SS format |
| \@ | Current time in am/pm format |
| \A | Current time in 24-hour HH:MM format |
| \u | User name |
| \w | Current working directory with full path |
| \W | Current working directory |
| \\# | Command number |
| \\\ | Backslash |
| \\[ | begin a sequence of non-printing characters |
| \\] | end a sequence of non-printing characters |

For example, if you'd like prompt to be like the following,

```
[15:34:42] user_name -- current_directory 
// ♥
```

you can achieve this by inputting the line below.

```
PS1='[\t] \u -- \W\n\\\[\]\\ ♥ '
```

![](https://www.evernote.com/shard/s51/sh/41a76709-e3d6-448a-af8d-acf5e1dc1293/aeeb31ff487bc3e2/res/81d3daaf-30fa-4303-a8f4-f30a0a1ecdc4/skitch.png)


## How to change color

You can customize not only text but color.

Let's take a look at an example command to change text to underlined, cyan colored with blue background.

```
PS1='\[\e[4;36;44m\]underlined cyan text on blue background\[\e[0m\] regular text'
```

![](https://www.evernote.com/shard/s51/sh/77755b46-5030-4445-a174-62ff9070e592/bb1aeb2d0be3e942/res/067f4476-36f4-4004-973a-32ef50c432ad/skitch.png)

The beginning of the command '\[\e[4;36;44m\]' means that the following text should be displayed in "underlined, cyan text color and blue background color". And the code '\[\e[0m\]' in front of the string 'regular text' means that the following text need not to be customized.


'\[\e[4;36;44m\]' is a regular pattern code for color customize.

The first digit '4' is formatting code:

| code | text formatting |
| -------- | -------- |
| 1 | Bold |
| 2 | Dim |
| 4 | Underlined |
| 5 | Blink |
| 7 | Reverse (invert the foreground and background colors) |
| 8 | Hidden |
| 0 | Clear all settings |

The second digit '36' is text color code:

| code | text color |
| -------- | -------- |
| 39 | Default foreground color |
| 30 | Black |
| 31 | Red |
| 32 | Green |
| 33 | Yellow |
| 34 | Blue |
| 35 | Magenta |
| 36 | Cyan |
| 37 | Light gray |
| 90 | Dark gray |
| 91 | Light red |
| 92 | Light green |
| 93 | Light yellow |
| 94 | Light blue |
| 95 | Light magenta |
| 96 | Light cyan |
| 97 | White |


The third digit '44' is background color code:

| code | background color |
| -------- | -------- |
| 49 | Default background color |
| 40 | Black |
| 41 | Red |
| 42 | Green |
| 43 | Yellow |
| 44 | Blue |
| 45 | Magenta |
| 46 | Cyan |
| 47 | Light gray |
| 100 | Dark gray |
| 101 | Light red |
| 102 | Light green |
| 103 | Light yellow |
| 104 | Light blue |
| 105 | Light magenta |
| 106 | Light cyan |
| 107 | White |



If you'd like prompt to be like the following,

![](https://www.evernote.com/shard/s51/sh/1f06fdc1-c253-414f-b98b-deadecda3fb9/6aca6635e8739dbb/res/6ef56d24-6529-4c4a-b6a8-fa12f5d76c3a/skitch.png)

you can achieve this by inputting the line below.

```
PS1='[\[\e[1;97;104m\]\t\[\e[0m\]] \[\e[0;31m\]\u\[\e[0m\] -- \[\e[0;32m\]\W\[\e[0m\]\n\[\e[0;94m\]\\\[\]\\ \[\e[0;31m\]♥ \[\e[0m\]'
```


## How to save these changes
You can save the above settings in ".bashrc" file.
If you haven't used this file, you need to create this.

To open or create this file, input the code below.
```
vi .bashrc
```

This command opens vi editor.

>  How to use vi editor
>  
>  (in English) https://www.cs.colostate.edu/helpdocs/vi.html
>  
> (in Japanese) http://net-newbie.com/linux/commands/vi.html

In the '.bashrc' file, add your 'PS1=...' line, and exit vi editor.
![](https://www.evernote.com/shard/s51/sh/2be1ec8b-60fe-4fc7-bcef-2b229d5678b9/0207ee4901b1b730/res/361af2a4-c2f4-42cb-ac79-f34ed47d3c0f/skitch.png)

To adopt this change, input the following code.

```
source .bashrc
```

Now, your prompt is saved! Next time you open Terminal, the prompt is displayed as you changed.
![](https://www.evernote.com/shard/s51/sh/fd1ebc6c-877b-49a9-984c-607ff3348637/87d66a7d32ead383/res/84047ddc-b02a-4022-b7a9-d52d58c3b9ce/skitch.png)


## How to show GitHub branch

In addition to the above changes, you can also add GitHub branch to prompt.

The function to get current GitHub branch is:

```
git_branch() {
  echo $(git branch --no-color 2>/dev/null | sed -ne "s/^\* \(.*\)$/(\1)/p")
}
```

Adding this function to '.bashrc', you can use "$(git_branch)" in your PS1 to display GitHub branch.


To reach this article's goal, edit '.bashrc' like this:
​
```
git_branch() {
  echo $(git branch --no-color 2>/dev/null | sed -ne "s/^\* \(.*\)$/(\1)/p")
}
PS1='[\[\e[1;97;104m\]\t\[\e[0m\]] \[\e[0;31m\]$(git_branch)\[\e[0;32m\]\W\[\e[0m\]\n\[\e[0;94m\]\\\[\]\\ \[\e[0;31m\]♥ \[\e[0m\]'
```

Then, my Terminal is now what I want.
​
![](https://www.evernote.com/shard/s51/sh/bc812d10-8074-4977-ac9e-9c3e064a93ab/f5db96d17baf8098/res/66f6f22d-0146-4551-bfce-354eaa59fa7d/skitch.png)
​

