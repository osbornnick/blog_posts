**November 22nd 2019: osbornnick.com version 1 is launched!**

This website is my personal repository. It is a place for coding projects, as well as anything else easily communicated with ones and zeros.  Perhaps one day it will be something else, but for now, that is what it is. For whatever reason you're here, I hope you find what you're looking for!

## The Setup, for Now

Now: 11/22/2019

Osbornnick.com is built using Django and a PostgreSQL database. It runs on Ubuntu 18.04 installed on a [DigitalOcean][1] droplet. The application interfaces with a [Gunicorn][2] WSGI server and uses [Nginx][3] to reverse proxy to Gunicorn. I chose Gunicorn because of its popularity and abundance of tutorials, and Nginx to take advantage of its high performance connection handling mechanisms and its easy-to-implement security features.

You can find the source code for the site [here][4].


[1]: https://www.digitalocean.com/ "DigitalOcean"
[2]: https://gunicorn.org/ "Gunicorn"
[3]: https://www.nginx.com/ "Nginx"
[4]: https://github.com/osbornnick/osbornnick "github link"
