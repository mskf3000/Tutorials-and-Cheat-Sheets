# MailCatcher

By Jack Szwergold, September 14, 2015

For email testing, you can use MailCatcher which is a great tool which runs a  simple SMTP server which catches any message sent to it to display in a web interface. Excellent for email process debugging. To install the Ruby GEM for it run this command:

    sudo gem install mailcatcher

Now, uninstall the currently installed `i18n` GEM—if it is already installed—due to incompatibility issues:

    sudo gem uninstall i18n

And reinstall it as the stable, `0.6.11` version:

    sudo gem install i18n -v 0.6.11

Then run it like this to bind to `192.168.56.10`:

    mailcatcher --http-ip=192.168.56.10

Or run it like this to bind to any port on the host:

    mailcatcher --http-ip=0.0.0.0

Setting your code to send SMTP mail to port `1025` on `localhost` will send it to MailCatcher. And to view mails, just go to port `1080` on that server via a web browser like this:

    http://sandbox.local:1080

***

*MailCatcher (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*