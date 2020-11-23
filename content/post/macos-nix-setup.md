---
title: "MacOS Nix Setup (an alternative to Homebrew)"
date: 2020-10-17T23:58:35-04:00
draft: false
---

I recently got a new Macbook, and began setting up the [Nix][nix] package manager to
install my developer toolset. I mainly did this to try and have a working setup without
installing [Homebrew][homebrew]. Since I ran into a few issues, I wanted to briefly
document what I did and why in case others wanted to try the same.

### Why Nix? (and why not Homebrew or MacPorts?)

The short answer: hype.

The long answer: I've been frustrated with Homebrew's user experience for years now, and
used this opportunity to start afresh. The default non-Homebrew answer is the venerable
[MacPorts][macports], which has been around for quite a while. Most people who aren't
functional programming or build system nerds should probably use MacPorts, as it has been
around long enough to have good support documentation floating around the internet.
Unfortunately I'm a sucker for hermetic builds [^hermetic-explain], so I decided to try
Nix.

In addition, Nix has one cool feature I haven't seen anywhere else: temporarily installing
packages using `nix-shell`. For example, I can install `ripgrep` inside a temporary shell,
and the package is automatically cleaned up when I'm done:

```sh
nix-shell -p ripgrep
```

There's a lot of cool stuff `nix-shell` can do, check out some more examples
[here][nix-shell].

#### Why not Homebrew?

If Homebrew works for you, then by all means keep using Homebrew! However, I've grown
frustrated with it. Homebrew has spent a lot of effort making a software delivery system
work across many iterations of OSX/MacOS, and countless developers have installed `brew`
as one of the first things on a new Mac. It is with this acknowledgement of the work the
Homebrew developers have put into the software and its resulting success that I offer
these criticisms:

1. Unpredictable command times. I never know if running `brew install` or `brew upgrade`
    is going to take 5 seconds or 20 minutes. This usually means knowing if a program or
    dependency is being downloaded as a prebuilt binary or compiled on the spot. It would
    be nice to signal to the user if compilation is about to happen, so they can plan the
    install accordingly.

2. Very slow `brew update` times. Homebrew's core package database is a
    [git repository][homebrew-core], which is great for enabling collaboration. Nix uses
    the [same model][nixpkgs], but the difference lies in how the clients get the package
    database: `brew update` does a `git pull` under the hood, while `nix-channel --update`
    downloads a compressed tarball each time the channel is updated. This means Homebrew
    updates can rely on git and GitHub itself as an efficient distribution mechanism,
    getting delta updates "for free" without setting up extra infrastructure. However, 
    this design choice means that the client has to maintain its own git repo --
    [`git gc` can strike at any time][homebrew-git-gc], for example, and slow things down
    tremendously. It is also a nightmare for [CI][homebrew-on-travis], where the lack of
    an updated Homebrew git cache can negatively impact build times. Meanwhile,
    `nix-channel --update` runs are very predictable, even if a bit inefficient -- you're
    downloading a 16Mb tarball when the channel is updated, but that's it.

3. Bad interactions when packages are upgraded. I'm not sure exactly why this happens, but
    occasionally when certain packages are upgraded (such as `python` from 3.7 to 3.8),
    [related packages can break][homebrew-python-pipenv]. Because Nix builds are hermetic,
    packages should all "work together" regardless of how the system was previously set
    up.

#### Why not MacPorts?

To be honest, MacPorts is probably just fine for your needs. I haven't used it in forever,
but my understanding is that it's reliable and has plenty of built-up community knowledge
on how to fix things. Take a look at it and see if it fits your needs! I also quickly 
found [this post][macports-vs-homebrew] which details some differences between it and
Homebrew. But hey, if you like adventure, give Nix a shot!

#### Why not Nix?

I felt it was honest to include a section like this.

1. I haven't done a deep evaluation, but my impression is that Nix is the least documented
    and least mature of the bunch. Expect to do a bit more digging to solve issues, but
    once you get started it generally just works.

2. Workarounds are currently needed for Catalina and above, especially if you have an
    older Mac without a T2 chip.

3. You'll need to understand a bit of the [Nix Expression Language][nix-expression]. It's
    not as scary as it sounds, but you will have to edit a `.nix` file to install a
    package (there is no CLI equivalent to `brew install` that I am aware of).

### Installing Nix

Installing Nix requires two phases: installing Nix itself, and then installing
[nix-darwin][nix-darwin]. The installation methods I know of involve `curl`ing with an
eventual `sudo` call inside the script[^yeehaw]. You will have to do something extra if
you're running Catalina[^catalina] or above.

1. Install Nix:
    * Pre-Catalina: `curl -L https://nixos.org/nix/install | sh`
    * Catalina with a T2 chip: `sh <(curl -L https://nixos.org/nix/install) --darwin-use-unencrypted-nix-store-volume`
        (according to the site, "unecrypted" is a misnomer as the chip will encrypt it
        anyway).
    * Catalina without a T2 chip: follow [these instructions][catalina-without-t2]
      [^thanks].

    Make sure that `nix-build` is now in your `PATH`. You may have to source 
    `$HOME/.nix-profile/etc/profile.d/nix.sh` in your shell's `rc` file to get this to
    work.

2. Install `nix-darwin`: 
    ```sh
    nix-build https://github.com/LnL7/nix-darwin/archive/master.tar.gz -A installer ./result/bin/darwin-installer
    ```

    You should now have `darwin-rebuild` in your `PATH`.

### Using Nix

The basic workflow for using Nix is editing `~/.nixpkgs/darwin-configuration.nix` and then
running `darwin-rebuild switch` to activate those changes.

#### Editing `darwin-configuration.nix`

