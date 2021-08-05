---
layout: article
title: Events, Listeners and Redis 
description: Debugging not seeing broadcasts.. sometimes..
date: 2019-08-08
categories: [laravel, events, redis, queue]
---

I have a project at work which uses Redis to handle the queues and the 
[Laravel websocket package](https://github.com/beyondcode/laravel-websockets) by 
[Freek van de Herten](https://freek.dev) from [Spatie](https://spatie.be) and 
[Marcel Pociot](https://marcelpociot.de/) from [Beyond Code](https://beyondco.de/).

This all went fine for a long time, but _hiccups_ started to come.

The issue I was starting to see that sometimes events were not broadcasted, which was weird as it was working
before. Eventually they stopped working altogether.
I've been trying to debug this myself but at first I didn't got very far.

After asking around on a course of action on how to handle this, I decided to try the power of open
source. And so I created an [issue](https://github.com/beyondcode/laravel-websockets/issues/219) on
the Github issue tracker for the package.

There were not a lot responses to it, mostly people trying to help me debug this issue.

## Pairing

I created the issue a bit before my holiday and upon returning I paired with a colleague to debug
this ourselves.
So at this point no broadcasting was being done and were thinking what would be causing this radical
change.

### Laravel patch updates

I'm someone who tries to stay up to date with dependencies, so I figured there must be some version
bump on which it all stopped working.

Looking back in git history I saw that when it all worked the Laravel framework was on version `5.8.24`.
I figured that the issue probably originates in the Laravel framework, so I fixed composer to that
version and ran composer update. And it all still worked, next step was to increment the patch
version until it didn't work anymore.

The patch version on which broadcasting stopped working was `5.8.25` so I didn't have to look far.
Looking at the release notes for that version one contribution stood out. 

PR [#28904](https://github.com/laravel/framework/pull/28904) (Prevent event cache from firing multiple
times the same event(s))

To make sure this was the one that was causing problems, I copied the files from the previous patch
release and voila everything worked!

### Discover my Events

After studying that PR I saw the `shouldDiscoverEvents` method and that got me thinking, I
remembered seeing something about this when Taylor tweeted about being able to cache events and 
being able to discover them.

To my dismay I discovered that I didn't have this method in my `EventServiceProvider`. After adding
this method and clearing my event cache I saw that my broadcasting was working again yay :D

### Not so soon!

Broadcasting was working again which was awesome, the thing I didn't mention that this was all using
the `sync queue connection` driver. Which works, but is slower than when using Redis for example.

With the `Redis queue connection` driver broadcasting wouldn't work. The day before I had a thought;
Redis has multiple databases and I made sure that every Laravel application had it's own.

Or so I thought...

Turned out that I had the same Redis database for production and staging, which shouldn't be a
problem since they all have prefixes right?

Well.. only Horizon has these prefixes, the queue'd jobs which are stored in Redis do not.

## Closing

The problem that I faced with broadcasting was solved by doing the following things.

1. add the `shouldDiscoverEvents` method to the `EventServiceProvider` and let it return true
2. made sure that the Redis database was only being used by the current application.

The second point could also be fixed by using a prefix for the queue's. This has been
[proposed](https://github.com/laravel/ideas/issues/848) before, but it didn't gain any traction.
