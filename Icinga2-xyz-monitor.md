> This is a scrubbed example of a README document I wrote for a university's Icinga2 repository hosted on Gitlab. My first project with this university was migrating their legacy Icinga server over to Icinga2.

# xyz-monitor

[XZY-Monitor](https://xzy-monitor.xyz.edu) is a monitoring server running Icinga2. The entire **configuration is code**. Please see the next section called **Important Folders and Files** for an explanation of the important paths. 

# Important Folders and Files

|Directory or File|Path in xyz-monitor|Comment|
|---|---|---|
|Icinga 2|`/etc/icinga2`|Icinga2's main directory.|
|This Repo's location|`/opt/icinga/icinga2`|A symlink connects this repo's managed configuration files to the Icinga 2 service running on `host.xyz-monitor`. 
|Configuration files managed by this repo.|`/etc/icinga2/conf.d/xzy`| **Symlinked**. If you're ssh'd in, this is usually the directory you'll be working in. All .conf files MUST pass a validiation check. **PLEASE do not directly place files here**. Please fork this repo and push your config changes. If the CI/CD clears you are free to proceed with opening a merge request into the production repository.|
|icingaweb2|`/etc/icingaweb2/`|This is essentially the face of [xyz-monitor](https://xyz-monitor.example.edu). The interface in which most people are familiar with. 
|Web Interface Permission Roles|`/etc/icingaweb2/roles.ini`|Determines which user get's what permissions for the web dashboard. Ex. Can a user Acknowledge a host or disable notifications? Check in here||

# Important Commands

|Command|Purpose|
|---|---|
|`icinga2 daemon -C`|This tests *every* **.conf** file in `/etc/icinga2/conf.d/` against a validation test. If there's even one error it will fail and you will need to correct the mistake. Make sure every .conf passes this check or restarting the daemon will fail, causing xyz-monitor to crash until the error is corrected and the service restarted.|
|`systemctl restart icinga2`|Restarts the Icinga2 daemon. ***WARNING*** DO NOT RUN THIS COMMAND UNLESS THE ABOVE COMMAND RUNS SUCCESSFULLY.|

# Restarting your personal fork
Sometimes over the course of sending files to your personal fork the Gitlab tests will fail, usually due to missing OR duplicate configuration. And sometimes it gets to the point where no matter what you do, the syntax tests will continue to fail. I find it's best to just start the fork over fresh from the latest upstream/xzy and start the work-flow back over again.

```
# ensures current branch is master
git checkout master

# pulls all new commits made to upstream/master
git pull upstream master

# this will delete all your local changes to master
git reset --hard upstream/master

# take care, this will delete all your changes on your forked  master
git push origin master --force
```

# Debug Log
If you want to check it, here's how to enable the debuglog. 
**Steps for the debug log:**
- [ ] enable the debug log
```
$ icinga2 feature enable debuglog
```

- [ ] Icinga2 *must* pass a valid syntax check
```
$ icinga2 daemon -C'
```

If the check passed go ahead and restart the daemon.
- [ ] Restart Icinga 2
```
$ sudo systemctl restart icinga2
```

Now you can access the log at `/var/log/icinga2`.
I recommend grepping since the file is huge.
```
[root@xyz-monitor icinga2]# cd /var/log/icinga2/
[root@xyz-monitor icinga2]# grep 'check_list' debug.log
===SNIP===
[2020-10-16 12:09:35 -0400] notice/Process: PID 11448 ('/usr/lib64/nagios/plugins/check_nrpe' '-2' '-H' '141.211
.4.166' '-a' 'xzy-staff-official 2 4' '-c' 'check_list') terminated with exit code 0
```

It's a constantly filling log, with SQL queries and other information. **Please** disable the debug log if you don't have a use for it, as it will fill up the disk. 

**To Disable**
```
$ icinga2 feature disable debuglog
$ icinga2 daemon -C
$ sudo systemctl restart icinga2
```

## Documentation

### xzy Licenses
[xzy License Server Spreadsheet](https://docs.google.com)


## Links

### Recovery Docs
- [Disaster Recovery Documents](https://docs.google.com)

### Using Icinga2 Source Control
- [Git Tutorial](https://docs.google.com)


### XZYCommunity Group
- [xzy-icinga-users](https://xyzcommunity.xzy.edu/#group:xzy-icinga-users)
- [xzy-icinga-users@xzy.edu](mailto:xzy-icinga-users@xzy.edu) *Direct Email Link*

### Slack Channel 
- [#monitoring](https://xzy.slack.com/archives/monitoring)

### xzy-Tower Project
- [xzy-Tower](https://ansible-tower.xyz.edu/#/projects/1?project_search=page_size:20;order_by:name)
