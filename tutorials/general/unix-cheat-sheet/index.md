---
layout: tutorials
title: Unix Cheat Sheet
author: naomi 
comments: true
date: 2016-07-25
---

## Objectives

After you complete the [Command Line course on Code Academy](https://www.codecademy.com/courses/learn-the-command-line), you should be able to:

1. Navigate directories and files from the command line
2. Copy, move, and delete files and directories from the command line
3. Redirect input and output to and from files and programs
4. Configure the environment using the command line

Below are just some of the commands you need to know for this course. For a full introduction, go to https://www.codecademy.com/courses/learn-the-command-line and complete the course.

## Basic Terminal Commands

| Command |  |
|:--------|:--------|
| CTRL L  | Clear the terminal |
| CTRL D  | Logout |
| SHIFT Page Up/Down | Go up/down the terminal |
| CTRL A | Cursor to start of line |
| CTRL E | Cursor the end of line |
| CTRL U | Delete left of the cursor |
| CTRL K | Delete right of the cursor |
| CTRL W | Delete word on the left |
| CTRL Y | Paste (after CTRL U,K or W) |
| TAB | auto completion of file or command |
| CTRL R | reverse search history |
| `!!` | repeat last command |
{: rules="groups"}

## Help on any Unix Command

| Command |  |
|:--------|:-------|
| `man {command}` | Type man ls to read the manual for the ls command. |
| `man {command} > {fileName}` | Redirect help to a file to download. |
{: rules="groups"}

## List a Directory

| Command |  |
|:--------|:-------|
| `ls {path}` |  |
| `ls {path_1} {path_2}` | List both {path\_1} and {path\_2}. |
| `ls -l {path}` | Long listing, with date, size and permissions. |
| `ls -a {path}` | Show all files, including important .dot files that don't otherwise show. |
| `ls -F {path}` | Show type of each file. "/" = directory, "*" = executable. |
| `ls -R {path}` | Recursive listing, with all subdirs. |
| `ls {path} > {filename}` | Redirect directory to a file. |
{: rules="groups"}

## Change to Directory

| Command |  |
|:--------|:--------|
| `cd {dirname}` | Go to directory. |
| `cd ~` | Go back to home directory, useful if you're lost. |
| `cd ..` | Go back one directory. |
{: rules="groups"}

## Make New Directory

| Command |  |
|:--------|:--------|
| `mkdir {dirname}` | Make a new directory with name. |
{: rules="groups"}

## Remove Directory

| Command |  |
|:--------|:--------|
| `rmdir {dirname}` | Only works if {dirname} is empty. |
| `rm -r {dirname}` | Remove all files and subdirs. Careful! |
{: rules="groups"}

## Print Working Directory

| Command |  |
|:--------|:--------|
| `pwd` | Show where you are as full path. Useful if you're lost or exploring. |
{: rules="groups"}

## Copy a File or Directory

| Command |  |
|:--------|:--------|
| `cp {file1} {file2}	` | 
| `cp -r {dir1} {dir2}` | Recursive, copy directory and all subdirs. |
| `cat {newfile} >> {oldfile}` | Append newfile to end of oldfile. |
{: rules="groups"}

## Move / Rename a File or Directory

| Command |  |
|:--------|:--------|
| `mv {oldfile} {newfile}` | Moving a file and renaming it are the same thing. |
{: rules="groups"}

## View a Text File

| Command |  |
|:--------|:--------|
| `cat {filename}` | View file, but it scrolls. |
{: rules="groups"}

## Edit a Text File

| Command |  |
|:--------|:--------|
| `vi {filename}` | Basic text editor. |
{: rules="groups"}