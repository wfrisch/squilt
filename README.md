# squilt
Wrapper to confine quilt with [nsjail](https://github.com/google/nsjail)

# Why?
Using quilt on untrusted spec files is not secure. For more see
[this posting by Matthias](https://www.openwall.com/lists/oss-security/2018/09/27/2).

Matthias developed the original exploit, but as described in the posting, any
command in %prep is run as the user calling quilt without any limitations. This
was (at least to us) unexpected.

# Usage
If you add an alias quilt=squilt or copy this script as 'quilt' to a directory
that is earlier in $PATH you should be able to use it like you use quilt without
noticying a difference.

# Protection Level

squilt uses namespace isolation techniques to mark most of the hosts file
systems as read-only and to make possibly sensitive data inacessible (like the
user's home directory or most system configuration files). Write access is
still necessary in the current working directory where squilt is invoked. This
means a malicous RPM spec file could still manipulate the RPM package's
directory contents. When working with untrusted spec files this should be
obvious, however.
