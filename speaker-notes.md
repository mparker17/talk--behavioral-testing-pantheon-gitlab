---
title: Automated Behavioral Testing on a budget with Pantheon, GitLab CI, and Behat - speaker notes
layout: static
---

# Automated Behavioral Testing on a budget with Pantheon, GitLab CI, and Behat
— with mparker17

Speaker notes

## 0/28 - (prep)

1. Screensaver off on computer, phone?
2. Slides working?
3. Speaker notes open on phone?
4. Wireless presenter working?
5. Mute slides, if applicable.

## 1/28 - (title)

1. Unmute slides, if applicable.
2. Can everyone hear me?
3. Shout-out to co-workers in the audience.
4. Start recording, if applicable.
5. Thank everyone for coming.
6. Thank hosts, if applicable.
7. ¤

## 2/28 - Who am I?

1. mparker17; but not on social media
2. I work for Brady's Meat & Deli, a small butcher shop in Waterloo, Ontario, Canada
    1. Have worked for Bradys since 2003 (a/o 2019 = 16y)
    2. Started out washing dishes and cleaning; later serving customers; now mostly come in to look after computer, scales, audio, cash register, etc.
    3. Created the shop's website in 2005; switched to Drupal 6 in 2009; then D7 in 2012
3. I also work for a web development firm in London called Digital Echidna
4. ¤

## 3/28 - Who am I? (ctd.)

1. (see slide)
2. ¤

## 4/28 - How did this all start?

1. I'm currently the only developer on the Bradys website.
2. I'm working on porting the Brady's website to D8
3. We host on Pantheon (basic plan)
4. Pantheon recommends - but does not force you - to run their very shallow fork of Drupal called drops - drops-7 for D7, drops-8 for D8
    1. Aside - drops is based on Pressflow, and I became a maintainer of Pressflow because I didn't like waiting for Pantheon to update
5. If you use drops-8, you get Pantheon's fabled 1-click updates...
6. ... but you can't use Composer - if you make any Composer changes, your one-click updates will fail forevermore; with thousands of merge conflicts
7. So... as I'm porting the Bradys website, I realize we're going to need to use composer... dun dun dun
    1. Aside - We use Drupal Commerce's backend to handle Turkey orders at Thanksgiving and Christmas. Starting with [Canadian] Thanksgiving in 2019, we've added a custom order-form front-end
8. ¤

## 5/28 - How did this all start? (ctd.)

1. Since I was kinda blocked on porting to D8 because of drops-8; I decided to do some research... maybe other hosting providers with better support for composer had better pricing options than the last time I looked?
2. Turns out, no.
3. So I was stuck with Pantheon... so I started to do some research to find out if there were _any_ recommended ways to work with composer...
4. And stumbled across example-drops-8-composer
5. We'll talk about it more later; but for now, it's useful to know that,

    1. it was a recommended way of managing a site with composer dependencies on Pantheon, and
    2. while it still breaks 1-click updates; updates become 1-composer-command... which is a bit better I guess?

6. **In this presentation, I'm going to tell you how I set it up, and got automated behavioral regression tests out of it as a bonus**

7. But before we get to _how_ I set up example-drops-8-composer, there's a really important concept you need to understand first...
8. ¤

## 6/28 - Concept: artifact repos

1. Artifact repos - it's an odd name.
2. ¤
3. And no, it has nothing to do with museums
4. ¤

## 7/28 - Concept: artifact repos (ctd.)

1. Some brief programming recap
2. Starting with FORTRAN in the 1950s, programmers had to "compile" human-readable instructions for the computer into unreadable machine language for the computer to run
    1. Shout out to German, Mitchell, Shirley, Zarnke, and Shantz, who put University of Waterloo on the map with their pioneering FORTRAN compiler, WATFOR
3. For a compiled language, we usually version control the _source code_, but not the _compiled thing(s) it produces_
4. One name for "the compiled things" is "artifacts"
5. Anyway, while scriptable shells appeared earlier; a new type of programming language started to emerge in the late 80s: scripting languages, where the source code _is_ the program
6. ¤
7. PHP happens to be one of those scripting languages
8. Jumping forward a bit to the era of Drupal
9. ¤
10. Up until Drupal 8, us Drupal developers mostly lived in our own little ecosystem - we had our own modules and themes, and didn't really use other stuff invented outside of the Drupal community
11. Since you, as the Drupal developer, controlled everything to do with your Drupal site, and your Drupal source code was the same thing as the artifacts needed to run your site, you could deploy your git repo to the production server if you wanted to - and many people did because it was easier; and we got Pantheon and Acquia who built a business model around that
12. ¤
13. But, even though you didn't notice it at the time, you were using Git for two purposes - to store the source code, and to control the state of the filesystem needed to run the site
14. ¤

