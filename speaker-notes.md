---
title: Automated Behavioral Testing on a budget with Pantheon, GitLab CI, and Behat - speaker notes
layout: static
---

# Automated Behavioral Testing on a budget with Pantheon, GitLab CI, and Behat
— with mparker17

Speaker notes

## 0/27 - (prep)

1. Screensaver off on computer, phone?
2. Slides working?
3. Speaker notes open on phone?
4. Wireless presenter working?
5. Mute slides, if applicable.

## 1/27 - (title)

1. Unmute slides, if applicable.
2. Can everyone hear me?
3. Shout-out to co-workers in the audience.
4. Start recording, if applicable.
5. Thank everyone for coming.
6. Thank hosts, if applicable.

## 2/27 - Who am I?

1. mparker17; but not on social media
2. I work for Brady's Meat & Deli, a small butcher shop in Waterloo, Ontario, Canada
    1. Have worked for Bradys since 2003 (a/o 2019 = 16y)
    2. Started out washing dishes and cleaning; later serving customers; now mostly come in to look after computer, scales, audio, cash register, etc.
    3. Created the shop's website in 2005; switched to Drupal 6 in 2009; then D7 in 2012
3. I also work for a web development firm in London called Digital Echidna

## 3/27 - Who am I? (ctd.)

(see slide)

## 4/27 - How did this all start?

1. I'm currently the only developer on the Bradys website.
2. I'm working on porting the Brady's website to D8
3. We host on Pantheon (basic plan)
4. Pantheon recommends - but does not force you - to run their very shallow fork of Drupal called drops - drops-7 for D7, drops-8 for D8
    1. Aside - drops is based on Pressflow, and I became a maintainer of Pressflow because I didn't like waiting for Pantheon to update
5. If you use drops-8, you get Pantheon's fabled 1-click updates...
6. ... but you can't use Composer - if you make any Composer changes, your one-click updates will fail forevermore; with thousands of merge conflicts
7. So... as I'm porting the Bradys website, I realize we're going to need to use composer... dun dun dun
    1. Aside - We use Drupal Commerce's backend to handle Turkey orders at Thanksgiving and Christmas. Starting with [Canadian] Thanksgiving in 2019, we've added a custom order-form front-end

## 5/27 - How did this all start? (ctd.)

1. Since I was kinda blocked on porting to D8 because of drops-8; I decided to do some research... maybe other hosting providers with better support for composer had better pricing options than the last time I looked?
2. Turns out, no.
3. So I was stuck with Pantheon... so I started to do some research to find out if there were _any_ recommended ways to work with composer...
4. And stumbled across example-drops-8-composer
5. We'll talk about it more later; but for now, it's useful to know that,

    1. it was a recommended way of managing a site with composer dependencies on Pantheon, and
    2. while it still breaks 1-click updates; updates become 1-composer-command... which is a bit better I guess?

6. **In this presentation, I'm going to tell you how I set it up, and got automated behavioral regression tests out of it as a bonus**
