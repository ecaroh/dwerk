dwerk
=====

The data workers toolcase

# Preface #

dwerk is a collection of scripts making administring debian/linux systems easier. The acronym dwerk means: Datenarbeiters       WERKzeugkasten or Dataworkers WorkbEnch and Resource Kit in English.


# License #

dwerk suite is licensed under the terms of the GNU Public License Version 2
or any later version. You should have received a copy of that.

<http://www.gnu.org/licenses/gpl.txt>


# Todo #
 - add working directory detection in all scripts
 - add gpl license in all scripts (append it at the bottom?)
 - smartctl -i / smartctl -H integration
 - `sysctl -a ` integration
 - `vol_id /dev/hda` integration
 + unique host ID in root dir (handcrafted Textfile?

## Today ##
 + check consistency of headers
 - check documentation whether up to date # please consider Kivio Charts!
 + check function of scripts
 * detect work still to be done

# Introduction #

dwerk consists of a traditional unix style directory structure containing severals shell scripts. It can be extracted from      tarball, preferably under /opt. Some functionalty relies on other software.

The main goal of dwerk is to make sysadmins life simpler when administring more than three linux machines. To reach this        approach dwerk suite is limited to pure shell scripting with the optional help of external standard unix/linux tools. Dwerk     suite shall contain but is not limited to:

 + Documentation
  * dwerk_his.sh: Host quick and short self information like hostname, kernel, RAM, CPU, IP-Adress
  * dwerk_hid.sh: Hardware information
  * dwerk_hie.sh: System information like partitions, raid, lvm, network throught
  * dwerk_hic.sh: Configuration information from configuration files
  * dwerk_hip.sh: Installed packages
  * dwerk_hif.sh: Find all files beneath a given path and store it in a logfile
 + Monitoring
  * dwerk_hih.sh: Hardware health
  * System performance data # later catched via sar or something else
 + Configuration
  * Sync config from master server to clients, reload daemons
  * Helpers to get new hosts under dwerk suite control
 + Versioning
  * Version control for the above generated docs, logs and configuration
 + Backup
  * Backup scripts
   - dwerk_hbt.sh: Tape backup
   - dwerk_hbd.sh: Disk backup via storeBackup (optional disk encryption mount helper)
 + Helper Scripts
  * dwerk_hmm.sh: Mount helper for (encrypted) storage media

To make track of historical changes a version controll system and standard logrotate is used. The decision what will be used we distinguish between the type of data:
 * Static data (which will not change more than once in a while). This data should be versioned with a version control system.
 * Dynamic data (which may change for instance when changing a config file or adding a route). This should be handled by        logrotate.
 * Volatile data (such as cpu and ram usage), this belongs to either a monitoring system or a suite like sar. For volatile data we want to save once in a while the logrotate tool will be sufficient.


## Naming convention ##

Dwerk suite is named hierachical, so the purpose of a script can be more easy deduced from the name.

| First field | Second Field | Third field, first char |

NNNNND123.SUF   where * is

    N   =   dwerk (dwerk suite prefix)
    D   =   `_' divider
        1   =   suite target
            d   =   dwerk itself
            h   =   host
            l   =   library
            n   =   network
        2   =   suite action
            i   =   information
            s   =   self
            v   =   version control
            b   =   backup
        3   =   action target
            d   =   devices
    .   =   divider
    SUF =   shell script suffix

For instance for file dwerk_his.sh the meaning is:
dwerk host information short-summary
dwerk-suite-prefix divider-underscore host information short/summary devider-dot suffix

# dwerk dependencies #
 - OS: Debian (Others Linux dists and Unixes planed)
 - Depends: bash, awk, grep, mktemp, lshw, tar, gzip
 - Recommends: cron, lshw, mktemp, logrotate, rcs, bazaar,


# dwerk directories #

## dwerk config files ( in ./etc ) ##
dwerk.config        # main dwerk config file
dwerk_cron.config   # cron sample file
dwerk_hci.config    # dw hci config file
dwerk_hic.config    # dw hic config file
dwerk_lwd.cf        # ?
dwerk_dv.config     # dw host versioning config     # based on git vcs

## dwerk libs ( in ./lib ) ##
dwerk_le.cf         # dw lib environment
dwerk_lv.cf         # dw lib variables
dwerk_lf.cf         # dw lib functions, all common functions are defined here
dwerk_lp.cf         # dw lib plattform (sh-cmd_sunos, sh_cmd_linux, sh-cmd_aix ...)
dwerk_lt.cf         # dw lib for later language translations

## dwerk main scripts ( in ./bin ) ##
dwerk_bst.sh        # dw bin script template    # generic template for other dwerk bin/shell scripts

### dwerk self helpers ( in ./bin ) ###
dwerk_dsi.sh        # dw self initialize (source all lib files)
dwerk_dsc.sh        # dw self config (selfconfig of dwerk) ( generate structure )
dwerk_dsp.sh        # dw self packaging (for later debianizing dwerk suite)
dwerk_dhc.sh        # dw host config (i.e. Scripts for auto rewriting fstab)

### dwerk host info ###
dwerk_hii.sh        # dw host info id           # self assignment of unique id, based on `hostid' which gets hex value from     eth0 hw mac address
dwerk_his.sh        # dw host info short/sum    # hostname FQDN, os-version, kernel, device-class (Desktop/Server), ip, ram,    cpu num, storage total, disks,
dwerk_hid.sh        # dw host info devices  +   # static [vcs] udid: lshw :: cpuinfo, pci, usb, ide, scsi/sata, smartctl        modules, interrupts, ioports
dwerk_hie.sh        # dw host info environment  -   # dynamic [overwrite] udid: hostname, kernel, dist-release, disks,          partitions, raidstate, lvm, mounts, network, route, dns, nis, ,
dwerk_hic.sh        # dw host info configs  -   # static [vcs] udid: see section below `# Config files #'
dwerk_hip.sh        # dw host info packages     # static [vcs] udid: dpkg -l, dpkg --get-selections (TODO: RPM)
dwerk_hiv.sh        # dw host info volatile +   # [overwrite] uptime, last, free, who (sar?), ethtool, vmstat, vnstat  vmstat,  vnstat, hddtemp (update all 5 minutes)
dwerk_hih.sh        # dw host info health   -   # [overwrite] udid: hostname, smartctl, hddtemp
dwerk_hia.sh        # dw host info all          # [overwrite] cat all host info logs into one big log

### dwerk self versioning ###
dwerk_dvb.sh        # dw dwerk versioning bazaar    # obsolet in the begining - change to git
dwerk_dvr.sh        # dw dwerk versioning rcs   # based on rcs
dwerk_dvg.sh        # dw dwerk versioning git   # based on git vcs

### dwerk host backup ###
dwerk_hbt.sh        # dw host backup tape       # based on ? (stand)
dwerk_hbd.sh        # dw host backup disk       # based on storeBackup (stand)
dwerk_hbs.sh        # dw host backup system     # fsarchiver (partimage: auslaufend, systemimager: install, clonezilla:nein

### dwerk host config ###
dwerk_hcs.sh            # dw host config synctool       # based on synctool? (stand)
dwerk_hcp.sh            # dw host config packages       # based on debsync?
dwerk_hci.sh            # dw host config initial        # nis (users/groups), nfs mounts, apt sources.list,

### dwerk host helpers ###
dwerk_hcm.sh        # dw host crypt mount-media
dwerk_hcu.sh        # dw host crypt umount-media

dwerk_hmm.sh        # dw host mount media (unencrypted only)
dwerk_hmu.sh        # dw host umount media (unencrypted only)

### dwerk network * ###
dwerk_n*.sh         # dw network *


## dwerk log ( in ./log ) ##
$HOSTNAME_$YYMMDD-HHMM_dwerk_*.sh.log   # as long as not periodicaly managed by logrotate or versioned by vcs system
$HOSTNAME_dwerk_*.sh.log        # when periodialy rotated by logrotate or versioned by vcs system


## dwerk tmp ( in ./tmp ) ##


## dwerk doc ( in ./doc ) ##
dwerk.info          # info file - this file
dwerk_suite.flw     # process chart

# List of configuration files to consider when generating a documentation #
    fstab
    crypttab
    sysctl.conf
    inittab
    modules
    passwd
    group
    aliases
    network/interfaces
    networks
    hostname
    defaultdomain
    timezone
    resolf.conf
    nsswitch.conf
    mailname
    host.conf
    hosts
    inetd.conf
    yp.conf
    ypserv.conf
    ypserv.securenets
    crontab
    sudoers
    X11/xorg.conf

    mdadm/mdadm.conf
    lvm/lvm.conf
    apt/sources.list
    ssh/sshd_config

    /boot/menu.lst

                         