## 8/28 - Concept: artifact repos (ctd.)

1. Today, with D8, we've started to embrace the stuff that the rest of the PHP community has built for us...
2. ... which is great! Because we don't have to build or maintain it ourselves, and the wider PHP community often built it better than we did...
3. ¤
4. ... but using other people's work forces us to have to worry about managing and compiling dependencies... and the tool we use to do that is Composer
5. Also, modern theming techniques increasingly require us to compile other things...
    * SASS or LESS to CSS, and minifying that
    * Downloading JavaScript libraries, transpiling them to ES5 for compatibility with older browsers, and minifying that
    * etc.
6. ¤
7. And as a consequence, web development has become a lot more like compiled app development... we have to compile our site before we can run it
8. ¤

## 9/28 - Concept: artifact repos (ctd.)

1. Back to git - it treats artifacts like every other file...
2. ... which is great, because that means you can still use it to manage the state of your production filesystem...
3. ... but, if you're storing all the compiled dependencies in your repository, all of a sudden it seems like anything you do results in a 35,000-line, 650-page diff...
4. ... which makes reviewing the changes for your current work hell; not to mention what it's like trying to see what someone else did in their branch
5. So you're probably saying to me...
6. ¤

## 10/28 - Concept: artifact repos (ctd.)

1. ... Matt, don't even bother to commit the artifacts, just compile whenever you deploy
2. And after a 650-page code review where Faker happily regenerated all the Lorem Ipsum text in your pattern library to different Lorem Ipsum text because it could...
3. ... that solution pretty nice...
4. ¤

## 11/28 - Concept: artifact repos (ctd.)

1. ... until you realize that _not_ committing your compiled site has some serious problems...
2. ¤
3. Like, for example, when you find out that your build tools don't produce the same output every time you run them, especially if you try running them on a different machine
4. ¤
5. Or Drupal.org or Node Package Manager goes down while you're trying to recover from a failed deployment that is actively destroying data, leaving you to make the decision "do I lose money now by taking the site down until npm is back up; or do I lose money later by trying to recover my trashed database"
6. ¤
7. Or you try to figure out if someone's been messing with the files on your production server
8. ¤
9. Or you try to spin up a load balancer quickly because you're experiencing a traffic spike
10. So what are you to do? Maybe that 650-page code review isn't so bad after all?
10. ¤

## 12/28 - Concept: artifact repos (ctd.)

1. Nope, there's another option...
2. Store your code in a "source repo", where you can ignore the artifacts causing you grief
3. Use some sort of automated script to compile your dependencies, which you then commit to an "artifact repo"
4. Deploy your artifact repo to any server or load balancer you want...
5. ¤
6. ... and enjoy the (relatively) easy development, reproducability, disaster recovery, security, and scalability you had before D8 came along
7. This concept of source and artifact repos came from Acquia BLT, interstingly, but it's a very useful concept in working with modern Drupal sites.
8. ¤

## 13/28 - Set up source repo

1. Okay, back to the topic at hand - behavioral testing on a budget with Pantheon, GitLab, and Behat
2. We're going to start moving a lot quicker here; because we've moved into the "show and tell" portion of my talk.
3. Pantheon provided the template for a source repo in their `example-drops-8-composer` repo; and scripts to compile dependencies and put that in an artifact repo
4. But when I sat down with Pantheon's template repo, I found a LOT of stuff that wasn't relevant to me; and I wanted to understand how it works
5. So I tore out all the stuff that was irrelevant, and ended up with about 32 files and 1200 lines of code to review, which was pretty reasonable
6. I pushed that to an empty GitLab repo.
7. ¤

## 14/28 - Set up automation bot

1. The scripts in the `example-drops-8-composer` repo need to run as an authorized user, so we're going to have to set up some stuff for a bot
2. I started by giving it a full-blown email account in my domain
3. Since it's going to be pushing commits to other servers, it needs a way to authenticate with those servers, so I made it an SSH public and private key pair
4. The only thing to note is, when you do this, make sure you don't stomp your own credentials
5. I also signed up the bot for it's own Pantheon dashboard account. Yes, I did read Pantheon's terms of service, and this is allowed.
6. I then uploaded the bot's public SSH key to its Pantheon dashboard
7. I then generated a Pantheon Dashboard machine token, which I saved to my password manager 'cause I'll need it later
8. And finally, I granted the bot's Pantheon dashboard account access to the site using my own dashboard account.
9. ¤

