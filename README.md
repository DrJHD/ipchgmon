# NAME

ipchgmon.pm - Watches for changes to public facing IP addresses

# SYNOPSIS

```
perl ipchgmon.pm --file c:\data\log.txt --server example.com
```

Who knows? It might even work. More usually, in a cron job:

```perl
perl ipchgmon --file ~/log.txt \
              --server back_office_top_shelf \
              --dnsname example.com \
              --leeway 86400 \
              --email serverchange@example.com \
              --mailserver 192.168.0.2 \
              --mailfrom ipchgmon@example.com \
              --mailsubject 'Change of IP address'
```

# BACKGROUND

I and friends run email and other servers at home. I pay for a static IP
address with the clause in my contract that force majeur may require a
change. Others are on dynamic addresses. Should the public facing address
change, we want to know. This modulino is intended to monitor the public
IP address and shout for help should the address change. One friend is
looking at code to change public DNS records automatically.

# DESCRIPTION

This modulino is intended to be run automatically as a cron job. It should
check whether the server running it has changed its IP address. If so, 
messages should be sent to those specified.

Either IPv4 or IPv6 or both formats can be tested. It might well be that
different servers handle the different types.

There are three issues that may be checked. The first is connectivity. If
there is no connectivity, messages should be sent. No further issues will be
tested.

The second issue that will be tested is whether the IP address has changed.
The current public-facing IP address is established via internet use and
compared to a log file, specified by the "--file" option. If it is not the
last entry in the log, messages will be sent.

Finally and optionally, the DNS name of the server will be used to get 
another IP address from global DNS. If this is not the last address in the
log file, messages will be send unless a leeway has been specified and this 
has not expired.

If connectivity is lost, the number of retries and the wait will both be
options in a later version. At present, or if the retries all fail, someone 
should be sent a message.

The design includes three forms of message, SMS, HTTP and email. It is 
reasonable to send an email if internet connectivity is lost; the server 
may be internal. SMS may be harder to justify. HTTP must depend on the 
location of the server.

There is currently no facility to send different classes of message to
different addresses. There is nothing inherently impossible about writing
code to do this if it proves desirable.

If the IP address has changed, messages should be sent without delay or retries.

# ON ARGUMENTS AND OPTIONS

These are processed by Getopt::Long. This means that shorter versions may
be used. If the first letter of an argument or option is unique, the call
may be reduced to a single minus sign and the first letter. So the "email"
argument has an alias, "mailto". But the alias must be specified as
"--mailt" at least, while "email" can be reduced to "-e".

# ARGUMENTS

```perl
--file filename THIS OPTION IS COMPULSORY unless --debug is used.
                The file "filename" will be created if it does not exist.
                It may be preferable to use a fully qualified filename.
                Attempting to write to a non-existent directory or without
                the necessary permissions will cause an error. The file is
                plain text containing the IPv4 and IPv6 addresses of the server.
--server name   THIS OPTION IS COMPULSORY unless --debug is used.
                It is the name that will be used to identify the machine
                should internet connectivity be lost or the IP address
                change. It is not used internally and should not be
                confused with the dnsname.
--dnsname name  The name of the server in global DNS.
--leeway time   This option specifies how many seconds should elapse from
                an IP address changing on DNS to a second email being sent.
                It is used only in conjunction with dnsname. If dnsname
                is unspecified, the value of this option is meaningless.
                It isn't compulsory, but without it, messages will be sent
                every time the modulino finds the IP address is not the
                same as returned by DNS. This is fine if you like getting
                up every hour during the night. Otherwise, use something
                like 86400 (one day).
--email address Multiple instances of this option are acceptable. An email
                will be sent to each, if possible.
--mailto        Synonym for --email.
--mailserver    Must be followed by the name or ip address of the outbound
                server. Some systems may have a default for this.
--mailport      Must be numeric. Will default to 25 if omitted.
--mailfrom      Most servers will insist on this, but some systems may
                have a default.
--mailsubject   Can be omitted if desired. A default would be created
                which would be different for each message type. Subjects
                that include spaces would need quoting. The quote character
                can be OS dependent.
```

# OPTIONS

```perl
--help          Brief manual
--man           Full manual
--versions      Code info
--debug         Debugging information
--singleemail   Sends one email at a time. Prevents multiple email 
                addresses appearing in each email and prevents server
                confusion if different mechanisms are used for different
                destinations. Yes, it can happen.
--4             Check IPv4 addresses. Both will be checked if neither
                or both options are used.
--6             Check IPv6 addresses. Both will be checked if neither
                or both options are used.
```

# TO DO

> \* Implement SMS messages
>
> \* Implement HTTP messages
>
> \* implement config file

# COPYRIGHT AND LICENSE

Copyright (C) 2022 by John Davies

This library is free software; you can redistribute it and/or modify it under the same terms as Perl itself.

# CREDITS

https://www.perlmonks.org/?node_id=155288 for the getopt/usage model.

Fergus McMenemie for the talk on modulinos (https://www.youtube.com/watch?v=wCW4tpMgdHs).
