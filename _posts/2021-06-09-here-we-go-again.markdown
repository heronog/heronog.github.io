---
layout: post
title:  "Here we go again"
date:   2021-06-09 04:01:07 +0000
categories: jekyll update
excerpt: How to publish the simplest Jekyll blog using GitHub pages
---
It's been a long time since I've written anything for myself, and thanks to the support
from some new friends I'm finally working on documenting my thoughts again.

As a web developer with 18 (!!!!) years of experience, I have been too ambitious in my previous iterations
at writing a blog, I would get burned out just by the setup, architecture and maintenance of the site, and
just abandon the project as if it was 'too much work' or 'not for me'.

But not any more, I'm embracing a much more GTD approach and focusing on the writing instead of all the
fun stuff around setting up a website, so I'll be using [Jekyll][jekyll-organization] and [Github Pages][github-pages] to
create a simple place where I can upload my articles.

## Approach

I have a Mac and I prefer the ease of use and general UI for my personal use, but I prefer to develop using Linux.
The way I've normally addressed this is by using [Docker](https://docker.com) to spin up a VM with a Linux OS inside.

There's a couple reasons why I prefer this, but mainly it comes down to 2:
* Installing custom CLI software in a Mac is messy
* Using docker forces some good practices around communicating the environment that software runs on, as well as helping
  maintenance and deployability.

## Preparation

We need a few things before we can have GitHub host our website.

* _Docker desktop_ [Install Docker from here](https://www.docker.com/products/docker-desktop)
* _GitHub pages repository_
  They have a short, [easy to follow tutorial][github-pages] in their main site.
* _Visual Studio Code_
  This is my [new favorite text editor](https://code.visualstudio.com).
* _Remote: Containers plugin for VSCode_
  This is where the magic begins. The plugin allows us to generate, manage and run a development environment
  with docker, and we have many pre-built templates available already. All withing VSCode, also it allows us to
  also share this setup with our team. There are many other ways to handle Docker setup but I like this one for
  quick projects.
  
  Follow the [Developing inside a Container](https://code.visualstudio.com/docs/remote/containers) tutorial
  on the VSCode website

## Setting up the local env

Now that we have installed the software we need, you should have a _username_.github.io website ready, even if it only
says "Hello World". If not, follow the instructions on the [GitHub pages][github-pages] tutorial and come back here.

+ Run VSCode and open the folder where your code is.
+ Click on the lower-left corner of the VSCode window on the green button. This will open a drop down menu at the top
  of the window

  ![VSCode remote button](/assets/herewegoagain/greenbutton.png)
  
+ Click on "Open folder in container" but since you don't have a container set up yet, it will enter the Container setup  menu.
+ VSCode will present some options of common Docker container environments, but not one for Jekyll. Click on "Show all definitions" and search
  for _Jekyll_
+ Once you select the _Jekyll_ option it will take a bit to download and prepare the environment for you.
  A text block/area may open with some more information at the bottom of the VSCode interface. but mostly this
  is all automated. Once all is done, the text window will ask you to press Enter. Press Enter.
+ press Ctrl-` or go to View -> Terminal in the top menu. A text terminal will appear at the bottom of the VSCode UI.
+ Type in the following command to initialize the _Jekyll_ project
  ```console
    jekyll new .
  ```
  If this throws an error, you may need to add the _--force_ flag
+ Commit your changes at this point. You should have a few files and folders for Jekyll as well as a _.devcontainer_ and _.vscode_ folders
+ Push your changes to GitHub and verify that they are being reflected on your _username_.github.io site.

## Running Jekyll and the development loop

Once your environment is set up, we can start focusing on the main task of writing a blog.
The main cycle consists on opening up VSCode, running Jekyll inside the container, and making changes to the post
until it's ready to be published.

+ Run VSCode and open the folder with the blog source code (if it was not open from previous steps)
+ If the container is not running, VSCode will promt you and ask permission to start the container environment
  make sure you are running Docker (I usually close the Docker service when I'm not actually doing development)
+ Open a terminal window with Ctrl-` or View -> Terminal in the top menu
+ Run Jekyll in serve and watch mode. This will serve the pages as well as keep checking for any changes to the source code.
  When a source code is detected, Jekyll will rebuild the static website and start serving the updated version.
  This is necessary so you can see your changes in your browser.
  ```console
    jekyll server -w
  ```
+ Open a browser and go to [http://localhost:4000](http://localhost:4000)
+ Add or edit a file  in the _\_posts_ folder. Follow the [Jekyll posts documentation](https://jekyllrb.com/docs/posts/)

As you keep making changes, refer back to your browser and reload the post/pages you are working on.
When a post is ready, push your changes to the repo in GitHub and your online _username_.github.io page should update.

## Repeat

Keep doing it every week!

## Pro tips and notes

* the _\_drafts_ folder can hold WIP posts that are not ready to publish
* Create a nice README.md file for the main Repository page

[jekyll-organization]: https://github.com/jekyll
[github-pages]: https://pages.github.com