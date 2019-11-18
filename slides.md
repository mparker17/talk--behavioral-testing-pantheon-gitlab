---
title: Automated Behavioral Testing on a budget with Pantheon, GitLab CI, and Behat - slides
---

<section>

# Automated Behavioral Testing on a budget with Pantheon, GitLab CI, and Behat
— with [mparker17][mparker17-drupalorg]

<small>Follow along at [mparker17.github.io/talk--software-testing-overview](index.md)</small>

[mparker17-drupalorg]: https://www.drupal.org/u/mparker17

</section>

<!-- Introduction -->

<section>

## Who am I?

[mparker17][mparker17-drupalorg] on Drupal.org, [Github][mparker17-github], and [Gitlab][mparker17-gitlab]

I work for [Digital Echidna][echidna], [Brady's Meat & Deli][bradysmeats]

<small>(previously [Environment Canada][wsc], [Versabanq][versabanq], [UWaterloo][uwaterloo], [PeaceWorks][peaceworks], [Myplanet][myplanet], [OpenConcept][openconcept])</small>

I am an [Acquia Certified Drupal Developer][acquia-cert]

<small><abbr title="Computer Science">CS</abbr> degree not completed at UWaterloo</small>

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

<!-- Why. -->

<section>
<section>

## How did this all start?

* Bradys is already using Pantheon for our existing D7 site
* Porting site to D8, finally
* Needed to install [Drupal Commerce][drupal-commerce] for our Turkey Order System
    * Drupal Commerce has non-Drupal dependencies, so needs composer
    * Using Composer on Pantheon breaks automatic updates, is not recommended

[drupal-commerce]: http://drupal.org/project/commerce

</section>
<section>

### How did this all start? (ctd.)

* Researched other hosting providers ([Acquia Cloud][acquia-cloud], [Platform.sh][platformsh-pricing], [Omega8.cc][omega8-pricing], [Contegix][contegix-drupal])
    * [Pantheon][pantheon-pricing] still cheaper by large margin for our use-case
* Reading support pages and forums pointed me to [pantheon-systems/example-drops-8-composer][example-drops-8-composer]
    * didn't solve the broken automatic updates,
    * is a recommended way of handling custom composer dependencies

[example-drops-8-composer]: https://github.com/pantheon-systems/example-drops-8-composer/

[acquia-cloud]: https://www.acquia.com/products-services/acquia-cloud
[platformsh-pricing]: https://platform.sh/pricing/
[omega8-pricing]: https://omega8.cc/pricing
[contegix-drupal]: https://www.contegix.com/drupal
[pantheon-pricing]: https://pantheon.io/plans/pricing

</section>
<section>

## Why Pantheon?

1. I'm the only developer at Brady's, and I'm not a security expert
2. Maintaining our own server would take too much time
3. Specialized Drupal hosting has key features
    * Drupal-tuned application firewall
    * strong control over code
    * automated backups
    * automated error collection and logging

</section>
<section>

## Why Pantheon? (ctd.)

4. Pantheon saves me time
    * competitive price
    * zero-config, free: HTTPS, traffic scaling, <abbr title="Content Distribution Network">CDN</abbr>, advanced caching, server-side metrics collection
    * Canadian data residency
    * automated status checks
    * (automatic core updates; but we are no longer using this feature)

</section>
<section>

## Why Gitlab CI?

* Simple pricing scheme ([max 2000 minutes per month][gitlab-pricing], versus [buying "credits"][circleci-pricing] or [max number of users][bitbucket-pricing])
* Useful, clear metrics (cycle time, CI job time, etc.)
* Easy to set up a self-hosted Community Edition instance if I want to stop using gitlab.com
* Other open-source projects I use have started using it ([Drupal.org][drupal-gitlab], [GNOME][gnome-gitlab], [LineageOS][lineage-gitlab], etc.)

[gitlab-pricing]: https://about.gitlab.com/pricing/
[circleci-pricing]: https://circleci.com/pricing/
[bitbucket-pricing]: https://bitbucket.org/product/pricing
[gitlab-ce]: https://about.gitlab.com/install/
[drupal-gitlab]: https://git.drupalcode.org
[gnome-gitlab]: https://gitlab.gnome.org
[lineage-gitlab]: https://gitlab.com/LineageOS

</section>
<section>

## Why Behat?

* Primarily need a way to test for regressions
* Very little custom code on the Bradys site - all behavior in config
* Working configuration already in template Pantheon repo
* Specify behavior with industry-standard [Gherkin][gherkin] <abbr title="Domain Specific Language">DSL</abbr>

[gherkin]: https://cucumber.io/docs/gherkin/reference/

</section>
</section>

<!-- Setup -->

<section>
<section>

## Repo setup

* Trimming down `example-drops-8-composer`

</section>
<section>

## Set up automation bot

* New email address
* Generate SSH keys
* Sign up for Pantheon user account

</section>
<section>

## Setting up Gitlab CI variables

* Encrypting the SSH private key
* Getting a Pantheon token
* Figuring out your Pantheon env and site

</section>
</section>

<!-- End result. -->

<section>
<section>

# End result

1. When I push to a branch
2. GitLab CI runs in three stages:
    1. Build
        * Deploys to dev with SFTP, runs behat tests on the dev site
    2. Test
        1. PHPCodeSniffer, unit tests
        2. Behat
    3. Cleanup

</section>
<section>

TODO screenshots

</section>
</section>

<!-- Conclusion. -->

<section>

## Thanks!

</section>
