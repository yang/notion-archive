# Mac setups

- Apps to install on new Macbooks
    - App store
        - MuteKey
    - Homebrew
        
        ```bash
        # minimum, e.g. on TV computer
        xargs brew install --cask << "EOF"
        aldente
        alt-tab
        discretescroll
        google-chrome
        google-drive
        mullvadvpn
        qbittorrent
        vlc
        
        # development
        xargs brew install --cask << "EOF"
        blackhole-2ch
        cdktf
        cyberduck
        descript
        docker
        homebrew/cask-versions/figma-beta
        flux
        gitup
        intellij-idea
        iterm2
        kap
        keycastr
        rescuetime
        send-to-kindle
        visual-studio-code
        
        # more apps
        cat << "EOF"
        cloudflare/cloudflare/cloudflared
        dropbox
        firefox-nightly
        handbrake
        keka
        notion
        shadow
        signal
        steam
        syncthing
        virtualbox
        virtualbox-extension-pack
        
        # archived
        activitywatch
        adobe-digital-editions
        android-studio
        fluxcd/tap/flux
        obs
        skype
        xquartz
        code42-crashplan
        fanny
        karabiner-elements
        homebrew/cask-drivers/steelseries-exactmouse-tool
        lens
        zoom
        EOF
        ```
        
    - Macports
        
        ```bash
        { sed 's/#.*//' | while read line; do sudo port install $line; done ; } << "EOF"
        bash
        bat
        broot
        cheat
        choose
        curl-ca-bundle # https://github.com/pypa/pip/issues/4205#issuecomment-360758571
        coreutils 
        difftastic
        entr
        exa
        fd
        findutils
        flyctl
        fzf
        gdu
        gh
        git-delta
        git-review
        htop
        jq
        kubectl
        moreutils
        msmtp
        num-utils
        osxutils
        pcre # pcregrep
        pcre2 # pcre2grep
        pipx
        postgresql15 # TODO update version
        postgresql15-server # TODO update version
        procs
        pstree 
        pv
        py-awscli2 # py310-awscli2
        realpath
        ripgrep
        sd
        steampipe
        stern
        terraform
        trash
        zoxide
        
        # more monitoring tools
        bottom
        glances
        gotop
        nnn
        zenith
        
        # more/archived tools
        dua-cli
        dust
        fx
        pstree
        ranger
        scc
        pkgconfig # to build other stuff like postgresql
        ffmpeg 
        wget +ssl
        cliclick
        croc
        eksctl
        fswatch
        graphviz
        grsync
        helm-3.6 # check latest version
        helmfile
        ntfs-3g
        kdiff3
        nq
        rubber
        sshfs
        scc
        tree
        vim +cscope+huge+python27+python35
        watchman # needed for yarn test https://github.com/facebook/create-react-app/issues/4540
        youtube-dl +ffmpeg+python27
        yt-dlp
        nss
        mkcert
        stunnel
        openjdk11 # check latest LTS version: https://adoptopenjdk.net/support.html
        p5-image-exiftool
        py-protobuf
        python +readline
        python39 +readline # TODO update version (py39-virtualenvwrapper might not exist yet, so might want to stick to 38, or you can do: pip install --user virtualenvwrapper)
        py39-pip
        py39-virtualenvwrapper
        EOF
        ```
        
        - First [enable installing binaries](https://trac.macports.org/wiki/BinaryArchives) by editing `/opt/local/etc/macports/macports.conf`:
            
            ```bash
            # When MacPorts should build ports from source.
            # - ifneeded: Download binary archives if available; build from source
            #   otherwise.
            # - always: Always build from source; never try fetching archives.
            # - never: Never build from source; try fetching archives and abort if
            #   unavailable.
            buildfromsource       ifneeded
            ```
            
        - See what you requested installed:
            
            ```bash
            port installed requested
            ```
            
        - 
    - npm
        
        ```bash
        concurrently
        git-unsaved
        github-email
        tldr
        vercel
        yarn
        ```
        
- Install or configure manually
    - chsh: `sudo chsh -s /opt/local/bin/bash yang`
    - git-redate
    - barrier: [https://github.com/debauchee/barrier/issues/1609#issuecomment-1066938159](https://github.com/debauchee/barrier/issues/1609#issuecomment-1066938159)
        - As a workaround, you can manually create a certificate for it to use.
        - In a terminal on the MacOS host:
        - cd /Users/USERNAME/Library/Application Support/barrier/SSL
        - openssl req -new -x509 -sha256 -days 999 -nodes -out Barrier.pem -keyout Barrier.pem
    - bash-it
        - bash-it enable completion git ssh
        - bash-it enable alias git osx vim
    - crashplan (MIT)
    - **Dolphin: Tools > Export Wii Saves**
    - [font-bundles](https://github.com/keremciu/font-bundles) (icon fonts)
    - flux
    - [https://linearmouse.org/](https://linearmouse.org/)
    - [macos-fn-toggle](https://github.com/jkbrzt/macos-fn-toggle) (unless using fluor, which may be needed for newer OS? - note, neither works with touch bar!)
    - macports (to list: port installed requested)
        - Do NOT install the following, which are problematic:
            - google-cloud-sdk (didn’t work, but also [this](https://stackoverflow.com/questions/42697026/install-google-cloud-components-error-from-gcloud-command))
            - virtualbox (outdated)
        - Postscript:
            - sudo port select --set python python39
            - sudo port select --set virtualenv virtualenv39
            - [https://stackoverflow.com/questions/28405556/how-to-install-postgresql-on-osx-yosemite-using-macports](https://stackoverflow.com/questions/28405556/how-to-install-postgresql-on-osx-yosemite-using-macports)
                - sudo port select postgresql postgresql13
                - sudo mkdir -p /opt/local/var/db/postgresql13/defaultdb
                - sudo chown postgres:postgres /opt/local/var/db/postgresql13/defaultdb
                - sudo su postgres -c '/opt/local/lib/postgresql13/bin/initdb -D /opt/local/var/db/postgresql13/defaultdb'
                - sudo port load postgresql13-server
        - See script at bottom
    - [https://mounty.app/](https://mounty.app/)
    - Office
    - PlayMemories (includes Wireless Auto Import - sony wireless auto importer is part of the general a6000 camera software suite)
    - SkyFonts
    - Slate
    - sourcetree
    - soundflower (if using old OS)
    - SoundflowerBed (if using old OS)
    - [Spotify bluetooth headset listener](https://github.com/jguice/mac-bt-headset-fix)
    - Steam
    - virtualbox: may fail installation the first time around, so you need to [allow in system preferences](https://medium.com/@DMeechan/fixing-the-installation-failed-virtualbox-error-on-mac-high-sierra-7c421362b5b5)
- Configs
    - cron
    - OS X
        - Change default keyboard shortcuts:
            - Mission Control left/right spaces from control to control command, since IntelliJ uses control-left/right
            - disable cmd+shift+a
        - Remap keyboard modifier keys
    - Blackhole MIDI
        - You must set drift correction! On the “non-source-clock devices” which I think means the Blackholde device? And only needed on the multi-output and not the aggregate input? This seems to work:
            
            [https://lh3.googleusercontent.com/5GS5yiAtlCY_F8ct0kRiH-wWWNlQlpw8cSTzYr-8j2uHkv0W0rZVYjr6EvbEySFJiv7x0zjEckSzpgnZpTiTSv5hqoQJS8keCFORsA88eODmXQ9UTLYiMJfNmVhvVGRBKUcgZxWfT9M1eUqpAXzF9bSASL-3ZW7_3yArLU3F-Fj2R_MQZ4HHvwUepP5NJw](https://lh3.googleusercontent.com/5GS5yiAtlCY_F8ct0kRiH-wWWNlQlpw8cSTzYr-8j2uHkv0W0rZVYjr6EvbEySFJiv7x0zjEckSzpgnZpTiTSv5hqoQJS8keCFORsA88eODmXQ9UTLYiMJfNmVhvVGRBKUcgZxWfT9M1eUqpAXzF9bSASL-3ZW7_3yArLU3F-Fj2R_MQZ4HHvwUepP5NJw)
            
            [https://lh6.googleusercontent.com/2B79lcCprIYJy6ky_3wnKzpPTqGSkecEE0CG52Te4EyU1FbHsaMXooOilU_y5eDF9guL7YaLVcjlkfq7bHyu3MNMlcXxzlMLa1A3CNV2uiRxWayABBtRoHCxTqDvx35Er5jrgZFeiVW-6XZnTveOstwx1jW2MLXHjo7DE-SoRZgl3GsSgIiwm_I_nzibTw](https://lh6.googleusercontent.com/2B79lcCprIYJy6ky_3wnKzpPTqGSkecEE0CG52Te4EyU1FbHsaMXooOilU_y5eDF9guL7YaLVcjlkfq7bHyu3MNMlcXxzlMLa1A3CNV2uiRxWayABBtRoHCxTqDvx35Er5jrgZFeiVW-6XZnTveOstwx1jW2MLXHjo7DE-SoRZgl3GsSgIiwm_I_nzibTw)
            
    - Chrome
        - [https://github.com/iamadamdev/bypass-paywalls-chrome](https://github.com/iamadamdev/bypass-paywalls-chrome)
        - After The Deadline: as of 2016-07-09, breaks Google Sheets keyboard usage
    - VS Code
        - defaults write com.microsoft.VSCode ApplePressAndHoldEnabled -bool false # For VS Code
        - sync plugin, auth to github, then alt+shift+d
    - intellij idea
        - .ignore
        - BashSupport Pro
        - CSV
        - String Manipulation
        - not IdeaVim: [ctrl-left/right don’t work](https://youtrack.jetbrains.com/issue/VIM-1490)
        - Makefile
        - MDX
        - Prettier
        - Python
        - Quokka
        - Rust
        - [https://plugins.jetbrains.com/plugin/10131-tabmover](https://plugins.jetbrains.com/plugin/10131-tabmover)
        - Wrap to Column
    - iTerm
        
        [https://lh5.googleusercontent.com/ee4P8ue8GcxNUtRrywA6xcV52uu6rY0gagJHv3OajXNTQAp7EqNTK3DF7g9E6_9sdDAJexiCICGUzF4c_-XndafAQc3XImVAIghECc42S995fHNZTX25Um-pj_arW6EkUFoiILfM2Az_9e1pJlbvXI-6iYjmoTMTQpAhcpdapYwmXt_KQqXOHVBfVYKp](https://lh5.googleusercontent.com/ee4P8ue8GcxNUtRrywA6xcV52uu6rY0gagJHv3OajXNTQAp7EqNTK3DF7g9E6_9sdDAJexiCICGUzF4c_-XndafAQc3XImVAIghECc42S995fHNZTX25Um-pj_arW6EkUFoiILfM2Az_9e1pJlbvXI-6iYjmoTMTQpAhcpdapYwmXt_KQqXOHVBfVYKp)
        
    - Karabiner for [macvim right alt as right ctrl](https://stackoverflow.com/questions/15248495/macvim-remapping-alt-key-as-ctrl-key) and numeric keypad
        
        [https://lh5.googleusercontent.com/D1w4YCiczBKmVWG30tVDj7oKPyKSeMaFcontIK7tFF_BXTEZWGj1z7ax1xeICoB-tCQIaVblPMKKaRBDPwuzA8Gj4AhQH_F9TWj-sXX_Fxhp0vwXyXX0IW8y8psjaG_zca-S9O6dVBzJcy1UwFufVTRmLJF6qN5Iq8N3QW-qEs0ge7CnFOosi9QjWqGD](https://lh5.googleusercontent.com/D1w4YCiczBKmVWG30tVDj7oKPyKSeMaFcontIK7tFF_BXTEZWGj1z7ax1xeICoB-tCQIaVblPMKKaRBDPwuzA8Gj4AhQH_F9TWj-sXX_Fxhp0vwXyXX0IW8y8psjaG_zca-S9O6dVBzJcy1UwFufVTRmLJF6qN5Iq8N3QW-qEs0ge7CnFOosi9QjWqGD)
        
        [https://lh5.googleusercontent.com/4cOdXl3ndtF5jSNeRqut7BCpVKUcyXxmgrXsICkscJtpCJYCKhHSCLwZxSVRsxWVubuR4y_awepukMbBYeKKUvpxV9dUDk1ojAXVHuXdz1pADPTrK5yebN2oS2WU3rYqyK6DbQucajxROlcl17Iz4msSKWzCFxuvDrmdg1Gfv0Vd-j7M1kjFeYBVdefp](https://lh5.googleusercontent.com/4cOdXl3ndtF5jSNeRqut7BCpVKUcyXxmgrXsICkscJtpCJYCKhHSCLwZxSVRsxWVubuR4y_awepukMbBYeKKUvpxV9dUDk1ojAXVHuXdz1pADPTrK5yebN2oS2WU3rYqyK6DbQucajxROlcl17Iz4msSKWzCFxuvDrmdg1Gfv0Vd-j7M1kjFeYBVdefp)