# Obscured-Files-File-Host-Source-Code
Source code for the Obscured Files clearnet/hidden service file host preserved here. Following message by original author CodeFate

The Official Source Code of Obscured Files. A distributed file host built on an equal footing share-nothing architecture enabling near limitless scalability. Every node acts independently to each other allowing for fallover and automatic recovering if a node were to go down. It has seamless integration for additional nodes with crate and peervpn.

This is not a file host with a simple setup. There is a lot of variables and many preferences will need to be changed and dependencies installed for your own server compatibility. But the basis is there.

We’ve been trying to simplify the install and configuration process making it as easy as possible to understand how this complicated file host works without waving a wand and saying “magic.”

Due to the dirty setup design, there is a large potential for errors. Chances are you will experience some bugs as well. Troubleshooting is required. You have been warned.
Prerequisites

Obscured Files has near limitless scalability with automatic healing. That means every node is independent from each other and has the capability to operate fully even if only one node is online. Each node requires all the dependencies and proper configurations to operate in the cluster.

To give a rundown of what every server will need: PHP7.0, ImageMagick, Crate, PeerVPN, OpenSSL, Tor, Nginx. All of which requires many other dependencies. Most of the dependencies require certain configurations to operate the site properly. The recommended configurations have been included in the repo/archive.

You will need (at least) two servers with a clean install of Debian Jessie (8.0) and a valid internet connection. Both of the servers will require an IP address which is accessible, at least, inside the cluster.

Obscured is designed to have both a clearnet and hidden service side. This means each individual server will need to have a clearnet sub-domain. While you can try to trade the sub-domain for just the node’s IP it’s not recommended that you do so.

While we will be releasing the complete source on system33’s git you can optionally download an archive of the git here.
Setup Helper

The “setup” script will install and configure all the dependencies in a very dirty way. Look at it. At the top, you should see all the software versions. It is very important you make sure all the software versions are up to date for each dependency. The script is dirty and might not work in all cases. The outline of what it does is below:

    Configures Nginx with Pagespeed and two upload improvement modules.
    OpenSSL custom complied to support __uint128_t making ECDH (which is used heavily in tor) about 2 to 4 times faster.
    Tor is complied with the above OpenSSL and configured in single hop mode with 10 introduction points.
    Creates a 5120MB tmpfs to store sessions, caches, and non-completed file uploads.
    PHP is configured with multiple things disabled and web directory jailed using open_basedir.
    Crate is configured with its required schema and configuration options.
    PeerVPN is configured to introduce itself to other systems in your cluster.
    The required directories are created and configurations put in the correct place.
    Other things (just check the script)
    Wraps things up with a little bow, and shoots it into the sun.

Preparing the Template

If you got it from git:
After you have your new servers booted up for the first time, login to root and download the git in your root directory. Move the git so that the setup script and all the folders are directly under /root/ and not within the created git directory.

If you got it from the archive:

After you have your new servers booted up for the first time, login to root and extract the archive in your root directory. Make sure the setup script and all the folders are directly under /root/ and not within the created archive directory (if there is one).
Cluster Template

Now you will need to change a little bit of the configurations for your own use. You should see a file named cluster.json. Open it up and edit the IP addresses/url portions for your own servers. This file will be used as a template to correctly connect all your systems together.

Go inside /cluster/config_peervpn.php and change the PSK to another secure string. This is used to authenticate and encrypt information transfers within the cluster. Don’t change the port number.

You will need to update www/www/application/config/cluster.php with your own cluster’s ips, Clearnet sub addresses, and onion addresses. If you don’t have an onion address just put null and the site will automatically adjust. In the same folder, you will need to edit config.php and update the encryption_key with your own secure string. In the same folder, you will need to edit uploads.php’s DELETE_HMAC_KEY, ABUSE_USER, ABUSE_PASS for your own configuration options. At the same time update the default_clear_url and default_onion_url to your own site’s main addresses.

Google Analytics is loaded in two different points. The only JavaScript on the site under web directory public/analytics.js and web directory back/analyticstracking.php you can remove these or edit the analytics IDs.

You now have your own Cluster template! Archive what you have and extract it on any other cluster node to have a super-fast cluster expand.
Installation

So, you now have that shiny Cluster Template archive, you should now transfer it to each of your cluster nodes. This way all the encryption keys are the same so every node will communicate in the same language.

Extract it out into root and boom you are already 70% done with the node setup and configuration.

First things first, open up nginx/conf.d/default.conf and edit the file updating all the required information. Remember to edit the public-key-pins for your own SSL configuration. The onion service runs on port 5620 and it’s configured that way in the tor torrc. It is highly recommended you get a domain name and configure every node with their own sub-domain. SSL is also highly recommended and you can do that, free of charge, with Lets Encrypt. If you don’t do these two things you will need to change some site code.

It’s recommended to generate your own onion hostname with Scallion. Once you have an onion you’re happy with, replace the dummy hostname and private_key under tor/hidden_service/.

You will now need to update www/www/application/config/site.php with your nodes information. Just fit the outlined template. Don’t worry about the offline, upload_offline, or balanced options; you can play around with those later once the site is up.

Now it’s recommended to double check you have all your configuration options correct before doing the next step. 

The last thing you will need to do is run the setup script. This will move all the appropriate folders to where they need to be, install and configure everything as recommended. If everything goes well the site will be running on your server’s sub-domain and fully functional. We don’t expect this to happen on the first try, do to the large room for error, but we are hopeful.

This should be enough to get most started with Obscured File’s Source Code. More posts will follow addressing some install issues and possible solutions, as well as administration instructions. Look forward to it.
License

Obscured’s Source is Licensed under the MIT Licence.
