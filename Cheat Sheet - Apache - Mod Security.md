# Cheat Sheet - Apache - Mod Security

By Jack Szwergold, October 1, 2015

***

### Installing Mod Security.

Install the Apache `mod_security` module and related items:

    sudo aptitude install libapache2-modsecurity modsecurity-crs

Enable the Apache `mod_security` module:

    sudo a2enmod mod-security

Enable the Apache `headers` module:

    sudo a2enmod headers

If you have to disable the the Apache `mod_security` module, this is how you do it:

     sudo a2dismod mod-security

Do some simple directory checks to make sure it’s installed:

	ls -la /usr/lib/apache2/modules/
	ls -la /etc/apache2/mods-available/
	ls -la /etc/apache2/mods-enabled/

Do some simple configuration checks to make sure it’s installed:

	sudo nano /etc/apache2/mods-available/mod-security.conf
	sudo nano /etc/apache2/mods-available/mod-security.load

#### Setting up the main Apache Mod Security config.

Now create an active config (`000-modsecurity.conf`)file based on the recommended config (`modsecurity.conf-recommended`):

    sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/000-modsecurity.conf

This is the custom config file (`999-tweaks_to_modsecurity.conf`) we can make adjustments to for overall server-wide Mod Security settings:

	sudo nano /etc/modsecurity/999-tweaks_to_modsecurity.conf

And here are the contents of `999-tweaks_to_modsecurity.conf`:

	<ifmodule mod_security2.c>
	
	  # Activate the rules engine. (On|Off|DetectionOnly)
	  # SecRuleEngine DetectionOnly
	  SecRuleEngine On
	
	  # Simple security rule for testing
	  # SecRule REQUEST_URI "/foobar"
	
	  # Set audit logging to the bare minimum.
	  SecAuditEngine RelevantOnly
	  # SecAuditLogParts ABCFHZ
	  SecAuditLogParts AHZ
	
	  # Set request body limit to 64MB in bytes (67108864); 32MB (33554432)
	  SecRequestBodyLimit 67108864
	  SecRequestBodyNoFilesLimit 67108864
	
	  # Allow specific IP addresses access without mod_security filtering.
	  SecRule REMOTE_ADDR "^127\.0\.0\.1$" "phase:1,nolog,allow,ctl:ruleEngine=Off,ctl:auditEngine=Off"
	
	  # Load the rules for mod_security
	  Include /usr/share/modsecurity-crs/*.conf
	  # Include /usr/share/modsecurity-crs/base_rules/*.conf
	  # Include /usr/share/modsecurity-crs/optional_rules/*.conf
	  Include /etc/modsecurity/activated_rules/*.conf
	
	</ifmodule>

#### Setting up the Apache virtual host specific Mod Security config.

This is a more virtual-host specific Mod Security config file. Note that `SecAuditEngine On` needs to be set to `SecAuditEngine Off` once testing is done:

    sudo nano /etc/apache2/sites-available/common_mod_security.conf

And here are the contents of `common_mod_security.conf`:

    # 2012-10-15: Added to granularly control Mod Security on this specific virtual host.
    <ifmodule mod_security2.c>

      # Exclude the URL locations AWStats, Munin & phpMyAdmin from mod_security checks
      <LocationMatch /(awstats|munin|phpmyadmin)>
        SecRuleEngine Off
      </LocationMatch>
    
      SecRuleInheritance On
      SecRuleEngine On
      SecAuditEngine On

      SecRuleRemoveById 960015
      SecRuleRemoveById 950901
      SecRuleRemoveById 958030

    </ifmodule>

#### Setting up the Apache virtual host config for Mod Security.

Then make sure your Apache virtual host config points to it like this. First open up the Apache virtual host config:

    sudo nano /etc/apache2/sites-available/default 

And make sure a line like this is set to have it load the `common_mod_security.conf` we have setup above:

    # 2014-03-12: Including common ModSecurity related items.
    include /etc/apache2/sites-available/common_mod_security.conf

### Activating Mod Security rules.

Create the `activated_rules` directory if it doesn’t already exist:

    sudo mkdir -p /etc/modsecurity/activated_rules

If you need to remove the `activated_rules` just dump the whole directory like this:

    sudo rm -rf /etc/modsecurity/activated_rules

#### Activate the Mod Security `base_rules`.

	cd /etc/modsecurity/activated_rules/
	
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_40_generic_attacks.data .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_41_sql_injection_attacks.data .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_20_protocol_violations.conf .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_23_request_limits.conf .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_30_http_policy.conf .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_40_generic_attacks.conf .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_41_sql_injection_attacks.conf .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_41_xss_attacks.conf .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_47_common_exceptions.conf .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_49_inbound_blocking.conf .
	sudo ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_60_correlation.conf .

#### Activate the Mod Security `optional_rules`.

	cd /etc/modsecurity/activated_rules/
	
	sudo ln -s /usr/share/modsecurity-crs/optional_rules/modsecurity_42_comment_spam.data .
	sudo ln -s /usr/share/modsecurity-crs/optional_rules/modsecurity_crs_10_ignore_static.conf .
	sudo ln -s /usr/share/modsecurity-crs/optional_rules/modsecurity_crs_13_xml_enabler.conf .
	sudo ln -s /usr/share/modsecurity-crs/optional_rules/modsecurity_crs_42_comment_spam.conf .
	sudo ln -s /usr/share/modsecurity-crs/optional_rules/modsecurity_crs_47_skip_outbound_checks.conf .
	sudo ln -s /usr/share/modsecurity-crs/optional_rules/modsecurity_crs_55_application_defects.conf .

#### Getting a count of various Mod Security rules.

Check how many `base_rules` are available:

	ls -la /usr/share/modsecurity-crs/base_rules/ | wc -l

Check how many `optional_rules` are available:

	ls -la /usr/share/modsecurity-crs/base_rules/ | wc -l

Check how many rules are activated:

	ls -la /etc/modsecurity/activated_rules/ | wc -l
	
### De-activating Mod Security rules.

I’ve removed these in the past for being overeager or too destructive in their approach to handling requests on some sites:

#### De-activated the Mod Security `base_rules`.

	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_35_bad_robots.data
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_35_scanners.data
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_50_outbound.data
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_50_outbound_malware.data
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_21_protocol_anomalies.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_35_bad_robots.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_42_tight_security.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_45_trojans.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_48_local_exceptions.conf.example
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_50_outbound.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_59_outbound_blocking.conf

#### De-activated the Mod Security `optional_rules`.

	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_11_avs_traffic.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_16_authentication_tracking.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_16_session_hijacking.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_16_username_tracking.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_25_cc_known.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_40_experimental.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_43_csrf_protection.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_46_av_scanning.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_49_header_tagging.conf
	sudo rm -f /etc/modsecurity/activated_rules/modsecurity_crs_55_marketing.conf

### Sundry Mod Security stuff.

Various ways to follow the logs:

	sudo tail -f -n 200 /var/log/apache2/error.log
	
	sudo tail -f -n 200 /var/log/apache2/modsec_audit.log
	
	sudo tail -f -n 8000 /var/log/apache2/modsec_audit.log | grep 'User-Agent:'
	
	sudo tail -f -n 10000 /var/log/apache2/modsec_audit.log | grep "GET /"

Items connected to audit logs:

    ls -lah /var/log/apache2/modsec_audit*

    sudo rm /var/log/apache2/modsec_audit*

***

*CCheat Sheet - Apache - Mod Security (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

