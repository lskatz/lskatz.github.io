---

layout: post
title:  "How to be a good citizen on Linux"
date:   2023-03-21 9:31:11 -0400
categories: linux

---

This is a short tutorial on how to be a good citizen in a shared Linux environment.
At my own institution, there are many users per computer and we all have to connect and play nicely with each other.
So how do we do that?
The main resources we are monitoring are CPU, Disk bandwidith ("I/O"), and RAM ("memory").
Then after figuring out what we need to regulate, how do we do that?

# Monitoring

First, we have to understand how to know if we are taking up too many resources.

## CPU

There are quite a few commands to see how much CPU you are taking.
I'll just choose a couple here.
My favorite is `htop` which shows the load per processor
and an interactive menu of each process with child/parent relatedness.
To view just your own processes, run `htop -u $USER`, where `$USER` evaluates to your username.
You can also run `htop` with no arguments and if so, it will show you all processes.

![htop example](/images/htop.png)

The command `top` is also available. 
It is more simple but it is available on most Linux installations, where `htop` might not be available.
You can also run it the same way with `top -u $USER`.

![top example](/images/top.png)

Most of your processes will be single threaded, meaning that it will only take up to 100% of one CPU.
You might be surprised if you are taking up many CPUs 
and if so, there are solutions later in this document.

## Disk IO

## Disk space

## RAM ("memory")

# Solutions

# In practice

# Workshop


