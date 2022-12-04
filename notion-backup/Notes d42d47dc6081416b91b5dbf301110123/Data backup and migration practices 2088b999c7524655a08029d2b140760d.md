# Data backup and migration practices

# What secrets to migrate

- .ssh
- .plasmic-misc
    - Has SUPER IMPORTANT things like email save state, Substack importer credentials and timestamp,
- .plasmic*
- .aws/credentials
- .msmtprc
    - [https://wiki.archlinux.org/title/msmtp](https://wiki.archlinux.org/title/msmtp)
- proj/plasmic/
    - Has discourse-importer credentials, Slack annotator credentials, discourse sync state, etc.

```bash
# Set default values for all following accounts.
defaults
auth           on
tls            on
# tls_trust_file /etc/ssl/certs/ca-certificates.crt
tls_trust_file /opt/local/share/curl/curl-ca-bundle.crt
logfile        ~/.msmtp.log

# Gmail
account        gmail
host           smtp.gmail.com
port           587
from           yang@plasmic.app
user           yang@plasmic.app
password       PASSWORD
```

# What to regularly clean out from laptops

- App Data
    - DaVinci Resolve projects
    - chrome developer tools snippets
    - photo booth photos
    - torrents
- Projects
    - Run git-unsaved
- Files
    - Desktop, Documents, Downloads, Resilio Sync

# What to regularly clean out from Androids

- Files
    - Download/
    - Bluetooth/
- App Data
    - Chrome tabs
    - Shazam (if not signed in, any identified tracks)
    - Sound recorder
    - Az recorder
    - Signal

# When setting up a new phone

- Ensure you have: 2FA
- Enable: ActivityWatch, RescueTime, Google Fit

# When setting up a new laptop

- Enable: ActivityWatch, RescueTime
- Auto-start: ActivityWatch

# When migrating from a work domain

- Codebase
- Gerrit—increase the query limit ([source](https://stackoverflow.com/questions/51322413/how-to-change-the-limit-numbers-of-gerrit-query))
- Data (Postgresql)
- Jenkins setup
- G Suite
    - Including files shared to you—add them to your drive
- Lastpass
- Dropbox Paper
    - Including files shared to you—use dropboxpaper
- Slack
- Airtable
- Full Story

# Hardware issues to check for on new computer

- Bluetooth
- Camera
- Mic when using group Hangouts
- DisplayPort, HDMI port
- Audio jack
- USB ports
- SD card

# Data to migrate from laptops

See also **What to regularly clean out**

- app data
    - save games / custom maps (Serious Sam, Brutal Doom, Grid wars)
    - Dolphin Wii saved games
- home dir files
    - .ssh keys (to login to services without needing to reset instances)
    - VMs

# Data to migrate from Androids

See also **What to regularly clean out**

- App Data
    - AndOTP
    - Signal
- text messages (SMS Backup & Restore)