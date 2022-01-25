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

# Runtime Configuration

Squilt does not support command line parameters, because it acts as a frontend
to the actual `quilt` program and thus passes on any command line parameters
to it.

To still allow some degree of configuration the following environment
variables are recognized by squilt:

- `SQUILT_VERBOSE`: if set then the verbosity of squilt and nsjail is
  increased to give a better picture of what happens.
- `SQUILT_DEBUG`: if set then even more internals of the squilt operation
  are shown. This currently means the temporary nsjail configuration file is
  dumped onto stdout before executing quilt.
- `SQUILT_ENTER_SHELL`: if set then instead of actually running quilt, an
  interactive shell in the jail environment is invoked. This allows
  interactive debugging of the shell environment. Note that some things are
  different in this mode: For example the /proc directory is mounted which
  is normally not the case.
- `SQUILT_EXTRA_MOUNTS`: to allow extra mount operations in the jail
  environment without having to modify the squilt main script you can
  specify a semicolon separted list of extra `MountSpec` entries that will
  be added. For example: `SQUILT_EXTRA_MOUNTS="MountSpec(src='/opt')"`. This
  would be mounting /opt from the host into the jail environment.
