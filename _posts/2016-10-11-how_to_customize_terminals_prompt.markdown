---
layout: post
title:  "How to customize Terminal's prompt"
date:   2016-10-11 17:33:30 -0400
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

![](http://i.imgur.com/lHYZp1I.png)


This article's goal is: 

![](http://i.imgur.com/eLkJU5E.png)

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

![](http://i.imgur.com/RnqVefG.png)


## How to change color

You can customize not only text but color.

Let's take a look at an example command to change text to underlined, cyan colored with blue background.

```
PS1='\[\e[4;36;44m\]underlined cyan text on blue background\[\e[0m\] regular text'
```

![](http://i.imgur.com/2wVMWmc.png)

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

![](http://i.imgur.com/7Ype7Sv.png)

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

![](http://i.imgur.com/HbpUcsJ.png)

To adopt this change, input the following code.

```
source .bashrc
```

Now, your prompt is saved! Next time you open Terminal, the prompt is displayed as you changed.
![](http://i.imgur.com/C1WQ1dQ.png)


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

```
git_branch() {
  echo $(git branch --no-color 2>/dev/null | sed -ne "s/^\* \(.*\)$/(\1)/p")
}
PS1='[\[\e[1;97;104m\]\t\[\e[0m\]] \[\e[0;31m\]$(git_branch)\[\e[0;32m\]\W\[\e[0m\]\n\[\e[0;94m\]\\\[\]\\ \[\e[0;31m\]♥ \[\e[0m\]'
```

Then, my Terminal is now what I want.

![](http://i.imgur.com/zTUVzNY.png)

