# Localghost

**TL;DR:** clone. run `./setup`

**Uses:** [Ghost](https://github.com/tryghost/ghost) Version 5.79.0 / [Casper](https://github.com/tryghost/casper) Version 5.7.0
**Project disk usage:** 48M
**Installed disk usage:** 591M

## Why does this exist?

Normally to get Ghost up and running you would would simply use the `ghost-cli` as per [official guidance](https://github.com/TryGhost/Ghost?tab=readme-ov-file#quickstart-install). However, it seems some people have run into problems getting up and running, due to permissions or whatever surrounding Node Version Manager (NVM), amongst other weird situations.

For this project I did the very same thing, but made some minor adjustments to streamline things, and since a friend asked me for help, I provided the eventuality of this initial setup. Later on, I decided to reinitialise the project (to purge the git objects) and publish it for people to use if they wish - you should be able to clone it, run the commands and be up and running without any further messing about.

## Whats different?

- Project updated to use `yarn berry`
- Stripped node modules, logs and other cache files.
- The `current` symlink changed from full path to relative.
- Adjustments to installation and runtime performance.
- Adjusted hard linked (full) path of various configs.
- Added blank yarn lock for root.
- Added git ignore rule set.

## Preparation Assumptions

> [!IMPORTANT]
> The following on your system would be handy before getting started;
>
> - Node Version Manager (NVM) (**not required**)
> - Node 18 (LTS / Hydrogen)
> - Node Package Manager 10 (NPM, bundled with Node)
> - Yarn (just having yarn installed should be enough)
>

## Install & Go

Reduced the amount of stuff you'll need to do - simply run:

```sh
./setup
```

## Known Issues

- Workspace setup doesn't seem to work very well in this project.
- You cannot seem to install dependencies from root without passing options.

---

## Initial Observations

### [5.79.0] Security vulnerability in many dependency packages

>
> [!CAUTION]
> Something I picked up on pretty much from the get go...
>
> - **CRITICAL** (1)
>   - [Arbitrary Code Execution in underscore](https://github.com/caminashell/localghost/security/dependabot/34)
> - **HIGH** (9)
>   - [Prototype Pollution in lodash](https://github.com/caminashell/localghost/security/dependabot/60)
>   - [Knex.js has a limited SQL injection vulnerability](https://github.com/caminashell/localghost/security/dependabot/50)
>   - [Luxon Inefficient Regular Expression Complexity vulnerability](https://github.com/caminashell/localghost/security/dependabot/49)
>   - [decode-uri-component vulnerable to Denial of Service (DoS)](https://github.com/caminashell/localghost/security/dependabot/45)
>   - [Moment.js vulnerable to Inefficient Regular Expression Complexity](https://github.com/caminashell/localghost/security/dependabot/42)
>   - [Crash in HeaderParser in dicer](https://github.com/caminashell/localghost/security/dependabot/40)
>   - [Path Traversal: 'dir/../../filename' in moment.locale](https://github.com/caminashell/localghost/security/dependabot/39)
>   - [Inefficient Regular Expression Complexity in nth-check](https://github.com/caminashell/localghost/security/dependabot/36)
>   - [Regular Expression Denial of Service in trim](https://github.com/caminashell/localghost/security/dependabot/35)
> - **MODERARATE** (18)
> - **LOW** (2)
>

### [5.79.0] Simply testing the OOB start/stop functionality produced a fault

- According to the Ghost FAQ, it is possibly due to a permissions issue concerning Node Version Manager (NVM)[^1] however, I have no intention of removing NVM from my workstation simply for this project. It is not apparently clear why this is a problem in the first place or what the root cause is.
- Some issues were posted to the ghost repo on this subject:
  - ISSUE: [CLOSED: local setup complains about directory permissions #711](https://github.com/TryGhost/Ghost-CLI/issues/711)
  - ISSUE: [CLOSED: SystemError: Directory is not readable by other users on the system #1532](https://github.com/TryGhost/Ghost-CLI/issues/1532)

#### Environment

```log
System:
  OS: Linux 6.1 Debian GNU/Linux 12 (bookworm) 12 (bookworm)
  CPU: (24) x64 AMD Ryzen 9 5900X 12-Core Processor
Binaries:
  Node: 18.19.0 - ~/.nvm/versions/node/v18.19.0/bin/node
  Yarn: 4.1.0 - ~/.nvm/versions/node/v18.19.0/bin/yarn
  npm: 10.4.0 - ~/.nvm/versions/node/v18.19.0/bin/npm
```

```git
❯ which ghost
/home/<user>/.nvm/versions/node/v18.19.0/bin/ghost
❯ npm root -g
/home/<user>/.nvm/versions/node/v18.19.0/lib/node_modules
```

#### Instance

```sh
ghost install local
ghost stop
ghost start
```

> ```git
> Running in development mode
>
> ✖ Checking system Node.js version - found v18.19.0
> ℹ Ensuring user is not logged in as ghost user [skipped]
> ℹ Checking if logged in user is directory owner [skipped]
> ✔ Checking current folder permissions
> ✔ Validating config
> ✔ Checking memory availability
> ✔ Checking binary dependencies
> One or more errors occurred.
>
> 1) SystemError
>
> Message: The directory /home/<user>/ is not readable by other users on the system.
> This can cause issues with the CLI, you must either make this directory readable by others or install node and Ghost-CLI in another location.
>
> Debug Information:
>     OS: Debian GNU/Linux, v12
>     Node Version: v18.19.0
>     Ghost Version: 5.79.0
>     Ghost-CLI Version: 1.25.3
>     Environment: development
>     Command: 'ghost start'
>
> Try running ghost doctor to check your system for known issues.
>
> You can always refer to https://ghost.org/docs/ghost-cli/ for troubleshooting
> ```

#### Workaround

> [!TIP]
> Suggested in [#711](https://github.com/TryGhost/Ghost-CLI/issues/711), to start ghost server with the following option;
>
> ```sh
> ghost start --no-setup-linux-user
> ```
>

---

## Background

*This is a project to end a bad habit of dumping my findings, ideas, and thoughts into a platform that is not really suitable for sharing & cataloguing such material efficiently.*

*I chose [Ghost](https://github.com/TryGhost/Ghost) because of its simplicity, setup, the fact it is open-source, and it's branding which resonated with me. Historically, I am not a fond of Wordpress or other "free" self-serve blogging platforms like Tumlr, Wix, Blogger, or even Twitter - I wanted something that I could build upon, manage and own myself, at as little cost as possible.*

*Humorously, it was also another excuse for me to create yet another (private) repository to play around with.*


[^1]: Docs / FAQ / Using nvm: Using nvm with local and production installs https://ghost.org/docs/faq/using-nvm/
