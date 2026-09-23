# OkcAgent

> **Maintained fork.** Upstream OkcAgent has had no commits since 2021 and no
> longer builds (its API libraries were only on jcenter, which is gone). This
> fork, kept alongside [PGPony](https://pgpony.app), changes three things:
>
> - **Crypto provider setting:** Settings, Crypto provider lists every
>   installed app that offers the SSH authentication API or the OpenPGP API.
>   OpenKeychain stays the default; when it is not installed, PGPony is used.
> - **Builds again:** the openpgp-api and sshauthentication-api sources
>   (Apache-2.0) are bundled instead of fetched from jcenter.
> - **No error reporting:** the Bugsnag crash reporter is removed, so the app
>   sends nothing anywhere.
>
> It keeps the package name `org.ddosolitary.okcagent`, so the stock Termux
> package (`pkg install okc-agents`) works with it unchanged. Because it is
> signed with a different key, uninstall an existing OkcAgent first.
>
> On Android 13 and up, allow OkcAgent's notifications: when your key needs a
> passphrase or a hardware key tap, OkcAgent asks through a notification, and
> without it ssh waits forever.
>
> Build with JDK 11 (Gradle 7.2 does not run on newer JDKs):
> `JAVA_HOME=$(/usr/libexec/java_home -v 11) ./gradlew assembleRelease`

![Build status](https://github.com/DDoSolitary/OkcAgent/workflows/.github/workflows/build.yml/badge.svg)

A utility that makes OpenKeychain available in your Termux shell.

## Features

This tool acts as a bridge/proxy between Termux and OpenKeychain, enabling you to perform crypto operations in Termux using your keys stored in OpenKeychain, like:

- authenticate SSH connections
- sign/verify/encrypt/decrypt messages

This tool implements the existing protocols in this field so you can seamlessly integrate it with other command line utilities like `ssh` and `git`.

## Demonstration

![](https://i.ibb.co/zG8Mq9Q/okc-ssh-agent.gif)
![](https://i.ibb.co/X2PFCDm/okc-gpg.gif)

## Install

This project consists of two components: the OkcAgent app, and command line utilities to be used in the Termux shell. You need to install both of them to make it work.

### The OkcAgent app

- Stable releases
  - [GitHub Releases](https://github.com/DDoSolitary/OkcAgent/releases)
  - [F-Droid](https://f-droid.org/packages/org.ddosolitary.okcagent/)
  - <del>[Google Play Store](https://play.google.com/store/apps/details?id=org.ddosolitary.okcagent)</del>  
    The app has been removed from Play Store because Google requires a privacy policy and I don't know how to create one. Any help is welcome.
- [Dev releases](https://ddosolitary-builds.sourceforge.io/OkcAgent/)

### Command line utilities

- Stable releases
  - Termux package: `pkg install okc-agents`
  - [GitHub Releases](https://github.com/DDoSolitary/okc-agents/releases)
- [Dev releases](https://ddosolitary-builds.sourceforge.io/okc-agents/)

## How to use

1. Install OpenKeychain, Termux and the necessary components mentioned above.
2. Open the app and configure the keys to be used for crypto operations.
3. Use the command line utilities.
    - `okc-ssh-agent` acts as an SSH agent. You can use `okc-ssh-agent` as `ssh-agent`, just `eval $(okc-ssh-agent)`, it will set `SSH_AUTH_SOCK` to that path to inform programs like `ssh` to connect to it.
    - `okc-gpg` supports a limited set of GPG options so you can use it to perform some crypto operations. Read [GpgArguments.kt](https://github.com/DDoSolitary/OkcAgent/blob/master/app/src/main/java/org/ddosolitary/okcagent/gpg/GpgArguments.kt) for a complete list of supported options.

### Starting okc-ssh-agent automatically

Like the normal `ssh-agent`, `okc-ssh-agent` needs to be started first to allow SSH clients to connect to it, and it will be handy to have it started automatically. You can put some startup script in `~/.bashrc` or `~/.profile` to start the agent when you open a new shell.

For v0.1.1 and earlier versions (of the command line tools, not the OkcAgent app), you need to spefify socket path as its first argument. Take a look at [this issue](https://github.com/DDoSolitary/okc-agents/issues/2) for details.

Starting from v0.1.2, `okc-ssh-agent` supports most command line options of `ssh-agent`, so you can use the same script for `ssh-agent` to start `okc-ssh-agent`. For example, the following script will start `okc-ssh-agent` if there isn't one already running and then setup environment variables.

```bash
if ! pgrep okc-ssh-agent > /dev/null; then
	okc-ssh-agent > "$PREFIX/tmp/okc-ssh-agent.env"
fi
source "$PREFIX/tmp/okc-ssh-agent.env"
```

Or you can just add the following line to start it:

```bash
eval $(okc-ssh-agent)
```

## Notes about the app

This app is available in Play Store at the price of $1. I didn't intend to make profit from this project and simply consider it as a way of donation. If you don't want to pay, you can always download the dev releases for free using the links mentioned above, or even build the app from its source code. However, please note that the APK files from these two sources are signed with different keys, which means that you have to uninstall the existing app first if you want to switch between them.

This app uses [Bugsnag](https://www.bugsnag.com/) for error reporting.

[![](https://i.ibb.co/PQy8pkK/bugsnag.png)](https://www.bugsnag.com/)
