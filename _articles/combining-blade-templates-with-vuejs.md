---
layout: article
title: Combining Blade templates with Vue.js
date: 2017-10-13
categories: [laravel,seo,vuejs]
---

In a project that I am working on, there are currently two public facing parts.

1. Website
2. Backoffice

In a discussion about what (front-end) technique we should use. Vue.js being a big contender. But a big concern was _Search Engine Optimization_ for the website.

Having no experience with prerender options (prerender.io, Nuxt.js) we chose to use the default template engine in Laravel projects ([Blade](https://laravel.com/docs/5.5/blade)).

I’d like to show how I went about and implemented this. Source code for an example project can be found on [Github](https://github.com/Treggats/co-housing-blade-vuejs).

Starting from scratch, I just created a fresh Laravel project with

> composer create-project laravel/laravel co-housing-blade-vuejs

The next step is to create some structure. Go to **resources/views** and add a directory called backoffice. And in that directory create a view called index.blade.php

![](https://cdn-images-1.medium.com/max/1024/1*5_PrEHF0RNNCgjnsqUgqBQ.png)

In that file reference the app.css and app.js, put the latter at the bottom of the body tag. Otherwise Vue won’t load. And put one div with an id of app in the body.

Next add two controllers, **PageController** & **Backoffice\PageController** and update the current route to point to the main index method of the _PageController._

Also add the route for the index method for the backend _PageController_.

Load the default welcome view in that method.

![](https://cdn-images-1.medium.com/max/1024/1*3CGwWx0kKUfAX2EIpz8ehQ.png)

![](https://cdn-images-1.medium.com/max/1024/1*qJsef3otvbqhbvKNRnJ4YA.png) Both controllers

![](https://cdn-images-1.medium.com/max/1024/1*s3yfsQS2G5D9armQyls6Hg.png) Web routes

By default a new Laravel project comes with an example Vue.js component, which is loaded in the _backoffice/index.blade.php_ template. So all that is needed to show the Vue.js template is to run

> npm run dev || yarn run dev

The result is that the ‘normal’ website is build using blade templates and that everything in the ‘backoffice’ route prefix is being rendered by Vue.js

So in closing we have two different front-end techniques in use due to SEO concerns.
