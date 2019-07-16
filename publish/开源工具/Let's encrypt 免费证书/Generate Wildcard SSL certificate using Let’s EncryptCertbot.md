---
title: Generate Wildcard SSL certificate using Let’s Encrypt/Certbot
date: 2019/7/16 10:57:0
updated: 2019/7/16 10:57:0
comments: true
tags:
- Wildcard Ssl
- Lets Encrypt
- Certbot
- Free Ssl Certificate
- Ssl Certificate
categories:
- tool kits
---

# Generate Wildcard SSL certificate using Let’s Encrypt/Certbot

From past few days or months, everyone on the World Wide Web is talking about SSL certificates and rushing to implement them. **But Why?** Cause recently [google has announced](https://blog.chromium.org/2017/04/next-steps-toward-more-connection.html), if your website, webpages or web-applications does not have SSL certificate then Chrome will label them as Non-Secured.

![](http://img-note.wuqianlin.cn/img-md/2019-07-16-025141.jpg)

In this blog will cover, how to generate a wildcard SSL certificate for your domain using Certbot. I am generating certificate for the domain **erpnext.xyz**

![](http://img-note.wuqianlin.cn/img-md/2019-07-16-025209.jpg)

## Step 1: Setup Pre-requisites

If you already have a droplet or a system then make sure your system have **Python 2.7 or 3** and **git** installed on it. As I am starting on fresh Ubuntu droplet, we have to setup the above pre-requisites.

```
apt-get update
apt-get install python-minimal
python --version
apt-get install git-core
git --version
```



## Step 2: Setup Certbot

After setting up the pre-requisites, now will setup the Certbot via github.

```
cd /opt
git clone https://github.com/certbot/certbot.git
cd certbot && ./certbot-auto
```

While installing the Certbot, I came across the error

```
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/virtualenv.py", line 2363, in <module>
    main()
  File "/usr/lib/python3/dist-packages/virtualenv.py", line 719, in main
    symlink=options.symlink)
  File "/usr/lib/python3/dist-packages/virtualenv.py", line 988, in create_environment
    download=download,
  File "/usr/lib/python3/dist-packages/virtualenv.py", line 918, in install_wheel
    call_subprocess(cmd, show_stdout=False, extra_env=env, stdin=SCRIPT)
  File "/usr/lib/python3/dist-packages/virtualenv.py", line 812, in call_subprocess
    % (cmd_desc, proc.returncode))
OSError: Command /opt/eff.org/certbot/venv/bin/python2.7 - setuptools pkg_resources pip wheel failed with error code
```

After googling, I came to know, the error triggered due to improper locale variables. Set the locale variables and re-run.

```
export LC_ALL="en_US.UTF-8"
export LC_CTYPE="en_US.UTF-8"
```

You can also install the Certbot via apt installer.

```
apt-get install letsencrypt
```



## Step 3: Generate The Wildcard SSL Certificate

Now with the help of Certbot will generate wildcard certificate for our test domain **erpnext.xyz**

# Step 3: Generate The Wildcard SSL Certificate

Now with the help of Certbot will generate wildcard certificate for our test domain **erpnext.xyz**

![](http://img-note.wuqianlin.cn/img-md/2019-07-16-025342.jpg)

**Note: As we are generating wildcard ssl certificate, mention domain with \* i.e. \*.erpnext.xyz**



## Step 4: Authenticate The Domain’s Ownership

For wildcard certificates, the only challenge method Let’s Encrypt accepts is the DNS challenge, which we can invoke via the **preferred-challenges=dns** flag.

After executing the above command, the Certbot will share a text record to add to your DNS.

```
Please deploy a DNS TXT record under the name
_acme-challenge.erpnext.xyz with the following value:J50GNXkhGmKCfn-0LQJcknVGtPEAQ_U_WajcLXgqWqo
```

**Record Name**: _acme-challenge
**Record Value**: J50GNXkhGmKCfn-0LQJcknVGtPEAQ_U_WajcLXgqWqo

Create TXT record via DNS console and setup key and value

![](http://img-note.wuqianlin.cn/img-md/2019-07-16-025425.jpg)


## Step 5: Get The Certificate

Once you authenticate the domain ownership; by cleaning up dns challenges, Certbot generates the ssl certificate and required keys.

![Congratulations!!! You have wildcard SSL certificate](http://img-note.wuqianlin.cn/img-md/2019-07-16-025450.jpg)

Congratulations!!! You have successfully generated wildcard SSL certificate for your domain.



## Step 6: Cross Verify The Certificate

To cross verify certificate’s validity via command line run

```
./certbot-auto certificates
```

![](http://img-note.wuqianlin.cn/img-md/2019-07-16-025450.jpg)



**原文**：https://medium.com/@saurabh6790/generate-wildcard-ssl-certificate-using-lets-encrypt-certbot-273e432794d7