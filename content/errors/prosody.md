---
title: "Prosody"
date: 2021-07-26T00:27:40-04:00
draft: false
---

### Traceback[Bosh]: Attempt to perform arithmetic on a table value
When I start a meeting I get the following output in the prosody logs:

```Shell
Jul 26 04:17:12 mod_bosh        info    New BOSH session, assigned it sid 'b0be5f11-8434-4b1d-b707-14a6a703df14'
Jul 26 04:17:12 boshb0be5f11-8434-4b1d-b707-14a6a703df14        info    Authenticated as df6d8268-d64a-4d0d-855f-e2c20b40c32f@freemeet.net
Jul 26 04:17:12 mod_bosh        error   Traceback[bosh]: ...jitsi-meet/prosody-plugins/mod_external_services.lua:119: attempt to perform arithmetic on a table value
stack traceback:
        ...jitsi-meet/prosody-plugins/mod_external_services.lua:119: in function 'get_services'
        ...jitsi-meet/prosody-plugins/mod_external_services.lua:169: in function '?'
        /usr/lib/prosody/util/events.lua:78: in function </usr/lib/prosody/util/events.lua:74>
        (tail call): ?
        (tail call): ?
        (tail call): ?
        /usr/lib/prosody/util/events.lua:78: in function </usr/lib/prosody/util/events.lua:74>
        (tail call): ?
        /usr/lib/prosody/core/stanza_router.lua:189: in function 'core_post_stanza'
        /usr/lib/prosody/core/stanza_router.lua:137: in function </usr/lib/prosody/core/stanza_router.lua:63>
        ...
        /usr/lib/prosody/net/http/server.lua:112: in function 'process_next'
        /usr/lib/prosody/net/http/server.lua:128: in function 'success_cb'
        /usr/lib/prosody/net/http/parser.lua:177: in function 'feed'
        /usr/lib/prosody/net/http/server.lua:159: in function </usr/lib/prosody/net/http/server.lua:158>
        (tail call): ?
        /usr/lib/prosody/net/server_select.lua:879: in function </usr/lib/prosody/net/server_select.lua:861>
        [C]: in function 'xpcall'
        /usr/bin/prosody:400: in function 'loop'
        /usr/bin/prosody:431: in main chunk
        [C]: ?
```

{{< expand "Attempt 1" >}}

**Monday July 26, 2021**

[Potential fix](https://community.jitsi.org/t/solved-issue-in-connectivity-after-upgrade-the-jitsi-meet/17882/7)
* Change all "none" to "null" in prosody config file

This did not fix the problem, however, this did not introduce any new errors either

{{< /expand >}}