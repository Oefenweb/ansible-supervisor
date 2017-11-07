## supervisor

[![Build Status](https://travis-ci.org/Oefenweb/ansible-supervisor.svg?branch=master)](https://travis-ci.org/Oefenweb/ansible-supervisor) [![Ansible Galaxy](http://img.shields.io/badge/ansible--galaxy-supervisor-blue.svg)](https://galaxy.ansible.com/tersmitten/supervisor)

Set up the latest or a specific version of supervisor in Debian-like systems.

#### Requirements

* `pip` (will not installed)
* `python` (will be installed)

#### Variables

* `supervisor_version` [default: `latest`]: Supervisor version to install (e.g. `latest`, `3.3.1`)
* `supervisor_unix_http_server_file` [default: `/var/run/supervisor.sock`]: A path to a UNIX domain socket (e.g. `/tmp/supervisord.sock`) on which supervisor will listen for HTTP/XML-RPC requests. `supervisorctl` uses XML-RPC to communicate with supervisord over this port
* `supervisor_unix_http_server_chmod` [default: `'0700'`]: Change the UNIX permission mode bits of the UNIX domain socket to this value at startup
* `supervisor_unix_http_server_chown` [optional]: The user and group of `supervisor_unix_http_server_file` file. Use the following format: `user:group`
* `supervisor_unix_http_server_username` [optional]: The username for authentication to the HTTP/XML-RPC server
* `supervisor_unix_http_server_password` [optional]: The password for authentication to the HTTP/XML-RPC server. This can be a cleartext password, or can be specified as a `SHA-1` hash if prefixed by the string `{SHA}`

* `supervisor_system_user` [default: `root`]: Name of the user that should own the config file/directory
* `supervisor_system_group` [default: `root`]: Name of the group that should own the config file/directory

* `supervisor_inet_http_server_enabled` [default: `false`]: Whether or not to enable the HTTP server
* `supervisor_inet_http_server_port` [default: `9001`]: A TCP host:port value or (e.g. `127.0.0.1:9001`) on which supervisor will listen for HTTP/XML-RPC requests. `supervisorctl` will use XML-RPC to communicate with supervisord over this port
* `supervisor_inet_http_server_username` [default: `admin`]: The username required for authentication to this HTTP server **Make sure to change!**
* `supervisor_inet_http_server_password` [default: `'4ubA&Et=ASPe'`]: The password required for authentication to this HTTP server. This can be a cleartext password, or can be specified as a SHA-1 hash if prefixed by the string `{SHA}` **Make sure to change!**

* `supervisor_supervisord_nodaemon` [default: `false`]: If true, supervisord will start in the foreground instead of daemonizing
* `supervisor_supervisord_logfile` [default: `/var/log/supervisor/supervisord.log`]: The path to the activity log of the supervisord process
* `supervisor_supervisord_pidfile` [default: `/var/run/supervisord.pid`]: The location in which supervisord keeps its pid file
* `supervisor_supervisord_childlogdir` [default: `/var/log/supervisor`]: The directory used for `AUTO` child log files

* `supervisor_supervisorctl_serverurl` [default: `"unix://{{ supervisor_unix_http_server_file }}"`]: The URL that should be used to access the supervisord server, e.g. `http://localhost:9001`. For UNIX domain sockets, use `unix:///absolute/path/to/file.sock`

* `supervisor_include` [default: `'/etc/supervisor/conf.d/*.conf'`]: A (single) absolute file glob of files to include

* `supervisor_programs_present` [default: `{}`, see `templates/etc/supervisor/conf.d/program.conf.j2`]: Program definitions
* `supervisor_programs_present.{n}` [required]: Program name
* `supervisor_programs_present.{n}.command` [required]: The command that will be run when this program is started
* `supervisor_programs_present.{n}.directory` [optional]: A directory to which supervisord should temporarily chdir before exec’ing the child
* `supervisor_programs_present.{n}.environment` [optional]: A list of key/value pairs that will be placed in the child process’ environment
* `supervisor_programs_present.{n}.autostart` [optional]: If true, this program will start automatically when supervisord is started
* `supervisor_programs_present.{n}.autorestart` [optional]: Whether the process will be autorestarted
* `supervisor_programs_present.{n}.startretries` [optional]: The number of serial failure attempts that supervisord will allow when attempting to start the program before giving up
* `supervisor_programs_present.{n}.startsecs` [optional]: The total number of seconds which the program needs to stay running after a startup to consider the start successful
* `supervisor_programs_present.{n}.stdout_logfile` [optional]: Put process stdout output in this file
* `supervisor_programs_present.{n}.stdout_logfile_maxbytes` [optional]: The maximum number of bytes that may be consumed by `stdout_logfile` before it is rotated
* `supervisor_programs_present.{n}.stderr_logfile` [optional]: Put process stderr output in this file
* `supervisor_programs_present.{n}.stderr_logfile_maxbytes` [optional]: The maximum number of bytes before logfile rotation for `stderr_logfile`
* `supervisor_programs_present.{n}.user` [optional]: This UNIX user account will be used as the account which runs the program
* `supervisor_programs_present.{n}.numprocs` [optional]: Supervisor will start as many instances of this program as named by numprocs
* `supervisor_programs_present.{n}.process_name` [optional]: Process name, defaults to `%(program_name)s-%(process_num)s` when `numprocs` > 1
* `supervisor_programs_present.{n}.stopsignal` [optional]: The signal used to kill the program when a stop is requested
* `supervisor_programs_present.{n}.stopwaitsecs` [optional]: The number of seconds to wait for the OS to return a `SIGCHILD` to supervisord after the program has been sent a stopsignal
* `supervisor_programs_present.{n}.stopasgroup` [optional]: If `true`, the flag causes supervisor to send the stop signal to the whole process group and implies `killasgroup` is `true`
* `supervisor_programs_present.{n}.killasgroup` [optional]: If `true`, when resorting to send `SIGKILL` to the program to terminate it send it to its whole process group instead, taking care of its children as well
* `supervisor_programs_absent` [default: `{}`]: Program definitions (to be removed)

* `supervisor_groups_present` [default: `{}`, see `templates/etc/supervisor/conf.d/group.conf.j2`]: Group definitions
* `supervisor_groups_present.{n}` [required]: Group name
* `supervisor_groups_present.{n}.programs` [required]: List of programs
* `supervisor_groups_present.{n}.priority` [optional]: A priority number
* `supervisor_groups_absent` [default: `{}`]: Group definitions (to be removed)

## Dependencies

None

## Recommended

* `ansible-pip` ([see](https://github.com/Oefenweb/ansible-pip))

#### Example

```yaml
---
- hosts: all
  roles:
    - supervisor
```

##### Adding two programs in one group

```yaml
supervisor_programs_present:
  foo:
    command: 'sleep 10'
    directory: /tmp
    autostart: true
    autorestart: true
    startretries: 3
    stdout_logfile: /tmp/foo.out
    stdout_logfile_maxbytes: 0
    stderr_logfile: /tmp/foo.err
    stderr_logfile_maxbytes: 0
    user: vagrant
    numprocs: 8
    process_name: '%(program_name)s-%(process_num)s'
  bar:
    command: 'sleep 10'
    directory: /tmp
    autostart: true
    autorestart: true
    startretries: 3
    stdout_logfile: /tmp/bar.out
    stdout_logfile_maxbytes: 0
    stderr_logfile: /tmp/bar.err
    stderr_logfile_maxbytes: 0
    user: vboxadd

supervisor_groups_present:
  foo-and-bar:
    programs:
      - foo
      - bar
    priority: 10
```

##### Removing a group

```yaml
supervisor_groups_absent:
  foo-and-bar: {}
```

#### License

MIT

#### Author Information

Mischa ter Smitten

#### Feedback, bug-reports, requests, ...

Are [welcome](https://github.com/Oefenweb/ansible-supervisor/issues)!
