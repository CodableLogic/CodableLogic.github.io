---
title: Generating Certificates with IPA
---
### TL;DR

First off, this post is more for reminding myself of what to do.
It's incredibly incomplete and anyone who didn't already know what they're doing probably wouldn't understand much of it anyways.
Sorry if you were looking for something more informational.

```
# Add host so that you can mention it in the certificate request
ipa host-add mirrors.fedoraproject.org --desc="A fake host for supporting yum repo redirection"
# Add host as Subject Alternative Name in certificate
ipa-getcert request -r -f /etc/pki/tls/certs/mirror-server.example.com.crt -k /etc/pki/tls/private/mirror-server.example.com.key -N "CN=mirror-server.example.com" -D mirror-server.example.com -D mirrors.fedoraproject.org -K HTTP/mirror-server.exmaple.com
```

### The Background

I've recently been playing with getting a CentOS repository mirror setup on a local network.
Eventually, the network wont be connected to the net and so it'll be nice to be able to install new packages when needed.
Also, I'll probably be able to update the mirror every now and then so that machines can get access to the latest updates.
We also have a lot of computers on this local network and it would be nice to take advantage of LAN speeds instead of the slower internet speeds, so this will be an added performance bonus.

The catch is that I want this to be as easy as possible for machines to start using.
Preferably, _zero_ configuration on the host machines.
This means that I'll have to override the DNS to redirect the mirrorlist urls in repo files located in `/etc/yum.repos.d/`.
For example, the base repo will go to this mirrorlist url: `http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=stock`.
The epel repo will go here: `https://mirrors.fedoraproject.org/metalink?repo=epel-7&arch=x86_64`

### The Grind

Redirecting these addresses are no problem.
I know there are a lot of people who will say that this is a bad idea, and generally it is.
However, since this will be on an isolated network without internet, I don't think it'll be as big of a problem as some might say.
The DNS entries happily point people to my `mirror-server` virtual machine.

The only problem that comes up is that the epel repo wants to _https_ instead of plain old _http_.
In order to stop complaints about a self signed certificate I'll need to generate a new certificate request and send it to my FreeIPA server to get it signed.
Unfortunately, for the certificate to be issued, the hostname needs to match up with the hostname in the url.
I really don't want to name my machine, "mirrors.fedoraproject.org", so I'll need to use the _Subject Alternative Name_ (SAN) of certificates to say that my certificate is also valid for mirrors.fedoraproject.org.
You can try this with FreeIPA, but then it will complain about mirrors.fedoraproject.org not being a host.

So, now, getting to the point.
You'll need to create a fake host by the name of mirrors.fedoraproject.org.
Then you can generate your certificate using the `ipa-getcert` tool to request a certificate with SAN including _mirrors.fedoraproject.org_.
Here are the steps.

```
ipa host-add mirrors.fedoraproject.org --desc="A fake host for supporting yum repo redirection"
ipa-getcert request -r -f /etc/pki/tls/certs/mirror-server.example.com.crt -k /etc/pki/tls/private/mirror-server.example.com.key -N "CN=mirror-server.example.com" -D mirror-server.example.com -D mirrors.fedoraproject.org -K HTTP/mirror-server.exmaple.com
```

At this point, you should have the certificate and key stored in the locations provided on the ipa-getcert command.
After configuring apache, I was now able to serve https pages from mirrors.fedoraproject.org.
