@title = 'Bitmask 0.9.4: Deep Changes Edition'
@author = 'kali'
@posted_at = '2016-12-28'
@more = true
@preview_image = '/img/pages/bitmask.png'

We are pleased to announce the Bitmask 0.9.4 release, codename “works for you”. This is a release focused on deep architectural changes, performance optimizations, and in response to feedback from many of you, a new user interface for the Bitmask client.

## What's new?

The first thing you will see is a brand new user interface built on html and javascript. This is the first version of Bitmask in which we leave behind the legacy Qt client. Almost all logic has been removed from the front end which communicates with the backend through a cleanly defined api allowing for faster response to user feedback.

One of our core libraries, SOLEDAD (the encrypted database that syncs all your data across devices), has undergone massive changes. This is a core library used by Bitmask as well as the Pixelated project (https://pixelated-project.org). This release includes some of these changes creating faster and more robust syncs, and we will soon merge the rest of the work to allow Bitmask to handle really big attachments and have faster initial and seamless syncing even for accounts with a large amount of data.

A deep dive into our development infrastructure, and process has produced more reliable code that is easier to evolve, release, test, and lowers the barrier to entrance for new contributors. Most of the working code has been ported to a new more modular core. Unit, integration, and e2e tests are automatically triggered per commit and monitored by our continuous integration server (for example, see https://0xacab.org/leap/bitmask-dev/graphs/0.9.4/ci). Automated nightly builds include new commits making for faster feedback from QA and test users. These architectural redesigns along with improvements in test driven development process quicken development time and release cycles.

For the command line lovers, it's now possible to interact with the bitmask daemon with a CLI.

## Try it out

We're looking forward to hearing back form you about performance and function. Test it, report issues, but don’t trust it yet in situations where data loss, delivery problems or any other errors can put you at risk or otherwise cause major trouble.

We maintain a demo provider for the mail service at https://mail.bitmask.net. This provider is already pinned in Bitmask for easy access when creating a new account from within the wizard. For this release, test only on Ubuntu Xenial operating system using Thunderbird as your email client.

Please help improve Bitmask:

* Get Bitmask: Goto https://dl.bitmask.net/client/linux/ and download 0.9.4
* Run Bitmask and select mail.bitmask.net as your provider
* Test with Thunderbird
* Report bugs in our [issue tracker](https://leap.se/code/projects/report-issues)

## Next release

With faster development cycles made possible by the architectural work done for this release we will be bringing back integration with the pixelated client early in 2017. The pixelated client is also built on html and javascript making for a more seamless integration with Bitmask. There are a [[handful of known issues]] we will fix, huge improvements to syncing speed for large accounts and attachment size already in the works, continued UI enhancements, and there are rumors of VPN re-integration swirling around. 2017 will be our best year to date.

## Links

* Commit graphs: https://0xacab.org/leap/bitmask-dev/graphs/0.9.4/commits
* Contributors: https://0xacab.org/leap/bitmask-dev/graphs/0.9.4
** 265 commits
** ~26800 lines diff
