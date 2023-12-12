# Troubleshooting

# Setup
For this demonstration, we will need to have a web server, in our case it was hosted in another VM:

```
sudo apt install nginx
sudo systemctl start nginx
```

And in our Kali VM, let's install a DDoS tool, slowloris: `git clone https://github.com/gkbrk/slowloris.git`.

Now, let's run it against our web server's IP with `python 3 slowloris.py 192.168.0.8 -s 5000`. Going to an incognito tab, we will not be able to reach the website anymore (it will probably still work for your regular tab because of caching).

# Identifying the Attack
Users reporting problems to connect to the websites, or long times to load a page are signs that a DDoS attacked could be being executed. Also, network tools could give you a better understanding.

Here, we simply decided to investigated nginx's access logs `tail -n 10000 /var/log/nginx/access.log | awk '{print $2}' | sort | uniq -c | sort -nr`:

```
    84609 192.168.1.105
    62936 182.168.0.9
       62 192.168.0.5
        1 192.168.1.104
```

This shows us almost 150k connections from only 2 IPs.

# Containment and Prevention
For this scenario, we decided to stick to this script (https://github.com/anti-ddos/Anti-DDOS) that will perform basic security configuration to our server.