Open up `~/.nixpkgs/darwin-configuration.nix` with your favorite editor. Look at
`environment.systemPackages`: this is the list of packages you'll want to install.
[Nix lists][nix-lists] are space-delimited (not comma-delimited). The packages listed
there come from the Nixpkgs channel you're subscribed to. You can list what packages are
available by running `nix-env -qaP` or searching [Nixpkgs][nixpkgs-search] online. All the
packages are prepended with `pkgs.`; I used Nix's `with pkgs;` clause to prepend that by
default:

```nix
  environment.systemPackages = with pkgs; [
      gitAndTools.gitFull
      byobu
      wget
    ];
```

#### Activating your changes

Once you're done editing, run `darwin-rebuild switch` to install your new packages! If you
made an error, `darwin-rebuild` will let you know and your current environment will not be
affected.

#### Updating the package database

If you want to update the package database, you can run

```sh
nix-channel --update
```

#### Advanced usage

Nix will detect conflicts and error out, such as when two packages install the same
binary. This can happen when `python37` and `python38`, for example, both want to create a
binary called `pydoc3`. To resolve this, you can call `lowPrio` to declare a package low
priority and have the other package win.

```nix
  environment.systemPackages = with pkgs; [
      python37
      (lowPrio python38)
    ];
```

(As a side note: once you've installed both, look at how quickly you can uninstall and
reinstall each package. It takes between 1 and 2 seconds to uninstall or reinstall
python3.7, which in my opinion is ridiculously fast.)

### Further reading

I have to admit that I only have a shallow knowledge of Nix, and the information above
has made it sufficient as a daily replacement for Homebrew. However, there is a rich
ecosystem around Nix that you may want to explore further. Here are a few links to learn
more:

* [What does `nix-darwin` provide?][nix-darwin-explanation]
* The [Nix Ecosystem][nix-ecosystem]
* The [Nix Expression Language][nix-expression]
* ["Nix Pills"][nix-pills], a detailed walkthrough of the Nix language and environment
* Tips and tricks for using [`nix-shell`][nix-shell]
* The online [Nixpkgs search][nixpkgs-search]
* A helpful [Nix cheatsheet][nix-cheatsheet]

### Good luck and have fun!

I hope this guide was helpful! Thanks to the Nix team for making such a cool package
manager. As I'm relatively new to Nix, feel free to contact me if there's something I got
wrong or could have explained more clearly. Good luck and have fun with Nix!


[^catalina]: Catalina gets a lot of shade for its read-only root filesystem and signed
    binaries, but I think it's a step forward for general purpose computing. What I object
    to is that signed binaries must be signed with registered Apple developer accounts,
    which cost money. It would be nice if that was a bit distributed, so anyone could sign
    and Apple maintained a reputation database or something. I'm annoyed at Apple for
    mixing good security and OS maintenance practices with total, walled-garden lockdown.
    Admittedly, the web has a lot of the [same security benefits][web], but we're not at a
    point yet where web apps can compete with native applications. Hopefully we'll get
    there one day.

[^hermetic-explain]: From the [Google SRE book][sre-book]: a build that is "insensitive to
    the libraries and other software installed on the build machine." This greatly
    increases reliability, because it means that whatever you have installed on your
    computer is less likely to affect the build process and the resulting program. This is
    similar to [Dockerizing][docker] an application, but less extreme and less
    resource-intensive to run.

[^thanks]: Thanks Philipp for figuring all this out and writing it down!

[^yeehaw]: This is just as risky as the classic [`curl | sudo sh`][curl-sudo].
    Unfortunately I can't find another way to easily install Nix, so I guess you can
    download https://nixos.org/nix/install and [compare its signature][install-sig] before
    running it.

[catalina-without-t2]: https://www.philipp.haussleiter.de/2020/04/fixing-nix-setup-on-macos-catalina/
[curl-sudo]: https://stackoverflow.com/questions/29382739/why-using-curl-sudo-sh-is-not-advised
[docker]: https://www.docker.com/
[homebrew-core]: https://github.com/Homebrew/homebrew-core
[homebrew-git-gc]: https://github.com/Homebrew/brew/issues/4755#issuecomment-416572560
[homebrew-on-travis]: https://discourse.brew.sh/t/best-practice-for-homebrew-on-travis-brew-update-is-5min-to-build-time/5215
[homebrew-python-pipenv]: https://discourse.brew.sh/t/homebrew-updating-major-python-version-breaks-a-lot-of-things-on-my-system/8545
[homebrew]: https://brew.sh/
[install-sig]: https://nixos.org/guides/nix-pills/install-on-your-running-system.html#idm140737320801568
[macports-vs-homebrew]: https://saagarjha.com/blog/2019/04/26/thoughts-on-macos-package-managers/
[macports]: https://www.macports.org/
[nix-cheatsheet]: https://nixos.wiki/wiki/Cheatsheet
[nix-darwin-explanation]: https://stackoverflow.com/questions/53335308/what-does-nix-darwin-provide
[nix-darwin]: https://github.com/LnL7/nix-darwin
[nix-ecosystem]: https://nixos.wiki/wiki/Nix_Ecosystem
[nix-expression]: https://nixos.wiki/wiki/Nix_Expression_Language
[nix-lists]: https://nixos.org/guides/nix-pills/basics-of-language.html#idm140737320584496
[nix-pills]: https://nixos.org/guides/nix-pills/index.html
[nix-shell]: https://ghedam.at/15978/an-introduction-to-nix-shell
[nix]: https://nixos.org/
[nixpkgs-search]: https://search.nixos.org/packages
[nixpkgs]: https://github.com/NixOS/nixpkgs
[sre-book]: https://landing.google.com/sre/sre-book/chapters/release-engineering/
[web]: https://xkcd.com/1367/
