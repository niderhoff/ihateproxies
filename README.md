## I hate proxies

Because every little application behaves differently. I mean, why would anybody bother to implement proxy usage into their programs anyway, because who cares about proxies in 2020?

You hate proxies, too? Then maybe you are lucky and find the correct instructions here for the program that gives you trouble (just CTRL+F).

Feel free to add instructions for anything you like. Needless to say instructions here are not comprehensive and in some cases  maybe outdated.

### How it's supposed to be

The defacto standard behaviour is something along these lines:

You set an [environment variable](https://en.wikipedia.org/wiki/Environment_variable) called `HTTP_PROXY` and one called `HTTPS_PROXY` (see below how to do that). Then all regular traffic uses the proxy defined in the former variable and all secure traffic uses the one defined in the latter.

And then there is `NO_PROXY`, which includes a list of hosts that will use no proxy at all (your machine, local network, intranet etc.)

### How reality is (why this page exists)

Some programs for some reasons (and some operating systems also) somehow started to adhere to some standard where the variables are lowercase `http_proxy`/`https_proxy`/`no_proxy`.

Some programs won't or can't read environment variables at all.

Some programs have different application layers which all need to get the proxy environment variable passed to it and somehow it gets lost on the way.

Some programs have UI settings.

Some programs have their own proxy settings.

Some programs don't work at all.

So let's start...

### How to set up environment variables for bash (and other shells)

```sh
export http_proxy="http://proxy:1234"
export no_proxy="0.0.0.0,localhost,127.0.0.1,*.local"
export HTTP_PROXY=$http_proxy
export https_proxy=$http_proxy
export HTTPS_PROXY=$http_proxy
```

- On some systems you can put this in `/etc/profile.d/proxy.sh`
- On most systems you can put this in `~/.bashrc`
- The safest way is probably to put this in `~/.profile`
- Yes HTTP_PROXY and HTTPS_PROXY are the same. Since most proxies accept https traffic over http:// anyway. If you proxy has a dedicated port for https, you can have two different settings here.

### How to (for Windows)

[This](https://www.computerhope.com/issues/ch000549.htm) explains it adequately.

### Programs that use uppercase HTTP_PROXY/HTTS_PROXY

- curl
- Postman

### Programs that use lowercase http_proxy/https_proxy

- pip
- wget

### Programs that have proxy override/their own settings

- Postman
- Most GUI applications

### Docker

*If you use Docker GUI you can set proxy there* (but you still might want to read on).

Additionally do [this](https://docs.docker.com/network/proxy/#configure-the-docker-client) to have proxy inside running containers automatically.


#### Docker Toolbox (old Virtualbox-based docker environment)

Docker accepts the uppercase variant for environment variables. However, if you use Docker Toolbox for Windows or some implementation that is based on VirtualBox, you must make sure that the proxy is correctly set before the Virtual Machine is set up as the proxy settings will permanently be injected to the VM. So just delete the "default" machine and setup a new docker-machine whenever your proxy settings change.

Most of the time you do **not** want to put the proxy variables inside the Dockerfile using `ENV` because that means that the image cannot be run on a different network/machine. Docker should pick up on the proxies when you run the container anyway and route traffic accordingly.

Also it can be useful to put the IP of your docker-machine inside the `NO_PROXY` to reach any ports you open from the container. (Regular docker commands will work usually)

When you build Images it can be useful to have the proxies inside the build (for example to to apt-get update or something during the build), but not in the image afterswards. You can achieve it like so:

```sh
docker build --no-cache --build-arg HTTP_PROXY=$http_proxy \
--build-arg HTTPS_PROXY=$http_proxy --build-arg NO_PROXY="$no_proxy" \
--build-arg http_proxy=$http_proxy --build-arg https_proxy=$http_proxy \
--build-arg no_proxy="$no_proxy" -t myContainer /path/to/Dockerfile/directory
```

### JVM based stuff

- Usually JVM takes the proxy settings from environment, but it has it's own variable called `JAVA_OPTS` and it's own syntax.
- Correct setting is `JAVA_OPTS=-Dhttps.proxyHost=<hostname> -Dhttps.proxyPort=<port>`
- Alternatively you can use a file called .jvmopts in your working directory with the same commands, but every command on it's own line.

#### Bloop

- Set your proxy settings inside .bloop/.jvmopts or ~/.bloop/.jvmopts

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
