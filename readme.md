SDK:

````javascript
<script src="https://wiinvent.tv/sdk/wii-sdk-1.5.1.js"></script>
````

1. Code Instream Sample:

```javascript
const player = videojs('videojs-player');
const wiiSdk = new WI.InstreamSdk({
  env: WI.Environment.SANDBOX,
  tenantId: 14,
  deviceType: WI.DeviceType.WEB,
  domId: 'videoId',
  player: player,
  channelId: '2',
  streamId: '604468',
  partnerSkipOffset: 5,
  vastLoadTimeout: 10,
  mediaLoadTimeout: 10,
  bufferingVideoTimeout: 15,
  alwaysCustomSkip: true,
  isAutoRequestFocus: false
});

player.one('loadeddata', () => wiiSdk.start())
player.on('sourceset', () => wiiSdk.destroy())
player.on('resize', () => wiiSdk.changeSize())
window.addEventListener("message", function (e) {
  if (['REQUEST', 'LOADED', 'ERROR', 'START', 'PAUSED', 'RESUMED', 'CLICK', 'IMPRESSION', 'SKIPPED', 'COMPLETE', 'DESTROY'].includes(e.data.type)) {
    console.log('mmmm', e)
  }
})

```

2. Overlay Sample

````javascript
const wiinsdk = new WI.OverlaySdk({
  domId: "videojs-player",
  playerType: WI.PlayerType.VIDEO_JS,
  channelId: '2',
  streamId: '604468',
  deviceType: WI.DeviceType.WEB,
  env: WI.Environment.SANDBOX,
  contentType: WI.ContentType.VOD,
  thirdPartyToken: 'JWT ',
  tenantId: 14,

  onTokenExpire: function () {
    //token expire
  },
  onUserLogin: function () {
    //require login
  }
})
````

3. Parameter

| Key                   | Description                                               |     Type |
|:----------------------|:----------------------------------------------------------|---------:|
| tenantId              | Identifier of the account created by the Broadcast Center |  integer |
| channelId             | Identifier of the channel created by partner              |   string |
| streamId              | Identifier of the channel created by partner              |   string |
| vastLoadTimeout       | Vast Load Timeout                                         |  integer |
| mediaLoadTimeout      | Media Load Timeout                                        |  integer |
| bufferingVideoTimeout | Buffering Video Timeout                                   |  integer |                                  
| partnerSkipOffset     | Skip Ad Duration                                          |  integer |                                  
| env                   | Override the API host url for development and testing     | constant |
| deviceType            | Device Type                                               | constant |
| thirdPartyToken       | JWT Token from partner                                    |   string |
| playerType            | Player Type                                               | constant |
| alwaysCustomSkip      | Decided to use custom skip button                         |  boolean |
| isAutoRequestFocus    | Decided to focus on skip button after skip time           |  boolean |

4. Constant

| Key         | Description                                                                                      |     
|:------------|:-------------------------------------------------------------------------------------------------|
| playerType  | WI.PlayerType.VIDEO_JS <br> WI.PlayerType.SHAKA <br> WI.PlayerType.HLS <br/>WI.PlayerType.AKAMAI |  
| deviceType  | WI.DeviceType.TV <br/> WI.DeviceType.WEB                                                         |  
| env         | WI.Environment.SANDBOX <br/> WI.Environment.PRODUCTION                                           |   
| contentType | WI.ContentType.VOD <br/>WI.ContentType.LIVESTREAM                                                | 

5. Ads Callback

| Type      | Value      | Description                                   |
|:----------|:-----------|:----------------------------------------------|
| EventType | REQUEST    | Fired when the ad requests                    |
| EventType | LOADED     | Fired when the ad loaded                      |
| EventType | START      | Fired when the ad starts playing              |
| EventType | IMPRESSION | Fired when the impression URL has been pinged |
| EventType | CLICK      | Fired when the ad is clicked                  |
| EventType | COMPLETE   | Fired when the ad completes playing           |
| EventType | SKIPPED    | Fired when the ad is skipped by the user      |
| EventType | ERROR      | Fired when the ad has an error                |
| EventType | DESTROY    | Fires when the ad destroyed                   |

