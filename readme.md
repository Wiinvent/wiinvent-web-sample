SDK:

````javascript
<script src="https://wiinvent.tv/sdk/wii-sdk-1.6.7.js"></script>
````

1. Code Instream Sample:

```javascript
var wiiSdk = [];
const player = videojs("videojs-player");

wiiSdk = new WI.InstreamSdk({
  env: WI.Environment.SANDBOX,
  tenantId: 14,
  deviceType: WI.DeviceType.WEB,
  domId: "videoId",
  channelId: "2",
  streamId: "1999",
  contentType: WI.ContentType.VOD,
  title: "noi dung 1",
  transId: "111",
  category: "1, 2",
  keyword: "1, 2",
  age: "20",
  gender: WI.Gender.MALE,
  partnerSkipOffset: 0,
  vastLoadTimeout: 10,
  mediaLoadTimeout: 10,
  bufferingVideoTimeout: 15,
  alwaysCustomSkip: true,
  isAutoRequestFocus: false,
  bitrate: 1024,
  skipText: "Skip ads after {0} seconds",
  skippableText: "Skip ads"
})
player.one('loadeddata', () => wiiSdk.start())
player.on('sourceset', () => wiiSdk.destroy())
player.on('resize', () => wiiSdk.changeSize())
window.addEventListener("message", function (e) {
  if (['REQUEST', 'LOADED', 'START', 'PAUSED', 'RESUMED', 'ERROR', 'PLAYER_ERROR', 'CLICK', 'IMPRESSION', 'SKIPPED', 'COMPLETE', 'DESTROY', 'FULLSCREEN'].includes(e.data.type)) {
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
| bitrate               | Bitrate                                                   |   number |
| title                   | Tiêu đề của nội dung                                    |   string |
| tranId                  | Thông tin theo yêu cẩu của đối tác                      |   string |
| category                | Danh sách danh mục - được ngăn cách bởi dấu ,           |   string |
| keyword                 | Từ khoá tìm kiếm của nội dung (nếu có)                  |   string |
| age                     | Tuổi (Nếu có)                                           |   number |
| gender                  | Giới tính (nếu có)                                      | constant |

4. Constant

| Key         | Description                                                                                      |     
|:------------|:-------------------------------------------------------------------------------------------------|
| playerType  | WI.PlayerType.VIDEO_JS <br> WI.PlayerType.SHAKA <br> WI.PlayerType.HLS <br/>WI.PlayerType.AKAMAI |  
| deviceType  | WI.DeviceType.TV <br/> WI.DeviceType.WEB                                                         |  
| env         | WI.Environment.SANDBOX <br/> WI.Environment.PRODUCTION                                           |   
| contentType | WI.ContentType.VOD <br/>WI.ContentType.LIVE_STREAM                                                | 
| gender      | WI.Gender.MALE <br/>WI.Gender.FEMALE <br/>WI.Gender.OTHER <br/>WI.Gender.NONE                    | 

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

