---
published: true
title: "Manage your dev.to blog posts from a GIT repo and use continuous deployment to auto publish/update them"
cover_image: "https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/github-travis-dev-to.png"
description:
tags: devto, publication, blogpost, continuousdeployment
series:
canonical_url:
---

**04/2023 UPDATE**: While the overall idea of the blog post remains unchanged and is still relevant, I no longer recommend to use Travis. Instead use Github Actions _(or if you're not using Github, any other CI runner of your choice)_. The [template repo](https://github.com/maxime1992/dev.to) has been updated to use Github Actions. Feel free to read this blog post to get the main idea but then head over to the [template repo](https://github.com/maxime1992/dev.to) for a more up to date step by step guide.

---

Have you ever wished that you had a monorepo (_\*1_ ) containing all of your dev.to posts on GitHub and once you merge an update into the master branch they would just automatically be updated on dev.to?

_1) or multiple repositories, it doesn't matter :smiley:_

Then you'll be pleased with what's coming, otherwise I'll let you know why you might want that in the next section.

## Why would I want to put all my blog posts on a git repo?

- Don't be afraid of messing up one of your articles while editing it
- Same good practices as when you're developing (format, make commits, have a history of your changes, compare when editing, etc)
- Use [prettier](https://prettier.io/) to format the markdown and all the code snippets
- Let people contribute to your article by creating a PR against it (tired of comments going sideways because of some typos? Just let people know they can make a PR at the end of your blog post)
- Create code examples close to your blog post and make sure they're correct thanks to [Embedme](https://github.com/zakhenry/embedme) (_\*1_)

_\*1: Embedme allows you to write code in actual files rather than into a Markdown file and then will inject the code for you where needed._

If you prefer not to use Prettier or Embedme, you can do so by simply removing them but I think it's a nice thing to have!

# Is it long to setup?

No more than a few minutes. Probably 3 to 5 minutes top, from scratch to automatically deploying an update on one of your blog posts on dev.to using this new setup :fire:!

# Show me how to set it up!

You can choose whether you want to integrate this workflow into an existing repository or using a **template** I've made to simplify the process of starting from scratch.

In this blog post we will focus on getting started using the template but reading this you'll realise how easy it would be to integrate in your own workflow.

Main steps:

- 1. Copy the template
- 2. Create a dev.to token
- 3. Pass that token to Travis CI
- 4. Put your blog post(s) into your new repository

## 1. Copy the template

Go to https://github.com/maxime1992/dev.to and copy the template:

![Copy the template repository](./assets/github-template-repo.png 'Copy the template repository')

Set the name of the repository as you wish and the description too:

![Define the name, description and visibility of the repository](./assets/create-repository-from-template.png 'Define the name, description and visibility of the repository')

_(note: if you want to use the free instance of Travis to continuously deploy your blog posts, the repository has to be public)_

Once your repository has been generated, you should see something like the following:

![Generated repository](./assets/repository-generated-from.png 'Generated repository')

## 2. Create a dev.to token

Go to https://dev.to/settings/account and give a name to the token so you can remember where you're using it:

![Generate a dev to token](./assets/dev-to-generate-token.png 'Generate a dev to token')

Keep the page open for now.

## 3. Pass that token to Travis

**Edit: 8th of July 2020**

[Beeman](https://dev.to/beeman) has written a great post on [dev.to](https://dev.to) to use [Github Actions](https://github.com/features/actions): {% link https://dev.to/beeman/automate-your-dev-posts-using-github-actions-4hp3 %}

The main advantage of Github Actions over Travis is that you can get 2000mn of free time if the repo is private (which should be more than enough!).

While we'll be focusing on how to use Travis as our CI, do check Beeman's article if you prefer to use Github Actions! =)

Go to https://travis-ci.org/account/repositories

As the GitHub repository has just been created, Travis may not see the repo yet. If that's the case, click on the left on the "Sync account" button to force the refresh of your repositories.

![Travis sync account and repositories](./assets/travis-ci-sync-account.png 'Travis sync account and repositories')

Once that's done you should be able to find your project and you'll need to click on the toggle button on the right to activate it:

![Travis activate the repository](./assets/travis-ci-my-dev-to-activated.png 'Travis activate the repository')

You can now click on "settings".

We now need to pass the dev.to token to Travis. Within the "environment variables" section, define a new one called `DEV_TO_GIT_TOKEN`. Go back to the dev.to tab where you've generated the token, copy it and paste it into the "value" input.

![Pass the dev to token to Travis](./assets/travis-ci-set-token.png 'Pass the dev to token to Travis')

Once ready, click on the "add" button.

## 4. Put your blog post(s) into your new repository

First thing that you need to do is make sure that your package.json defines a property `repository.url`. It should look like this one: https://github.com/maxime1992/my-dev.to.git with your own username/repository name. It will be used to retrieve your article images from there.

Then, notice that there's a `dev-to-git.json` at the root of the project. This is where we will be managing the blog posts we want to publish.

That json file should contain an array, and every entry should have 2 fields: `id` and `relativePathToArticle`.

Example:

```json
[
  {
    "id": 133785,
    "relativePathToArticle": "./blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/manage-dev-to-blog-posts-with-continuous-deployment.md"
  }
]
```

There's no easy way to manage the creation of an article (yet?) but it's a quick / one time thing. Go to https://dev.to and click at the top on the "write a post" button. Write a title (that can be updated later) and press the "Save Draft" button. This way the article is not published yet and only available to you.

As dev.to doesn't display the ID of a blog post on the page itself, I've made a small query to find it into the page. Open your browser console on the dev.to page of your article and paste the following:

```js
+$('div[data-article-id]').getAttribute('data-article-id');
```

This will give you the ID of the post to put into `dev-to-git.json`.

Your blog post should have a "front matter" header. For example, the one of the blog post I'm currently writing is:

```
---
published: false
title: "Manage your dev.to blog posts from a GIT repo and use continuous deployment to auto publish/update them"
cover_image: "https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/github-travis-dev-to.png"
description:
tags: devto, publication, blogpost, continuousdeployment, github, travis
series:
canonical_url:
---
```

This means that you can manage all of those attributes directly from your blog post written in Markdown. How cool is that?

Finally, all the local images links are rewritten to become remote ones that points to the files on GitHub. Yes, even your images can be part of your versioning process :raised_hands:!

# Thanks for reading

Don't be shy, let me know what you think of this project in the comments :relaxed:. Do you like it? Will you consider using it? If not, I'd love to know what's missing to make it work better for you, with your workflow.

<hr>

I work at [CloudNC](https://cloudnc.com) in central London and we are [recruiting](https://cloudnc.com/careers)!

![CloudNC is recruiting](./assets/join-cloudnc.jpg 'CloudNC is recruiting')

We have a hackday every first Friday of the month and it's during the last one that I decided to work on this project. I've open sourced an npm module called [dev-to-git](https://github.com/maxime1992/dev-to-git) that handles most of the heavy work to read and publish on dev.to (which is used by the template repository).

# Found a typo?

If you've found a typo, a sentence that could be improved or anything else that should be updated on this blog post, you can access it through a git repository and make a pull request. Instead of posting a comment, please go directly to https://github.com/maxime1992/my-dev.to and open a new pull request with your changes. If you're interested how I manage my dev.to posts through git and CI, [read more here](https://dev.to/maxime1992/manage-your-dev-to-blog-posts-from-a-git-repo-and-use-continuous-deployment-to-auto-publish-update-them-143j).

# Follow me

| &nbsp;                                                                                                                              | &nbsp;                                                                                                                                           | &nbsp;                                                                                                                                               | &nbsp;                                                                                                                                                    | &nbsp;                                                                                                                                                                | &nbsp;                                                                                                                                                                                     |
| ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [![Dev](https://raw.githubusercontent.com/maxime1992/my-dev.to/master/shared-assets/dev-logo.png 'Dev')](https://dev.to/maxime1992) | [![Github](https://raw.githubusercontent.com/maxime1992/my-dev.to/master/shared-assets/github-logo.png 'Github')](https://github.com/maxime1992) | [![Twitter](https://raw.githubusercontent.com/maxime1992/my-dev.to/master/shared-assets/twitter-logo.png 'Twitter')](https://twitter.com/maxime1992) | [![Reddit](https://raw.githubusercontent.com/maxime1992/my-dev.to/master/shared-assets/reddit-logo.png 'Reddit')](https://www.reddit.com/user/maxime1992) | [![Linkedin](https://raw.githubusercontent.com/maxime1992/my-dev.to/master/shared-assets/linkedin-logo.png 'Linkedin')](https://www.linkedin.com/in/maximerobert1992) | [![Stackoverflow](https://raw.githubusercontent.com/maxime1992/my-dev.to/master/shared-assets/stackoverflow-logo.png 'Stackoverflow')](https://stackoverflow.com/users/2398593/maxime1992) |

# You may also enjoy reading

| [!['Cover'](https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/the-holy-grail-of-note-taking/assets/cover.png)](https://dev.to/maxime1992/the-holy-grail-of-note-taking-private-data-efficient-methodology-and-p2p-encrypted-sync-across-all-your-devices-1ih3) | [!['Cover'](https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-3/assets/enigma-3-cover-image.png)](https://dev.to/maxime1992/brute-forcing-an-encrypted-message-from-enigma-using-the-web-worker-api-166b) |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [The holy grail of note-taking: Private data, efficient methodology and P2P encrypted sync across all your devices](https://dev.to/maxime1992/the-holy-grail-of-note-taking-private-data-efficient-methodology-and-p2p-encrypted-sync-across-all-your-devices-1ih3)                   | [Brute-forcing an encrypted message from Enigma using the web worker API](https://dev.to/maxime1992/brute-forcing-an-encrypted-message-from-enigma-using-the-web-worker-api-166b)                                                            |
