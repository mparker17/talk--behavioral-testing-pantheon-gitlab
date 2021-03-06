---
title: Automated Behavioral Testing on a budget with Pantheon, GitLab CI, and Behat - slides
---

<section data-background="assets/images/2019-11-20--canonical-site--qr.png" data-background-size="123px" data-background-position="bottom left">

# Automated Behavioral Testing on a budget with Pantheon, GitLab CI, and Behat
— with [mparker17][mparker17-drupalorg]

<small>Follow along at [mparker17.github.io/talk--software-testing-overview](index.md)</small>

[mparker17-drupalorg]: https://www.drupal.org/u/mparker17

</section>

<!-- Introduction -->

<section>
<section>

## Who am I?

[mparker17][mparker17-drupalorg] on Drupal.org, [GitHub][mparker17-github], and [GitLab][mparker17-gitlab]

I work for [Brady's Meat & Deli][bradysmeats], [Digital Echidna][echidna]

![Brady's Meat & Deli's logo](assets/images/2019-11-20--sponsor--bradysmeats.svg)
![Digital Echidna's logo](assets/images/2019-11-20--sponsor--digital-echidna.svg)

<small>(previously [Environment Canada][wsc], [Versabanq][versabanq], [UWaterloo][uwaterloo], [PeaceWorks][peaceworks], [Myplanet][myplanet], [OpenConcept][openconcept])</small>

[mparker17-github]: https://github.com/mparker17
[mparker17-gitlab]: https://gitlab.com/mparker17
[echidna]: https://echidna.ca
[bradysmeats]: https://bradysmeats.com
[wsc]: https://www.canada.ca/en/environment-climate-change/services/water-overview/quantity/monitoring/survey.html
[versabanq]: https://www.versabank.com
[uwaterloo]: https://uwaterloo.ca
[peaceworks]: https://peaceworks.ca
[myplanet]: https://www.myplanet.com
[openconcept]: https://openconcept.ca
[acquia-cert]: https://certification.acquia.com/user/843258

</section>
<section>

## Who am I? (ctd.)

I am an [Acquia Certified Drupal Developer][acquia-cert]

<small><abbr title="Computer Science">CS</abbr> degree not completed at [UWaterloo][uwaterloo]</small>

[acquia-cert]: https://certification.acquia.com/user/843258
[uwaterloo]: https://uwaterloo.ca

</section>
</section>


<!-- Why. -->

<section>
<section>

## How did this all start?

1. Bradys is using Pantheon for our existing D7 site; but we are porting our site to D8
2. We needed to install [Drupal Commerce][drupal-commerce]
    * Drupal Commerce has non-Drupal dependencies, so it needs Composer
    * Using Composer on Pantheon breaks automatic updates — and isn't encouraged

[drupal-commerce]: http://drupal.org/project/commerce

</section>
<section>

### How did this all start? (ctd.)

3. Researched other hosting providers ([Acquia Cloud][acquia-cloud], [Platform.sh][platformsh-pricing], [Omega8.cc][omega8-pricing], [Contegix][contegix-drupal])
    * ... for our use case, [Pantheon][pantheon-pricing] is still cheaper by large margin
4. Reading support pages and forums pointed me to [`pantheon-systems/example-drops-8-composer`][example-drops-8-composer]
    * it didn't solve the broken automatic updates;
    * but it is a recommended way of managing a site with composer dependencies

[example-drops-8-composer]: https://github.com/pantheon-systems/example-drops-8-composer/
[acquia-cloud]: https://www.acquia.com/products-services/acquia-cloud
[platformsh-pricing]: https://platform.sh/pricing/
[omega8-pricing]: https://omega8.cc/pricing
[contegix-drupal]: https://www.contegix.com/drupal
[pantheon-pricing]: https://pantheon.io/plans/pricing

</section>
</section>

<!-- Artifact repos -->

<section>
<section>

## Concept: artifact repos

<div class="fragment">

<small>By Illustratedjc - Own work, CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=26761018</small>

![A photo of Kitchener's THEMUSEUM, taken by Illustratedjc](assets/images/2013-06-19--Illustratedjc--themuseum.jpg)

</div>

</section>
<section>

### Concept: artifact repos (ctd.)

<div class="fragment fade-in-then-semi-out">

1. PHP is an interpreted language: its source code is the program — "traditional" languages are compiled to "artifacts", which get distributed/run.

</div>
<div class="fragment fade-in-then-semi-out">

2. Before D8, Drupal was in its own world: nearly everything in modules or themes
    * (sometimes you needed libraries — usually front-end scripts — and PHP libraries were a special case)

</div>
<div class="fragment fade-in-then-semi-out">

3. This meant you could use the same Git repo to:
    * version control your source code,
    * manage the state of your production filesystem.

</div>

</section>
<section>

### Concept: artifact repos (ctd.)

4. With D8, we made a conscious decision to build on work outside Drupal community:
    * this lets us accomplish more than if we did everything ourselves...
    * <span class="fragment">but... we have to deal with Composer (which compiles dependencies);</span>
    * <span class="fragment">and... modern theming practices often involves compiling assets.</span>

<div class="fragment">

5. So now web dev has become more like "traditional" development <br />(i.e.: we have to compile before we can run)

</div>
</section>
<section>

### Concept: artifact repos (ctd.)

6. Git treats artfacts like any other file
    * this is great if you're using Git to manage the state of a filesystem,
    * but if you're doing that, it makes development difficult (lot of noise/churn)

</section>
<section>

### Concept: artifact repos (ctd.)

One possible solution:

1. tell your VCS to ignore artifacts,
2. do your development as normal
3. deploy the repo to production
4. compile your dependencies, assets, etc. on production

</section>
<section>

### Concept: artifact repos (ctd.)

This has problems, though...

* <span class="fragment fade-in-then-semi-out"> **reproducability** — composer/npm *can and will* download different versions of packages on different runs</span>
* <span class="fragment fade-in-then-semi-out"> **disaster recovery** — what if package server is down? what if you want to spin up a replacement server?</span>
* <span class="fragment fade-in-then-semi-out"> **security** — how do you detect unauthorized changes? what if your prod server has limited outbound internet access for security?</span>
* <span class="fragment fade-in-then-semi-out"> **scalability** — what if you want to spin up a load-balancer running identical code?</span>

</section>
<section>

### Concept: artifact repos (ctd.)

* A better solution:
    1. store your code in a "source" repo without compiled dependencies,
    2. use Continuous Integration (CI) to compile your dependencies, and package that into an "artifact repo"
    3. deploy your artifact repo

<div class="fragment">

* The **artifact repo** gives you the reproducability, disaster recovery, security, and scalaiblity you need
* The **source repo** makes development / code review easier

</div>

</section>
</section>

<!-- Setup -->

<section>
<section>

## Set up source repo

[`pantheon-systems/example-drops-8-composer`][example-drops-8-composer] is a source repo template

1. But it had a lot of stuff in it that I didn't need (provisioning scripts, CircleCI code, etc.)
2. And I wanted to understand it
3. So I started by deleting all the stuff I didn't need
    * Got it down to 32 files and 1200 lines of code (excluding `composer.lock`)

</section>
<section>

## Set up automation bot

The automation bot needs to push code to Pantheon...

1. Create the bot an email account
2. Generate an SSH key pair with `ssh-keygen -f $destination_file`
    * Don't forget the `-f $destination_file`!
    * Don't set a password because GitLab CI can't enter it
3. Sign up bot for Pantheon user account; add SSH public key; generate machine token
4. Grant the bot access to the site

</section>
<section>

![Screen capture of generating an SSH key for the automation bot](assets/images/2019-11-19--bot-ssh-keygen.png)

</section>
<section>

![Screen capture of uploading the automation bot's SSH key](assets/images/2019-11-19--bot-upload-ssh-pubkey.png)

</section>
<section>

![Screen capture of getting a machine token for the automation bot](assets/images/2019-11-19--bot-machine-token.png)

</section>
<section>

![Screen capture of adding the automation bot to a site's team](assets/images/2019-11-19--add-bot-to-team.png)

</section>
<section>

## Setting up GitLab CI variables

The automation bot is going to push from GitLab...

1. Base64-encode the SSH private key with `cat $destination_file | base64 -w0`
2. Add information to the GitLab repo's Settings -> CI/CD -> Variables...
    * `SSH_PRIVATE_KEY` = the base64-encoded one
    * `TERMINUS_ENV` = `dev` (i.e.: the pantheon environment I want to run tests on)
    * `TERMINUS_SITE` = the machine name of the site
    * `TERMINUS_TOKEN` = the machine token

</section>
<section>

![Screen capture of base64-encoding a SSH key](assets/images/2019-11-19--bot-base64-ssh-key.png)

</section>
<section>

![Screen capture of setting GitLab CI variables](assets/images/2019-11-19--set-gitlab-ci-variables.png)

</section>
<section>

## GitLab CI Variable options

When entering variables, you'll see the following options...

* **Type** — can be variable or file — examples used vars
* **Protected** — whether variable is available during feature branch tests — maybe "no" on public sites?
* **Masked** — whether variable shows up in logs — "no" for keys/tokens
* **Scope** — lets you make multiple vars with same name, but different values per environment — useful if CI deploys to multiple environments

</section>
<section>

## Behat tests

`example-drops-8-composer` looks for tests in `tests/behat/features/`

For example:

```gherkin
# tests/behat/features/environment_indicator.feature
Feature: Environment indicator
In order to avoid making changes to the wrong environment
As an administrator
I need a way to distinguish which environment I am using

@api
Scenario: Administrator sees links to open this page in other environments
    Given I am logged in as a user with the "administrator" role
    When I go to "admin/content"
    Then I should see "Open in: Dev"
```

</section>
<section>

## End result

1. When I push to a branch, GitLab CI runs in two stages...
    1. **build** — compiles artifact repo, pushes with SFTP to Pantheon dev site
    2. **test** — spawns two sub-processes that run independently
        1. **test:behat** — runs Behat tests on the Pantheon dev site
        2. **test:code_sniff_unit_test** — runs `php -l`, `phpcs` with Drupal, DrupalPractice lints, `phpunit` on custom code

</section>
<section>

![Screen capture a successful GitLab CI pipeline](assets/images/2019-11-19--success-ci-pipeline.png)

</section>
<section>

![Screen capture of a successful Behat test run](assets/images/2019-11-19--success-ci-pipeline-output.png)

</section>
</section>

<!-- Limitations -->

<section>
<section>

## Limitation: no multidev

This setup mangles the dev environment, so only one person can test, and only one change at a time; and you can't really use the dev environment for anything else

* example-drops-8-composer was designed to be used with Pantheon multidev; but that's expensive
* this works for our use case though (1 dev, working local environment, hardly use dev, rarely use stage)


</section>
</section>

<!-- Conclusion. -->

<section>

## Thanks!

Special thanks to Rob and Monica at [Brady's Meat & Deli][bradysmeats]

</section>
