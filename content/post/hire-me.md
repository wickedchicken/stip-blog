---
title: "Hire Me"
date: 2024-11-04T00:20:35+01:00
draft: false
---

# Hire me!

Hello! I'm a software engineer looking for a new job in Berlin (or remote)!
I have 15 years of professional experience in small, medium and large companies, and am
looking forward to seeing how I can use that experience to help further your team's goals.
I have listed more about about myself below.

If I seem
like a good fit, please contact me using one of the methods listed in the "contact"
section. Even if you're not hiring, I'm up for a coffee or chat if you're in Berlin.
Looking forward to hearing from you!

## What I'm Looking For

I'm relatively open in my search right now, and am interested in the following three
areas:

* Rust (I am still a novice Rust programmer, but am very willing to expand my skills).
* Decarbonization.
* Small companies or startups.

I would be happy to find a job
that matches any one of these, but would be very interested if you are offering something
that matches two or all three.

Additionally, while I have a long career in building and operating continuous integration
systems, I am looking for a role developing backend services or as a
"jack-of-all-trades" engineer.

## Experience

My most recent experience is listed below. More information can be found on my
[LinkedIn page][linkedin].

### Ableton (2017-2024)

* Software Engineer on the Developer Tools (DevTools) team.
* Managed the Jenkins-based Continuous Integration (CI) infrastructure on Mac, Windows, Linux
  and iOS for Ableton's products along with associated Python-based tooling.
* Significantly contributed to an ongoing effort to port projects from a legacy CI system
  to a more modern one.
* Built an automated deployment system for a legacy Jenkins server which was
  previously undeployable and Jenkins nodes which were only deployable via an arduous
  manual process.
* Designed and owned the software running the custom CI system for [Ableton Move][move],
  which runs functional and performance tests on physical devices multiple times a day.
* Improved operations by introducing more rigorous monitoring and alerting systems.
* Wrote and maintained CLI and web-based developer tooling, including GitHub integrations.
* Conducted audits and cleanup of legacy code few wanted to touch.
* Set priorities, ran planning meetings and coordinated with client development teams
  while still performing engineering duties as part-time Product Owner.

### Google (2012-2017)

* Software Engineer on the Chrome Infrastructure team.
* Managed the Buildbot-based Continuous Integration (CI) infrastructure for Chromium and
  Chrome on Mac, Windows, Linux, ChromeOS, Android and iOS, along with associated
  Python-based tooling.
* Built an automated deployment system for Buildbot servers, significantly
  reducing toil, human error, infrastructure drift, and downtime.
* Improved operations by introducing more rigorous monitoring and alerting systems.
* Tech Lead of the Chrome on Android CI infrastructure team[^tech-lead].
  This was likely the largest open[^open] mobile continuous
  integration system at the time, with over 2000 Android devices running tests.
* Ported Chrome on Android to use a new [task scheduler][swarming] which resulted in a
  25% reduction in resource usage and execution time.


## Job Requirements

* I am looking for a job in Berlin or a remote position that allows me to stay in Berlin.
* I'd prefer to work roughly during central European hours, but can make certain
  exceptions for US time zones.
* I'm looking for a full-time position, but am open to contracting work as well.
* I don't mind occasional travel.
* I can be paid in Euro, US dollars, and stock (to a certain extent).

## Skills / Attributes

* 15 years of Python experience, both 2.x, 3.x, and porting the former to the latter.
* 12 years experience in continuous integration systems and developer tooling.
* Extremely comfortable with Linux in both desktop and server environments.
* Background in electrical engineering; can occasionally solder stuff[^through-hole]
  or debug firmware.
* Good at identifying and automating manual and tedious processes.
* Very familiar with Git and GitHub.
* Very familiar with Docker and Dockerfiles.
* Familiar with modern GCP and Azure.
* Familiar with Grafana, Prometheus and Alertmanager.
* Familiar with Ansible.
* Lightly familiar with Kubernetes and Terraform.
* Lightly familiar with Rust, Go and Nix.
* US citizen and German permanent resident.
* Telc B1 Zertifikat Deutsch, but I speak somewhere between a B2/C1 level.

## Fun Facts

* Originally got started with Linux by downloading ZipSlack over dialup and compiling
  sound card drivers.
* One of the few people in the world who can claim professional [`k`][k-lang]
  experience on their resume[^k-experience].
* Was once on a conference call where "[your team] is threatening to
  destabilize Git hosting at Google" was uttered. Was later on-call during such an
  incident and created a permanent fix for the problem.
* Once collected, graphed and alerted on thermal data of phones because battery fires
  in the datacenter were a concern.
* Can list about 4 reasons why Apple makes automated deployment of CI systems incredibly
  painful.
* Once diagnosed an issue where a device's floating serial-port inputs received radio
  interference from the device itself, which were interpreted as keystrokes
  that subsequently interrupted the device's own boot process.

## Contact

The easiest and most professional way to contact me would be via my
[LinkedIn page][linkedin]. However, my email address is relatively easy to obtain from my
git commits or personal website. If you contact me via email, please do so from a company
email address or with some other way to verify your identity.

Looking forward to hearing from you!

\- Mike

[move]: https://www.ableton.com/en/move/

[linkedin]: https://www.linkedin.com/in/mikestipicevic/

[swarming]: https://chromium.googlesource.com/infra/luci/luci-py/+/HEAD/appengine/swarming/doc/Design.md

[k-lang]: https://en.wikipedia.org/wiki/K_(programming_language)

[^k-experience]: This was 15 years ago and only for a little while; I've forgotten nearly
  everything about `k` except for a few fun stories to tell at parties.

[^through-hole]: At least through-hole soldering. I'm not afraid of doing more complicated
  stuff, I just don't have that much hands-on experience with SMT.

[^open]: I use "open" here because the Chromium project, while
a corporate-controlled open source project, allowed outside contributors to test PRs on
its CI system.
This is in contrast to the Android team's approach, which at the time did not allow this.
If you know of a similarly large mobile testing infrastructure that was open to outside
contributors, let me know!

[^tech-lead]: Chrome on Android CI was collectively run by three teams: the operations team
  working on devices in the datacenter, the test team which created the test frameworks
  used by developers, and the infrastructure team which managed task scheduling and
  execution, fleet monitoring, and developer integrations.
