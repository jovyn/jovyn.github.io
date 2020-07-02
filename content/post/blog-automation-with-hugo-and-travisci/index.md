---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Automating Website Builds & Deployments with Hugo + Travis-CI"
subtitle: "A blog about how this website is built, deployed and hosted using Hugo, Travis-CI and GitHub Pages."
summary: ""
authors: []
tags: []
categories: []
date: 2020-07-03T01:50:58+05:30
lastmod: 2020-07-03T01:50:58+05:30
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

_Inspiration_ - I was happily living in the past with Blogger until I spoke to [Anant](https://anantshri.info/) who asked me *"How are you going to preach about automation and DevSecOps if you do not automate your own work ?"* That made a lot of sense and he pointed me towards Hugo/Jekyll for static site generation and Travis CI or Netlify for continuous integration. 

[Rohit Salecha](https://www.rohitsalecha.com/#about) has an excellent blog on using [Hugo + Academic with Netlify](https://www.rohitsalecha.com/post/create_a_personal_blog_using_hugo_academic_netlify/) to achieve the same.

After exploring the above options I chose to use Hugo with [Academic theme](https://themes.gohugo.io/academic/) to generate the static site and use Travis CI to build and push the static content to my gitpages repo. I found an excellent [blog by Justin Ellis](https://jellis18.github.io/post/2017-12-03-continuous-integration-hugo/) that really helped me clear out some of the confusions I had regarding TravisCI integration. 

Let me break it down - my set up can be broadly divided to 3 parts:
1. Hugo + Academic = To generate a really awesome static website (like the one you are currently viewing)
2. Github Pages = A repository to host the static web pages. 
				  This repo will have 2 branches i.e  First a 'dev' branch: where we commit all the binaries and files required by Hugo for building the production site. Second branch is a 'master' branch where the production static files are pushed by Travis CI.
3. Travis CI = To automatically pull code from the dev branch, build the static website and push the production files back to the 'master' branch.

You can download Hugo from https://gohugo.io/ and follow their[Quickstart Guide](https://gohugo.io/getting-started/quick-start/) to install and learn how to use Hugo. I have a small "how-to" article for [installing Hugo on Windows](https://hacktheripper.blogspot.com/2020/06/installing-hugo-in-windows.html)
Hugo provides you with a multitude of [themes](https://themes.gohugo.io/) and I chose Academic as I found it very comprehensive. Check out [Academic  docs](https://sourcethemes.com/academic/docs/install/) to get started. 
Alternatively you may proceed to use any other theme that best suites your needs as the rest of the procedure reamins the same.

Create a new repository (preferably *username.github.io*) to host the static files of our website. 
Build the static website and then create a local git repo of the same. Checkout to a 'dev' (or any other non-master) branch and add all the files in the site folder to staging. Set the origin of the local git to the remote repo *username.github.io* and commit the changes to the 'dev' branch.

Goto [Travis CI](http://travis-ci.org/), login with your Github credentials, and add the *username.github.io* repository for Travis to access.
We now need to provide access of this repository to Travis CI, we do so by creating a GITHUB API Key and adding the GITHUB username and API key to the Travis CI Environment Variables associated with the newly added *username.github.io* repository.
<pic- github keys> , pic <travis env >

On the local git repository, we will create a .travis.yml file, this is the config file that contains the actions that the Travis CI is supposed to carry after we commit to the 'dev' branch.
Below is the .travis.yml file which prompts Travis CI to first install Hugo from the binary location and then check if any folder "/pulic" exists to delete it.
Once the script runs, Hugo generates the necessary files in a folder called "/public".
Post this script is run we delete all the other files from the root directory and copy the contents from the *"public"* to the root directory.
We then add a CNAME file with my domain name - This is needed only if you are hosting the site on a custom domain.
Checkout to master and add all the files to staging and commit. 
Finally "git push orgin master" and our production web static files are pushed to the master branch, which is then hosted at *"https://username.github.io"*  

```
  language: python

  install:
       - sudo dpkg -i binaries/hugo*.deb
       - pip install Pygments
  before_script: |
      if [ -d "/public" ]; then
      rm -rf /public && echo "Old public dir deleted ..."
      fi
  script:
      - hugo 
  after_success: |
      if [ -n "$GITHUB_API_KEY" ]; then
      git checkout --orphan master
      shopt -s extglob
      rm -rf -v !("public"|".git"|".git*") 
      # git rm -rf .
      mv public/* .
      rm -rf public binaries/hugo_*
      echo "hacktheripper.com" > CNAME
      git add -f --ignore-errors --all
      git -c user.name='travis' -c user.email='travis' commit -m init
      git push -f -q https://$GITHUB_USER:$GITHUB_API_KEY@github.com/$TRAVIS_REPO_SLUG master
      fi    
  ```         
I have created a diagram of the aforementioned workflow for a better understanding:
![www.hacktheripper.com workflow](images/workflow.png "www.hacktheripper.com workflow")
References:
1. https://gohugo.io/getting-started/quick-start/
2. https://sourcethemes.com/academic/docs/
3. https://gohugo.io/hosting-and-deployment/hosting-on-github/
4. https://jellis18.github.io/post/2017-12-03-continuous-integration-hugo/
5. https://www.rohitsalecha.com/post/create_a_personal_blog_using_hugo_academic_netlify/

PS: This post was published using the above workflow !!