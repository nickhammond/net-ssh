[![Gem Version](https://badge.fury.io/rb/net-ssh.svg)](https://badge.fury.io/rb/net-ssh)
[![Join the chat at https://gitter.im/net-ssh/net-ssh](https://badges.gitter.im/net-ssh/net-ssh.svg)](https://gitter.im/net-ssh/net-ssh?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/net-ssh/net-ssh.svg?branch=master)](https://travis-ci.org/net-ssh/net-ssh)
[![Coverage status](https://codecov.io/gh/net-ssh/net-ssh/branch/master/graph/badge.svg)](https://codecov.io/gh/net-ssh/net-ssh)
[![Backers on Open Collective](https://opencollective.com/net-ssh/backers/badge.svg)](#backers])
[![Sponsors on Open Collective](https://opencollective.com/net-ssh/sponsors/badge.svg)](#sponsors)

# Net::SSH 5.x

* Docs: http://net-ssh.github.com/net-ssh
* Issues: https://github.com/net-ssh/net-ssh/issues
* Codes: https://github.com/net-ssh/net-ssh
* Email: net-ssh@solutious.com

*As of v2.6.4, all gem releases are signed. See [INSTALL](#install).*

## DESCRIPTION:

Net::SSH is a pure-Ruby implementation of the SSH2 client protocol.
It allows you to write programs that invoke and interact with processes on remote servers, via SSH2.

## FEATURES:

* Execute processes on remote servers and capture their output
* Run multiple processes in parallel over a single SSH connection
* Support for SSH subsystems
* Forward local and remote ports via an SSH connection

## SYNOPSIS:

In a nutshell:

```ruby
require 'net/ssh'

Net::SSH.start('host', 'user', password: "password") do |ssh|
# capture all stderr and stdout output from a remote process
output = ssh.exec!("hostname")
puts output

# capture only stdout matching a particular pattern
stdout = ""
ssh.exec!("ls -l /home/jamis") do |channel, stream, data|
  stdout << data if stream == :stdout
end
puts stdout

# run multiple processes in parallel to completion
ssh.exec "sed ..."
ssh.exec "awk ..."
ssh.exec "rm -rf ..."
ssh.loop

# open a new channel and configure a minimal set of callbacks, then run
# the event loop until the channel finishes (closes)
channel = ssh.open_channel do |ch|
  ch.exec "/usr/local/bin/ruby /path/to/file.rb" do |ch, success|
    raise "could not execute command" unless success

    # "on_data" is called when the process writes something to stdout
    ch.on_data do |c, data|
      $stdout.print data
    end

    # "on_extended_data" is called when the process writes something to stderr
    ch.on_extended_data do |c, type, data|
      $stderr.print data
    end

    ch.on_close { puts "done!" }
  end
end

channel.wait

# forward connections on local port 1234 to port 80 of www.capify.org
ssh.forward.local(1234, "www.capify.org", 80)
ssh.loop { true }
end
```

See Net::SSH for more documentation, and links to further information.

## REQUIREMENTS:

The only requirement you might be missing is the OpenSSL bindings for Ruby with a version greather than `1.0.1`.
These are built by default on most platforms, but you can verify that they're built and installed on your system by running the following command line:

```sh
ruby -ropenssl -e 'puts OpenSSL::OPENSSL_VERSION'
```

If that spits out something like `OpenSSL 1.0.1 14 Mar 2012`, then you're set.
If you get an error, then you'll need to see about rebuilding ruby with OpenSSL support,
or (if your platform supports it) installing the OpenSSL bindings separately.

## INSTALL:

```sh
gem install net-ssh # might need sudo privileges
```

NOTE: If you are running on jruby on windows you need to install `jruby-pageant` manually
(gemspec doesn't allow for platform specific dependencies).

However, in order to be sure the code you're installing hasn't been tampered with,
it's recommended that you verify the [signature](http://docs.rubygems.org/read/chapter/21).
 To do this, you need to add my public key as a trusted certificate (you only need to do this once):

```sh
# Add the public key as a trusted certificate
# (You only need to do this once)
curl -O https://raw.githubusercontent.com/net-ssh/net-ssh/master/net-ssh-public_cert.pem
gem cert --add net-ssh-public_cert.pem
```

Then, when install the gem, do so with high security:

```sh
gem install net-ssh -P HighSecurity
```

If you don't add the public key, you'll see an error like "Couldn't verify data signature".
If you're still having trouble let me know and I'll give you a hand.

For ed25519 public key auth support your bundle file should contain `ed25519`, `bcrypt_pbkdf` dependencies.

```sh
gem install ed25519
gem install bcrypt_pbkdf
```

## RUBY SUPPORT

* See [net-ssh.gemspec](https://github.com/net-ssh/net-ssh/blob/master/net-ssh.gemspec) for current versions ruby requirements

## RUNNING TESTS

If you want to run the tests or use any of the Rake tasks, you'll need Mocha and
other dependencies listed in Gemfile

Run the test suite from the net-ssh directory with the following command:

```sh
bundle exec rake test
```

Run a single test file like this:

```sh
ruby -Ilib -Itest test/transport/test_server_version.rb
```

To run integration tests see test/integration/README.txt

### BUILDING GEM

```sh
rake build
```

### GEM SIGNING (for maintainers)

If you have the net-ssh private signing key, you will be able to create signed release builds. Make sure the private key path matches the `signing_key` path set in `net-ssh.gemspec` and tell rake to sign the gem by setting the `NET_SSH_BUILDGEM_SIGNED` flag:

```sh
NET_SSH_BUILDGEM_SIGNED=true rake build
```

For time to time, the public certificate associated to the private key needs to be renewed. You can do this with the following command:

```sh
gem cert --build netssh@solutious.com --private-key path/2/net-ssh-private_key.pem
mv gem-public_cert.pem net-ssh-public_cert.pem
gem cert --add net-ssh-public_cert.pem
```

## CREDITS

### Contributors

This project exists thanks to all the people who contribute.

[![contributors](https://opencollective.com/net-ssh/contributors.svg?width=890&button=false)](graphs/contributors)

### Backers

Thank you to all our backers! 🙏 [Become a backer](https://opencollective.com/net-ssh#backer)

[![backers](https://opencollective.com/net-ssh/backers.svg?width=890)](https://opencollective.com/net-ssh#backers)

### Sponsors

Support this project by becoming a sponsor. Your logo will show up here with a link to your website. [Become a sponsor](https://opencollective.com/net-ssh#sponsor)

[![Sponsor](https://opencollective.com/net-ssh/sponsor/0/avatar.svg)](https://opencollective.com/net-ssh/sponsor/0/website)

## LICENSE:

(The MIT License)

Copyright (c) 2008 Jamis Buck

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
