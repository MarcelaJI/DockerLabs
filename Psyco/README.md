# Psyco Machine 🧠 

Difficulty: 🟢 Easy

🔗 You can download the machine here:
[https://dockerlabs.es/](https://dockerlabs.es/)

---

## 1. Deployment of the vulnerable machine

![machine](./images/1.png)

---

## 2. We send a ping to find out if the machine is active on the network.

![machine](./images/2.png)

---

## 3. Reconnaissance with nmap

![machine](./images/3.png)

Detailed explanation of each parameter:

- **-p-**: Scans all ports.
- **--open**: Show only open ports.
- **--min-rate=5000**: This parameter helps us control the speed of the packets sent and thus be able to perform a faster scan with 5000 packets per second.
- **-Pn**: It does not ping because it assumes the host is up..
- **-n**: It does not do DNS resolution.
- **-vvv**: Verbose mode, to view detailed information in real time.


Port 80 and port 22 are open. Now I'm going to scan those two ports to see what versions they're running behind.



![machine](./images/4.png)

We did not find any vulnerabilities in the versions.

---


We will take a look at the web page that is running on port 80

![machine](./images/5.png)

When reviewing the page, we noticed an error message at the end.

## Fuzzing with ffuf

Remember, the page returned an error, which suggests you may be trying to access a resource incorrectly.We can use ffuf to find out which extensions the page is using and then discover the vulnerable parameter(I put index because it is a default name on web pages).

![machine](./images/6.png)

We discovered that it uses phps and php extensions

![machine](./images/7.png)

![machine](./images/8.png)

We have discovered that the vulnerable parameter is "secret".

![machine](./images/9.png)

Two users appear here, luisillo and vaxei. With the SSH port active, we'll try to get the id_rsa file for one of them.

Luisillo doesn't have this file, but vaxei does, so we'll use it to try to access it.



