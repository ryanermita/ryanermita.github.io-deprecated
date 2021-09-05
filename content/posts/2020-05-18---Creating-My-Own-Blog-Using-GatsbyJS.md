---
title: "Creating My Own Blog Using GatsbyJS"
date: "2020-05-18"
template: "post"
draft: false
slug: "creating-my-own-blog-using-gatsbyjs"
category: "Software Engineering"
tags:
  - "javascript"
  - "gatsbyjs"
  - "blog"
description: "On my journey of looking for a tool to create my own blog site that I can easily manage and to learn something new. I stumble upon GatsbyJS, an open-source static website generator (SSG) that is based on the frontend development framework React and makes use of Webpack and GraphQL technology. It can be used to build static sites that are progressive web apps, follow the latest web standards, and optimized for speed and security. On this article I'll be sharing the steps and aha-moments during my exploring of GatsbyJS while creating my own blog site."
---

I've been writing my articles in [Medium](https://medium.com/@ryanermita) and [Wordpress](https://ryanermita.wordpress.com/). But last week, I decided to create my own blog and eventually move my articles from Medium and Wordpress on it. I look over the internet on what tools I can use for my blog. I came across a [self-hosted Wordress](https://wordpress.org/), [Ghost](https://ghost.org/docs/concepts/hosting/), [Jekyll](https://jekyllrb.com/), [GatsbyJS](https://www.gatsbyjs.org/), [Pelican](https://blog.getpelican.com/), and the likes. For the self-hosted Wordpress and Ghost, I need to pay for a hosting site, which is problem because I don't have money for that. 
So I narrow my options to use a [static site generators](https://dev.to/integridsolutions/best-static-site-generator-to-use-in-2020-4kjk) and use [Github](https://github.com/) as hosting using its [Github Pages](https://pages.github.com/) feature. 

So now, I have 3 options for my static site generator. Jekyll, GatsbyJS, and Pelican. They are all amazing tools and can give what I want to achieved. So the deciding factor for me is the programming language that I'll be using. For Jekyll I need to use [ruby](https://www.ruby-lang.org/en/), [javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) for GatsbyJS, and [Python](https://www.python.org/) for Pelican. I'm already comfortable with ruby and python.
Javascript specifically using [ReactJS](https://reactjs.org/) is not really my strong suit, so I went to use GatsbyJS to challenge my self and to be more familiar with javascript specially ReactJS.

So for this weekend, my goals are:
- explore and learn GatsbyJS, enough to create my own blog. 
- deploy my static site in Github and use Github Pages to publish my site publicly.
- Configure github pages to use my own domain.
- move some of my contents from Medium and Wordpress to my newly created blog.

## Exploring GatsbyJS
> Gatsby is a blazing fast modern site generator for React. - https://www.gatsbyjs.org/docs/

So how GatbsyJS works? basically GastsbyJS use [source plugins](https://www.gatsbyjs.org/tutorial/part-five/) such as the items below to retrieve data to feed on the generated site. You can even [create your own source plugin](https://www.gatsbyjs.org/docs/creating-a-source-plugin/) to suit your needs.

- [`gatsby-source-filesystem`](https://www.gatsbyjs.org/packages/gatsby-source-filesystem/)
- [`gatsby-source-wordpress`](https://www.gatsbyjs.org/packages/gatsby-source-wordpress/)
- [`gatsby-source-pg`](https://www.gatsbyjs.org/packages/gatsby-source-pg/)
- [`gatsby-source-mysql`](https://www.gatsbyjs.org/packages/gatsby-source-mysql/)

GatsbyJS with the help of source plugins build the data in a way that [GraphQL](https://graphql.org/) can use it for data filtering and presentational purposes like blogs posts, products list or viewing, and the likes. This presentational layer consist of core web technologies, like HTML, CSS, and Javascript(ReactJS). 

When you're done customizing your site: setup the data source and presentational layer, you can create a production build and deploy it to your hosting service like: [AWS](https://www.gatsbyjs.org/docs/deploying-to-aws-amplify/), [Netlify](https://www.gatsbyjs.org/docs/deploying-to-netlify/), and [Github Pages](https://www.gatsbyjs.org/docs/how-gatsby-works-with-github-pages/). That's it!


## Creating my Blog
1. I install GatsbyJS cli on my system.
```bash
npm install -g gatsby-cli
```
2. Generate a new GatsbyJS project, I use a starter plugin for this. 
```
gatsby new gatsby-starter-julia https://github.com/niklasmtj/gatsby-starter-julia
```

Using a [starter plugins](https://www.gatsbyjs.org/starters/?v=2) makes it more convenient to create sites using GatsbyJS. Aside from being very simple and minimal, the starter plugin I use contains these plugins among the others:

  * [`gatsby-source-filesystem`](https://www.gatsbyjs.org/packages/gatsby-source-filesystem/) - this makes it possible to retrieve data from filesystem specially for [markdown](https://www.markdownguide.org/) content.
  * [`gatsby-plugin-react-helmet`](https://www.gatsbyjs.org/packages/gatsby-plugin-react-helmet/) - for managing my site header tags, useful for adding metadata to help with [SEO](https://en.wikipedia.org/wiki/Search_engine_optimization).
  * [`gatsby-plugin-offline`](https://www.gatsbyjs.org/packages/gatsby-plugin-offline/) - make my site available offline, [PWA](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) for the win!

&nbsp;3. Explore the directories and files to customize some of the UI components. The generated source directory is very organize and easy to follow. The directory structure of the source directory looks like this:
```
src/
  components/
  content/
  images/
  pages/
  templates/
```
  * `components` consist of reusable components. We are using ReactJS, creating reusable components is the de facto here.
   * `content` consist of the markdown files, each markdown file corresponds to a blog post. 
   * `images`, yes you guess it right. for image files.
   * `pages` consist of javascript files, each javascript file corresponds to a page on the generated site. The pages are automatically accessible via site url with this format: `https://yoursite.com/<page-file-name-without-js-extension>`
   * `templates` consist of resuable templates, like blog post template.

&nbsp;4. Creating a content. I'm using markdown files for my blog content. All markdown files resides on `src/content/` directory. Writing markdown content is very simple, you can use this site as markdown syntax reference: [Markdown Guide](https://www.markdownguide.org/basic-syntax/)

  One important thing to remember is to add metadata on your markdown files. These metadata can be included on your markdown files using [`frontmatter`](https://jekyllrb.com/docs/front-matter/), denoted by the triple dashes at the start and end of the block. This metadata is required as it is used by GraphQL for querying our content. We use the queried data for presentational purposes: displaying the blog content or listing the blog posts for example. 

  The code below is an example of a markdown content with metadata inside the `frontmatter` code block at the top of the file.

```
---
title: "Creating My Own Blog Using GatsbyJS"
date: "2020-05-18" 
draft: true
path: "/software-engineering-journal/creating-my-own-blog-using-gatsbyjs"
---

your blog post content and use markdown syntax if needed.
```

5. Generate a production build code. Running the command below will generate production ready codebase inside the `public` directory.

```
gatsby build
```

6. Deploy production codebase to github pages. As a requirement, we need to install first the `gh-pages` module by using the command below.

```
yarn add gh-pages --save-dev
```

include the deploy script on `package.json` so we dont need to type a long command just to deploy our blog. 

```
  "scripts": {
    ...
    "deploy": "gatsby build && gh-pages -d public -b master"
  },
```

Run `yarn run deploy` and that's it. Given that our repo is properly configured. We can view our site on this url: `<username>.github.io`

Configuring github pages to use a custom domain deserves its own post. To be posted soon!


*Happy coding!*