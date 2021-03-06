# Postmortem Report

<a href='https://postimages.org/' target='_blank'><img src='https://i.postimg.cc/s2wRCDC6/Screen-Shot-2019-03-06-at-12-33-42-PM.png' border='0' alt='Screen-Shot-2019-03-06-at-12-33-42-PM'/></a>

## 500 server error

### Incident report for [500 error / Site Outage](https://github.com/leinefran/holberton-system_engineering-devops/tree/master/0x17-web_stack_debugging_3)

#### Summary

On the morning of Friday March 2nd, 2019 the server access went down resulting in 500 error. Such error occur when there's something wrong on the website's server.

#### Timeline

- **10:15 PST** - 500 error when trying to access the website
- **10:20 PST** - Reloaded the webpage, same error response.
- **10:25 PST** - Using "ps -auxf", checked what processes were active. Apache Server was running two processes.
- **10:30 PST** - Used strace and curl in both servers as a way to diagnose the problem.
- **10:32 PST** - Noticed that one of the servers were performing system calls in files with the wrong extension: "phpp" instead of "php".
- **10:35 PST** - Corrected the problem by changing the file extension to the correct one using puppet and "sed" linux command.
- **10:45 PST** - The webpage was succesfully reloaded.

#### Root Cause and Resolution
As you can see in the example below, the issue was caused by the server connecting to a file with the wrong extension.

```
stat("/var/www/html/wp-includes/default-filters.php", {st_mode=S_IFREG|0644, st_size=25220, ...}) = 0
stat("/var/www/html/wp-includes/l10n.php", {st_mode=S_IFREG|0644, st_size=43130, ...}) = 0
lstat("/var/www/html/wp-includes/class-wp-locale.phpp", 0x7ffda99ecb20) = -1 ENOENT (No such file or directory)
lstat("/var/www/html/wp-includes/class-wp-locale.phpp", 0x7ffda99ec9f0) = -1 ENOENT (No such file or directory)
lstat("/var/www/html/wp-includes/class-wp-locale.phpp", 0x7ffda99eec20) = -1 ENOENT (No such file or directory)
open("/var/www/html/wp-includes/class-wp-locale.phpp", O_RDONLY) = -1 ENOENT (No such file or directory)
```

Once the problem was corrected, the server was reinitiated without further issues.

#### Corrective and Preventive Measures

- All servers and sites should have error logging turned on to easily identify errors if anything goes wrong.
- All servers and sites should be tested locally before deployment.