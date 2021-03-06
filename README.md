# Kill Me Softly

Kill processes The Right Way.

## Purpose

An incredible number of times, I had to perform a simple task: killing a process,
and making sure the process was dead before proceeding.

Sometimes the process doesn't die by SIGTERM, and you need to send a SIGKILL.

In some (rare) situations, the process doesn't die by SIGKILL either.

Sometimes the process just takes some time to die.

By the way, what I usually want is something like **hey, kill this process and don't go on until it's dead**.

It's stupid, but it's everything *killmesoftly* does; it tries to send a SIGTERM to a process, if it doesn't die within
15 seconds it sends a SIGKILL, if it still doesn't die it reports it.

AFAIK there's no other tool to do that, even though such logic is implemented in a lot of software and service scripts.

## Install

### Standalone executables

**This is the recommended way to go** if you just want to use this tool, at least until I don't submit it to Homebrew and Linuxbrew.

Go anywhere in your executable path and download the latest standalone version of [kmsp](https://www.franzoni.eu/releases/killmesoftly/master/kmsp) and [kmsn](https://www.franzoni.eu/releases/killmesoftly/master/kmsn).

```
wget https://www.franzoni.eu/releases/killmesoftly/master/kmsp https://www.franzoni.eu/releases/killmesoftly/master/kmsn
chmod +x ./kmsp ./kmsn
```

### From Github source

#### Build and install

Just clone this repository, or [download a packed zip](../../archive/master.zip), and use

```
make install
```

by default, this will install standalone executables in ```/usr/local/bin```. If you want to change your install destination:

```
make PREFIX=/tmp/asd install
```

will install in ```/tmp/asd/bin``` or

```
make BINDIR=/tmp/mydir install
```

will install in ```/tmp/mydir```.

#### Make-less installation

Otherwise, for a make-less install, you could just copy  ```kmsp``` and ```kmsn``` in your path, set them executable, make sure ```kms_functions``` is in the same directory, and you're ready to go.

#### Developer-wise installation

If you want to develop changes to killmesoftly, just symlink ```kmsp```, ```kmsn``` and ```kms_functions``` to a directory in your path (e.g. ~/bin)


## Usage

You can feed the two scripts with process names (or matching expression, that will be sent to [pgrep](http://linux.die.net/man/1/pgrep)) or PIDs, respectively.

The approach is blocking by design: the script won't exit until the process is actually dead, and latter PIDs or names won't be killed until previous ones are dead.

If there's an error on one parameter (e.g. you've specified a name which matches more than one process, or a non-existent PID) the scripts will go on with the other parameters; on the contrary, if there's an hard error (e.g. a process that doesn't exit on a SIGKILL) the whole kill chain will be stopped and an error will be returned.

The default timeout is 15 seconds. It's currently hardcoded.

### kmsn

```
Kill processes softly by name matching pattern.
Usage:
kmsn first_pgrep_pattern [second_pgrep_pattern] <...>
```

### kmsp
```
Kill processes softly by pid number
Usage:
kmsp first_pid_to_kill [second_pid_to_kill] <...>
```

## Requirements

Those should be available on almost any Linux system, even on minimal installs,
and are installed by default in OSX.

* Linux or OSX
* Bash
* *Kill* and *pgrep* executables (e.g. *procps* package on Ubuntu/Debian)

## License

This is Apache-2.0 licensed, which is the most permissive license I could think of. Feel free to include and modify this code however you like.

## TODOs
* \*BSD support
* a ```--all``` switch for kmsp that allows killing all processes that match a certain parameter
* consider whether we should return different exit codes in the event some parameters are wrong; currently we return a nonzero exit code only on zero-parameters passed or hard failures.
* allow specifying a different signal rather than SIGTERM for soft kill, and/or additional signals to be sent before/after SIGTERM.
* configurable timeouts
* consider transforming such scripts in a set of functions that can be sourced, rather than used as executables
* add options for killing process groups and/or check whether children have actually exited
* add options for matching on full name, not just on process name
* remove dependency on bash and let sh suffice.
* add tool in Linuxbrew and Homebrew repositories for easy install.
* make the testsuite runnable on OSX (currently prevented by extended sed regexp)

## Thanks

The following people helped developing this software in some way or another, without
a specific order.

* Jean-Philippe Daigle
* Laurent Cozic
