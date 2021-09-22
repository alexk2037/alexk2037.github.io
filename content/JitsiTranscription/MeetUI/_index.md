
### Enable Subtitle Button

For some reason, at the time of writing (summer 2021), the ‘Start Subtitles’ button is missing by default. To fix this, uncomment   `transcribingEnabled`
and set to  `true`, then uncomment the `toolbarButtons` array

{{< expand "/etc/jitsi/meet/$(hostname -f)-config.js" >}}
```js
...
    // Whether to enable live streaming or not.
    // liveStreamingEnabled: false,
        // Transcription (in interface_config,
    // subtitles and buttons can be configured)
    transcribingEnabled: true,
    // Enables automatic turning on captions when recording is started
    // autoCaptionOnRecord: false,
...
    // Moved from interfaceConfig(TOOLBAR_BUTTONS).
    // The name of the toolbar buttons to display in the toolbar, including the
    // "More actions" menu. If present, the button will display. Exceptions are
    // "livestreaming" and "recording" which also require being a moderator and
    // some other values in config.js to be enabled. Also, the "profile" button will
    // not display for users with a JWT.
    // Notes:
    // - it's impossible to choose which buttons go in the "More actions" menu
    // - it's impossible to control the placement of buttons
    // - 'desktop' controls the "Share your screen" button
    // - if `toolbarButtons` is undefined, we fallback to enabling all buttons on the UI
    toolbarButtons: [
       'microphone', 'camera', 'closedcaptions', 'desktop', 'embedmeeting', 'fullscreen',
       'fodeviceselection', 'hangup', 'profile', 'chat', 'recording',
       'livestreaming', 'etherpad', 'sharedvideo', 'shareaudio', 'settings', 'raisehand',
       'videoquality', 'filmstrip', 'invite', 'feedback', 'stats', 'shortcuts',
       'tileview', 'select-background', 'download', 'help', 'mute-everyone', 'mute-video-everyone', 'security'
    ],
...
```
{{< /expand >}}

Now start a meeting and check that the ‘Start Subtitles’ button is present.