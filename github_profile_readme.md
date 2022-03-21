---
title: "How to Customize Your GitHub Profile"
date: 2022-01-03T13:13:03+08:00
tags: [GitHub]
categories: [Note]
---

In this post, I will share how to customize your GitHub profile page,
and you can find the end result [here](https://github.com/jdhao).

<!--more-->

GitHub allows us to show README content on top of our profile page: `https://github.com/{user_name}`.

# Create a special repo

First, we need to create a special repo that has the same name as our user name.
For example, my GitHub name is `jdhao`, so the new repo name will be [`jdhao`](https://github.com/jdhao/jdhao).

# Create a README

In the new repo, add a file named `README.md`.
All content inside this file will be rendered and displayed on your profile.

# Customization

## Show latest blog post

We can use this [blog-post-workflow](https://github.com/gautamkrishnar/blog-post-workflow) [GitHub action](https://github.com/features/actions) to update the README with the latest post from our blog site.

First, edit the `README.md` and add the following section:

```markdown
# Latest blog posts

<!-- BLOG-POST-LIST:START -->
<!-- BLOG-POST-LIST:END -->
```

Then in the personal repo `https://github.com/jdhao/jdhao`, create folder `.github/workflows`.
In this folder, add `blog-post-workflow.yml`:

```yaml
name: Latest blog post workflow
on:
  schedule: # Run workflow automatically
    - cron: '30 23 * * *' # Runs every day on 23:30
  workflow_dispatch: # Run workflow manually (without waiting for the cron to be called), through the Github Actions Workflow page directly

jobs:
  update-readme-with-blog:
    name: Update this repo's README with latest blog posts
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Pull in my blog posts
        uses: gautamkrishnar/blog-post-workflow@master
        with:
          feed_list: "https://jdhao.github.io/index.xml"
```

This action will be run periodically (every day on 23:30). Check [here](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#onschedule) for the GitHub action cron syntax.

Note the feed link for `feed_list` field should be replaced with your own blog feed.

Push the changes to your personal repo.
To trigger the action manually, go to `Actions`, click the workflow, and then click `Run workflow`.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202201031342779.jpg" width="800">
</p>

This action will update your README automatically.

## Show GitHub stats

With the help of project [github-readme-stats](https://github.com/anuraghazra/github-readme-stats), we can display our GitHub stats easily:

```html
<p align="center">
<img src="https://github-readme-stats.vercel.app/api?username=jdhao&show_icons=true&count_private=true&theme=solarized-light&hide_border=true" width="800">
</p>
```

The result is:

<p align="center">
<img src="https://github-readme-stats.vercel.app/api?username=jdhao&show_icons=true&count_private=true&theme=solarized-light&hide_border=true" width="800">
</p>

# References

+ https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/about-your-profile
+ Create GitHub profile: https://www.youtube.com/watch?v=ECuqb5Tv9qI
+ https://aboutmonica.com/blog/how-to-create-a-github-profile-readme/
