# Changelog

### Version 1.0.5
  * Add positive and negative `message_count` to allow for directional content requests

### Version 1.0.4
  * Add `draft/msgid` tag to scrollback
  * Fix case-sensitivity bug
  * Include additional debug output when enabled
  * Fix line parsing bug

### Version 1.0.3
  * Change `SCROLLBACK` time format to match IRCv3 `server-time` spec (same as used in `@batch`)
  * Added option 3rd parameter to `SCROLLBACK` to specify `message_count` (will use ZNC user setting if not specified)

### Version 1.0.2
  * Make command case-insensitive

### Version 1.0.1
  * Change CAP implementation to RPL_ISUPPORT 005 numeric, sending `SCROLLBACK` as a supported command
  * Change `SCROLL` command to `SCROLLBACK`

### Version 1.0.0
  * Implement `znc.in/scrollback` capability
