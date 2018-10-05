---
layout: post
title: "Setup todo.txt CLI on OSX with Homebrew and Dropbox"
date: 2012-09-20 11:58
comments: true
categories:
---

I have recently re-discovered [todo.txt](http://todotxt.com) the handy plain text todo list, along with its CLI.<!-- more --> I have fallen in love with this method for keeping todo lists, its taggable and has search capabilities too! One of the biggest pain points for me getting started was installing the CLI and hooking it up to Dropbox for cross platform sharing. The todo.txt docs dont provide much of a step by step getting started for OSX with homebrew, they merely say go install with homebrew and then you have to digg though the Tips and Tricks to find what different params to setup to customize your experience.

This article will walk you through setting up [todo.txt](http://todotxt.com) CLI on OSX with Homebrew and storing your todo.txt files in Dropbox for use across multiple platforms.
<!-- more -->

<span class="label label-info">Heads up!</span> make sure to change any `<your username>` to your OSX username

1. Install todo.txt CLI with homebrew using this command

    `$ brew install todo-txt`

2. Create a folder in your Dropbox folder called “todo”
3. Create the following files in your new folder in Dropbox folder

    * todo.txt
    * todo.txt.bak
    * report.txt
    * done.txt
    <br><br>

    Or from the command line you could `cd` into your new folder and run this command instead

    `$ touch todo.txt todo.txt.bak report.txt done.txt`

4. Make a copy of the todo.cfg in your new Dropbox folder

    `$ cp /usr/local/Cellar/todo-txt/2.9/todo.cfg $HOME/Dropbox/todo`

5. Update your new todo.cfg file to reflect the location of your todo directory. This should be on line 4 change `/Users/gina/Documents/todo` to `$HOME/Dropbox/todo`

6. Update your `~/.bash_profile` with these lines

    `source /usr/local/Cellar/todo-txt/2.9/etc/bash_completion.d/todo_completion
complete -F _todo t`

    `alias t='/usr/local/Cellar/todo-txt/2.9/bin/todo.sh -d $HOME/Dropbox/todo/todo.cfg'`

    This will give you the shortcut of `t` so you dont have to type todo.sh every time. It will also add tab complete to for t as well.

7. Restart your terminal and you should now be able to add tasks using

    `$ t add “My first task”`

For more info about todo.txt see <http://todotxt.com>
