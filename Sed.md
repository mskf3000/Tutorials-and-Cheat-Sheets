## Sed

By Jack Szwergold

### Comment out all uncommented lines in a file including empty lines.

	sed -e '/^#/!s/\(.*\)/# \1/g' /etc/update-motd.d/90-updates-available
	sed -e '/^#/!s/\(.*\)/# \1/g' /etc/update-motd.d/91-release-upgrade
	sed -e '/^#/!s/\(.*\)/# \1/g' /etc/update-motd.d/95-hwe-eol

### Comment out all uncommented lines in a file excluding empty lines.

	sed -e 's/^\([^#].*\)/# \1/g' /etc/update-motd.d/90-updates-available
	sed -e 's/^\([^#].*\)/# \1/g' /etc/update-motd.d/91-release-upgrade
	sed -e 's/^\([^#].*\)/# \1/g' /etc/update-motd.d/95-hwe-eol

### Stuff that somehow didn’t work in the Vagrant provisioning scripts.

    # Comment out the content of these MOTD scripts.
    if [ -f "/etc/update-motd.d/90-updates-available" ]; then
      sudo sed -i 's/^\([^#].*\)/# \1/g' "/etc/update-motd.d/90-updates-available";
    fi

    if [ -f "/etc/update-motd.d/91-release-upgrade" ]; then
      sudo sed -i 's/^\([^#].*\)/# \1/g' "/etc/update-motd.d/91-release-upgrade";
    fi

    if [ -f "/etc/update-motd.d/95-hwe-eol" ]; then
      sudo sed -i 's/^\([^#].*\)/# \1/g' "/etc/update-motd.d/95-hwe-eol";
    fi

    # MOTD_90_PATH="/etc/update-motd.d/90-updates-available"
    # sudo echo $(awk '$0 && $0 !~ /^#/ {printf "# "}1' "${MOTD_90_PATH}") > "${MOTD_90_PATH}";
    # sudo gawk -i inplace '$0 && $0 !~ /^#/ {printf "# "}1' "${MOTD_90_PATH}"

***

*Sed (c) by Jack Szwergold; written January 24, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*