## 15/28 - (keygen screenshot)

1. Here's a screenshot of me generating an SSH keypair
2. ¤

## 16/28 - (pantheon dashboard add key)

1. Here's a screenshot of me uploading the public key to the bot's Pantheon dashboard
2. ¤

## 17/28 - (pantheon dashboard get machine token)

1. Here's me asking the Pantheon dashboard to generate the bot a machine token
2. ¤

## 18/28 - (pantheon dashboard add team member)

1. Here's me on my own dashboard account adding the bot to the Bradys' Meat & Deli team
    1. Now if only I could get it to do the dishes for me after the shop closes...
2. ¤

## 19/28 - Setting up GitLab CI variables

1. Now that I have the GitLab repo with the source files in it, plus all the CI scripts to run...
2. ... and I just finished getting the information I need for the CI scripts to do what they're supposed to...
3. ... all I have to do is give Gitlab this information.
4. How do we do that without committing sensitive information, like the private key, to the repo?
5. The answer is... environment variables...
6. ... but first, I have to encode the private key in a way that will let me store it in an environment variable, by base64-encoding it
7. Okay, now I can set the environment variables for Gitlab's CI/CD task runners...
8. Under the repo Settings, there's a "CI/CD" section, which has a "Variables" subsection. When I expand that, I can enter the variable name, value, and some options...
9. ¤

## 20/28 - (base64 encoding the private key)

1. Here's a pointlessly-redacted screenshot of me base64-encoding a dummy private key I created for the purposes of the screenshot but never used.
2. ¤

## 21/28 - (GitLab variables form)

1. And here's me entering the environment variables... I'll explain the extra fields in just a minute... but the important parts of this form are the "Key" column and the "Value" column
2. ¤

## 22/28 - GitLab CI Variable options

1. Brief sidetrip to the Gitlab CI variable options...
2. Type - the first column - can be either variable or file - Pantheon's template used environment vars, presumably because that was the common denominator between all the CI tools it was trying to support, so everything I entered were variables
3. Recall the second column was Key, and the third column was "Value"
4. Protected - the fourth column - if you say "yes" here, then the variable will ONLY be available on "mainline" branches, like develop and master - not feature branches - setting this to "no" will break automated testing for feature branches, so I recommend leaving protected "off", unless you don't trust the people submitting merge requests (who could use unprotected variables to find out sensitive information, like a private key or machine token that gives them access to a public server)
5. Masked - the fifth column - this masks that variable in the log files... you probably want to do that for private keys or machine tokens, but setting that for other variables will make it hard to debug
6. Scope - the sixth column - this lets you set environment variables with the same key to have different values based on the environment - this can be useful in complex situations
7. The seventh and final column was hard to see in the screenshot, but is actually a "delete" button
8. ¤

## 23/28 - Behat tests

1. At this point, when you push to the source repo on GitLab; GitLab's CI/CD is pre-configured to deploy that code to dev for you
2. But this presentation is about automated testing too....
3. Pantheon's template is already set up to run Behat tests, so all you have to do is add them to a particular folder
4. They'll get run the next time you push to a branch
5. ¤

## 24/28 - End result

1. So lets try that... what happens is... (read slide)
2. ¤

## 25/28 - (screenshot of pipeline)

1. Here's a view of what happens when you push - GitLab calls this a pipeline
2. ¤

## 26/28 - (screenshot of successful behat test)

1. Here's a screenshot of Behat finding that a feature - the one I had a code sample for earlier, actually - passing
2. ¤

## 27/28 - Limitation: no multidev

1. For the most part, everything works out of the box.
2. However, Pantheon's template is designed to take advantage of its multidev feature... it's supposed to deploy to a multidev environment, run tests, and if those all pass, merge them automatically to master.
3. But, Brady's doesn't have, and can't afford Multidev - I ended up calling Pantheon telephone support to ask really really nicely if they could make a special exception for me, and they couldn't - I just gotta pay more for that feature. Sad trombone.
4. So my adapation demonstrated here just mangles the dev environment every time you push, and puts it into SFTP mode
5. If you like the functionality, you can commit the changes as a new commit to Pantheon's artifact repo
6. This works well enough for Brady's use-case...
7. ... but there's lots of room to refine this process, which is a charitable way of saying it's a little annoying, but not game-breaking
8. ¤

## 28/28 - Thanks

1. And that's my presentation - thanks for listening!
2. Special thanks to Rob and Monica for hiring me back in 2003 and letting me run the website - I have learned, and continue to learn a lot and refine my skills.
