# znc-scrollback
Implements the `znc.in/scrollback` capability, enabling infinite scrollback in clients by pulling previous content from log files and sending them to the client as raw IRC lines

## Table of Contents
- [Requirements](#requirements)
- [Installation](#installation)
- [Loading](#loading)
- [Commands](#commands)
- [Capability Specification](#capability-specification)
- [Contact](#contact)

## Requirements
 - <a href="http://znc.in">ZNC 1.6 or later</a>
     - Log files named per the [ZNC 1.6 log module](http://wiki.znc.in/Log#Arguments)  (`%Y-%m-%d.log`)
 - <a href="https://www.python.org">Python 3</a>
 - <a href="http://wiki.znc.in/Modpython">modpython</a>

## Installation
To install *znc-scrollback*, place `scrollback.py` in your ZNC modules folder

## Loading
`/znc loadmod scrollback`

## Commands

`set <setting> <value>` Set the [configuration options](#settings)

`settings` Display your current settings

`about` Display information about this module

`help` Print help for this module

### Configuration options

`size` **integer** The amount of lines to retrieve during each scrollback request

`extras` **True/False**  Include extra events in scrollback (join, kick, mode, nick, quit, part, topic)

`strip` **True/False** Strip control codes from output

`path` **path_to_file** 
Specify the complete, absolute path to your log files. Accepted variables:
- Current user: `$USER`
- Current network: `$NETWORK`
- Channel / query: `$WINDOW`

`debug` **True/False** Send output to module instead of client

## Capability Specification

The module outputs content specified by the [IRCv3.2 batch Extension](http://ircv3.net/specs/extensions/batch-3.2.html) using a modified [chathistory](http://ircv3.net/specs/extensions/batch/chathistory-3.3.html) batch type. All IRC content follows [RFC 1459](https://tools.ietf.org/html/rfc1459).

To allow the client to identify scrollback content, the `chathistory` type has been changed to `scrollback`. See below for examples.

### Variables
- **ID:** A 13-character alphanumeric string used to identify the batch
- **target:** A channel or query window to which the scrollback belongs

### Requesting Scrollback
Scrollback content can be requested from the client by sending the `SCROLL` command to ZNC from the client.

The latest timestamp the client currently has in it's scrollback should be sent. The module will send any content **before** the given date and time.

#### Format
    SCROLL target YYYY-mm-dd HH:mm:ss

#### Example
    SCROLL #znc 2016-11-19 18:02:10

### Returned Scrollback

Each example displays the generic line format followed by a specific example. Each set of returned data will begin and with a unique BATCH ID.

#### Begin
    :znc.in BATCH +ID scrollback target
    :znc.in BATCH +XNyDSitp9MvcX scrollback #znc
#### PRIVMSG
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :nick!ident@host PRIVMSG target :message
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:01.000Z :MuffinMedic!Evan@unaffiliated/muffinmedic PRIVMSG #znc :The znc.in/scrollback capability is going to be fantastic!
#### NOTICE
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :nick!ident@host NOICE target :message
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:02.000Z :MuffinMedic!scrollback@znc.in NOTICE #znc :Announcing the new 'znc.in/scrollback' capability!
#### JOIN
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :nick!ident@host JOIN :#channel
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:03.000Z :MuffinMedic!Evan@unaffiliated/muffinmedic JOIN :#znc
#### PART
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :nick!ident@host PART #channel :reason
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:04.000Z :MuffinMedic!Evan@unaffiliated/muffinmedic PART #znc :This place is too addicting
#### QUIT
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :nick!ident@host QUIT #channel :reason
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:05.000Z :MuffinMedic!Evan@unaffiliated/muffinmedic QUIT #znc :Restarting in debug mode
#### KICK
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :op_nick!ident@host KICK #channel kicked_nick :message
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:06.000Z :MuffinMedic!scrollback@znc.in KICK #znc CupcakeMedic :Muffins > cupcakes
#### NICK
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :old_nick!ident@host NICK :new_nick
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:07.000Z :MuffinMedic!Evan@unaffiliated/muffinmedic NICK :Evan
#### TOPIC
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :nick!ident@host TOPIC #channel :topic
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:08.000Z :MuffinMedic!scrollback@znc.in TOPIC #znc :Check out the new 'znc.in/scrollback' capability
#### MODE
    @batch=ID;time=YYYY-mm-ddTHH:mm:ss.000Z :op_nick!ident@host MODE #channel mode(s) parameter(s)
    @batch=XNyDSitp9MvcX;time=2016-11-19T18:02:09.000Z :MuffinMedic!scrollback@znc.in MODE #znc +b *!*@unaffiliated/cupcakemedic
#### End
    :znc.in BATCH -ID
    :znc.in BATCH -XNyDSitp9MvcX

## Contact

Issues/bugs should be submitted on the <a href="https://github.com/MuffinMedic/znc-scrollback/issues">GitHub issues page</a>.

For assistance, please PM MuffinMedic (Evan) on <a href="https://kiwiirc.com/client/irc.snoonet.org:+6697">Snoonet</a> or <a href="https://kiwiirc.com/client/irc.freenode.net:+6697">freenode<a/>.