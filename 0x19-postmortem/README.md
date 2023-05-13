## Postmortem of WordPress LAMP Server Outage

**Issue Summary**

At approximately 12:05 AM WAT on May 12th, 2023, our Apache-powered Wordpress web server became unresponsive to all external requests. System administrators discovered the issue upon waking up at 7:10 AM WAT and toiled away at the problem until far later in the day, approximately 10:10PM. During this period all web services from our server were entirely unavailable. The root cause was discovered to be a simple typo within one of our PHP configuration files. This problem was compounded by our PHP & Apache error log configurations, which guaranteed that the breakdown of service was neither logged in our records nor reported to sysadmins interacting with the server on the backend. The typo was corrected at approximately 10:10PM, and our team has written a script to automate any similar issues hereafter.

**Timeline**


- 7:05AM WAT: Sysadmin discovers outage on web server, debugging begins
- 7:25AM WAT: Sysadmin confirms that all basic web requests return a HTTP 500 error
- 7:30AM to 8:05PM WAT: Sysadmin runs through several ineffective debugs, including multiple uses of strace and curl
- 8:05PM to 9:10PM WAT: Sysadmin discovers that all PHP and Apache error logs have been disabled. Sysadmin gradually campus up error log reporting until virtually all error messages are enabled, then uses grep to parse through error log output
- 9:11PM WAT: Sysadmin discovers the following in the PHP error log: open("/var/www/html/wp-includes/class-wp-locale.phpp", O_RDONLY) = -1 ENOENT (No such file or directory)
- 9:15PM WAT: Sysadmin writes bash script using sed to fix the typo
- 9:30PM WAT: Server is reset and normal service is restored

**Root Cause of trouble**

The root cause was the typo in the PHP configuration file was discovered at /var/www/html/wp-settings.php in the server. A single file extension was listed as “phpp” when it should have been “php,” meaning that the server could not open a require file when attempting to respond to HTTP requests. Furthermore, all debugging messages were disabled in the Apache and PHP error logs, preventing the web server from issuing the proper warnings when the website failed to serve. 

**Resolution & Recovery**

Since the entire web server was already failing to fulfill any of its basic functionality for clients, it was decided to take the server offline by stopping the apache processes. Once error logging was re-enabled, sysadmins caught the typo in the error logs and fixed it. Restarting all services thereafter was smooth and normal server performance was restored.

**Corrective and Preventative Measures**

Verbose and excessive error logging can be expensive for a web server, both in terms of performance for the client and for memory usage on the server. However, in this case, error logging was at the other extreme. The SRE/Sysadmin teams should predefine several tiers of error logging for PHP and Apache, e.g. minimal, low, medium, and high - and build scripts to automate the process of setting logging at any of those levels. Furthermore, we have decided to script a small script that notifies the sysadmin of what portion of error logging is enabled when they log in to the server. Here the logs were silent and the team simply wasn’t aware. We will streamline the logging process and document this outage, emphasizing the importance of documenting risky changes such as log minimization in the future. Simply put, superior team communication could have prevented this outage before it began.
