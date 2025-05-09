<<<<<<< HEAD
# Build your own Bluesky bot 🦋

This is a template repo for building [Bluesky](https://bsky.app/) bots that post on their own schedule. It uses [TypeScript](https://www.typescriptlang.org/) to build the bot and [GitHub Actions](https://docs.github.com/en/actions) to schedule the posts.

* [How to use](#how-to-use)
  * [Things you will need](#things-you-will-need)
    * [A Bluesky account](#a-bluesky-account)
    * [Node.js](#nodejs)
  * [Create a new repository from this template](#create-a-new-repository-from-this-template)
  * [Running locally to test](#running-locally-to-test)
  * [Create your own posts](#create-your-own-posts)
  * [Deploy](#deploy)
    * [Schedule](#schedule)
    * [Environment variables](#environment-variables)
  * [Set it live](#set-it-live)


## How to use

### Things you will need

#### A Bluesky account

To use this repo you will need a [Bluesky account](https://bsky.app/). Currently Bluesky is invite only and you can [sign up for an invite here](https://bsky.app/).

Once you have an account for your bot, you will need to know your bot's handle and password (I recommend using an App Password, which you can create under your account's settings).

#### Node.js

To run this bot locally on your own machine you will need [Node.js](https://nodejs.org/en) version 18.16.0.

### Create a new repository from this template

Create your own project by clicking "Use this template" on GitHub and then "Create a new repository". Select an owner and give your new repository a name and an optional description. Then click "Create repository from template".

Clone your new repository to your own machine.

```sh
git clone git@github.com:${YOUR_USERNAME}/${YOUR_REPO_NAME}.git
cd ${YOUR_REPO_NAME}
```

### Running locally to test

To run the bot locally you will need to install the dependencies:

```sh
npm install
```

Copy the `.env.example` file to `.env`.

```sh
cp .env.example .env
```

Fill in `.env` with your Bluesky handle and password.

Build the project with:

```sh
npm run build
```

You can now run the bot locally with the command:

```sh
npm run dev
```

This will use your credentials to connect to Bluesky, but it *won't actually create a post yet*. If your credentials are correct, you should see the following printed to your terminal:

```
[TIMESTAMP] Posted: "Hello from the Bluesky API"
```

To have the bot create a post to your Bluesky account, in `index.ts` change line 4 to remove the `{ dryRun: true }` object:

```diff
- const text = await Bot.run(getPostText, { dryRun: true });
+ const text = await Bot.run(getPostText);
```

Build the project again, then run the command to create a post to actually create the post with the API:

```sh
npm run build
npm run dev
```

### Create your own posts

Currently the bot calls on the function [`getPostText`](./src/lib/getPostText.ts) to get the text that it should post. This function returns the text "Hello from the Bluesky API" every time.

To create your own posts you need to provide your own implementation of `getPostText`. You can do anything you want to generate posts, the `getPostText` function just needs to return a string or a Promise that resolves to a string.

### Deploy

Once you have built your bot, the only thing left to do is to choose the schedule and set up the environment variables in GitHub Actions.

#### Schedule

The schedule is controlled by the GitHub Actions workflow in [./.github/workflows/post.yml](./.github/workflows/post.yml). The [schedule trigger](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule) uses cron syntax to schedule when the workflow runs and your bot posts. [Crontab Guru](https://crontab.guru/) is a good way to visualise it.

For example, the following YAML will schedule your bot to post at 5:30 and 17:30 every day.

```yml
on:
  schedule:
    - cron: "30 5,17 * * *"
```

Be warned that many GitHub Actions jobs are scheduled to happen on the hour, so that is a busy time and may see your workflow run later than expected or be dropped entirely.

#### Environment variables

In your repo's settings, under *Secrets and variables* > *Actions* you need to enter two Secrets to match your `.env` file. One secret should be called `BSKY_HANDLE` and contain your Bluesky username, and the other should be called `BSKY_PASSWORD` and contain your App Password that you generated for the bot account.

### Set it live

Once the schedule is set up and your Environment variables configured, push your changes to your repo and wait for the schedule to trigger the workflow. Your bot will start publishing posts based on your code.

If you have any issues with that, please [raise an issue in this repo](https://github.com/philnash/bsky-bot/issues) or send me a message on Bluesky [@philna.sh](https://staging.bsky.app/profile/philna.sh).

Being used for @bostonbruins.bsky.social
=======
# Carolina Hurricanes Bluesky Bot

This is a bot that automatically reposts the Twitter posts of the Carolina Hurricanes to [Bluesky](https://bsky.app/). Because the Twitter API now costs money to use, this bot instead collects data from the Walt Ruff Mastodon API created by sportsbots.xyz. It uses [TypeScript](https://www.typescriptlang.org/) to build the bot and [GitHub Actions](https://docs.github.com/en/actions) to schedule the posts. Although this repository is specifically for Walt Ruff, this codebase could be leveraged to produce bots to repost other Mastodon profiles to Bluesky without too much additional effort.

This bot uses the [Bluesky Bot Template](https://github.com/philnash/bsky-bot) created by [Phil Nash](https://github.com/philnash) as a boilerplate codebase to work from. Thanks, Phil!

* [Current Feature Set](#current-feature-set)
  * [Schedule](#schedule)
  * [Reposting](#reposting)
  * [Iterative Post Clustering](#iterative-post-clustering)
  * [Chunking Long Posts](#chunking-long-posts)
  * [Multimedia](#multimedia)
  * [Links](#links)
  * [Link Cards](#link-cards)
  * [Giveaway Disclaimers](#giveaway-disclaimers)
* [Things To Do](#things-to-do)
  * [Image Compression](#image-compression)
  * [Modularity](#modularity)


## Current Feature Set

### Schedule
This bot uses the GitHub Actions interface to automatically run the code. It has a cron specified to post every 5 minutes, but in reality it is considerably slower in most cases. Average time taken between executions seems to be around 10-15 minutes during normal times, and upwards of 25-45 minutes during busy times.

### Reposting
This bot uses the Mastodon Canes API to collect tweets created by the Canes. The Mastodon API returns content values in HTML, so considerable regex formatting is needed in order to get the content into a plaintext value that can be used by the Bluesky API. This Mastodon API is flawed, leading some posts to not be collected and given to my bot. This is not really a workable problem for me, as my code sees the Mastodon API as a black box, and I am essentially forced to play telephone with the posts through a middleman unless I am willing to spend the money to pay Elon Musk for Twitter API. I cannot guarantee that this bot will repost every post made by the Hurricanes on Twitter. Instead, I guarantee that this bot will repost all posts collected by the Mastodon API.

### Iterative Post Clustering 
Because the execution occurs in inconsistent intervals and it is possible that the Canes post large amounts of posts at a time, this API takes a iterative approach to posting. When executed, the bot collects a constant number of posts already made by the bot, and a constant number of posts from the Mastodon API. The bot checks each Mastodon post, ensuring that they do not match with any of the posts already posted. If the posts match, the post is discarded to avoid duplicates. If the post does not match, this post has not been posted by the bot yet, and the bot posts it. This iterative process goes in reverse sequential order, ensuring that the bot posts old posts before trying to post new posts, ensuring that even during long wait times between executions, the bot does not miss a post.

### Chunking Long Posts
When Elon Musk purchased twitter, he allowed Twitter Blue accounts to post ridiculously long posts. The Carolina Hurricanes are a Twitter Blue member, and sometimes use this feature. However, Bluesky still has a 300 character limit on posts. To remedy this, the Canes bot chunks posts longer than 300 characters into multiple smaller posts, each replied under one another in a thread format. These posts can be up to 294 characters long, and include a 6 character " [.../...]" counter. The bot determines the maximum number of words from the string that can be fit into a post (this approach allows us to avoid splitting posts mid-word), and posts these post chunks to Bluesky iteratively.

### Multimedia
The Hurricanes often post images to go along with their tweets. These images are collected and stored as blobs, and posted. Alt text is also collected and posted when available. Videos and GIFs are currently not available on Bluesky, so their thumbnails are posted instead when posted to Twitter. Multiple images can be collected and posted at a time when applicable.

### Links
Links are often abbreviated on Twitter, rendering them unusable when posted directly as plaintext to Bluesky. Instead, the bot automatically detects when a link has been abbreviated using a regular expression, and replaces it with the full URL stored in the Twitter post's URL card. 

### Link Cards
When an image is not available, posts look better when a preview is given of the URL. This automatically occurs on posts, generating a preview with title, description and thumbnail when a URL exists and an image does not.

### Giveaway Disclaimers
Giveaways performed by the Canes admin are not valid unless there is an external link to the giveaway. The bot detects common keywords related to giveaways from the Canes admin, and adds a disclaimer saying that the giveaway is not valid on bluesky.

## Things To Do
Although this bot is currently up and running on Bluesky, this codebase is still under development. As such, there are changes left to add. Some are listed below:

### Image Compression
Bluesky does not support the same size images as Twitter. When an image is too big, the bot should compress the image such that it is small enough to be posted. As is, the bot just rejects the images entirely.

### Modularity
Although this codebase exists purely to repost the posts of the Carolina Hurricanes, this codebase could serve an additional purpose of being a generic codebase for anyone wanting to create a generic bot that automatically reposts Mastodon posts from a particular account to Bluesky. In order to do this, the codebase should be modified such that it is obvious what small changes must be made to achieve this purpose, and a step-by-step guide in the readme should be provided to allow new readers to leverage my codebase for their own bots. 
>>>>>>> b485b5fcfc1c36e5f8e918b701056509001ad3f6
