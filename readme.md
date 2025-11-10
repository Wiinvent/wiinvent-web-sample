SDK:

````javascript
<script src="https://wiinvent.tv/sdk/wii-sdk-2.0.1.js"></script>
````

1. Hướng dẫn tích hợp:
### 📄 Tài liệu đính kèm

👉 [Tải xuống WI Instream Ad SDK Guide (PDF)](./IntegrationGuide.pdf)

2.Code Instream Sample:

```javascript
  // --- Player Elements ---
const video = document.getElementById('content-video');
const playPauseBtn = document.getElementById('play-pause');
const adsContainer = document.getElementById('wiinvent_ads_container_id');
const controls = document.querySelector('.controls');
const progress = document.getElementById('progress');
const time = document.getElementById('time');

// FULLSCREEN ELEMENTS
const fullscreenBtn = document.getElementById('fullscreen-toggle');
const videoContainer = document.querySelector('.video-container');

window.wiiSdk = null;
let adsInitialized = false;
let skipButton = null; // Keep a reference to the skip button
let isAdPlaying = false;

// --- Main Player Logic ---
playPauseBtn.addEventListener('click', () => {
  if (video.paused || video.ended) video.play();
  else video.pause();
});

video.addEventListener('play', () => {
  playPauseBtn.textContent = 'Pause';
  if (!adsInitialized) {
    handleAds();
    adsInitialized = true;
  }
});

video.addEventListener('pause', () => {
  playPauseBtn.textContent = 'Play';
});

// --- FULLSCREEN LOGIC ---
function isInFullscreen() {
  return document.fullscreenElement === videoContainer;
}

fullscreenBtn.addEventListener('click', () => {
  if (!isInFullscreen()) {
    if (videoContainer.requestFullscreen) {
      videoContainer.requestFullscreen();
    }
  } else {
    if (document.exitFullscreen) {
      document.exitFullscreen();
    }
  }
});

document.addEventListener('fullscreenchange', () => {
  if (isInFullscreen()) {
    fullscreenBtn.textContent = 'Exit Fullscreen';
  } else {
    fullscreenBtn.textContent = 'Fullscreen';
  }
});

// --- SDK Initialization ---
function handleAds() {
  const sdkConfig = {
    domId: "wiinvent_ads_container_id",
    liveTv: false,
    env: WI.Environment.SANDBOX,
    flatform: WI.Flatform.WEB,
    tenantId: 14,
    deviceType: WI.DeviceType.PC,
    channelId: "115376",
    streamId: "23203", // 23173, 23203
    adId: "1999",
    contentType: WI.ContentType.VIDEO,
    title: "noi dung 1",
    transId: "111",
    category: "1, 2",
    keyword: "1, 2",
    age: "20",
    gender: WI.Gender.MALE,
    uid20: "test",
    manufacturer: "",
    model: "",
    osName: "",
    osVersion: "",
    partnerSkipOffset: 7,
    vastLoadTimeout: 5,
    mediaLoadTimeout: 10,
    bufferingVideoTimeout: 10,
    alwaysCustomSkip: true,
    isAutoRequestFocus: false,
    bitrate: 1024,
    skipText: "`Bỏ qua sau ${countdown} giây`",
    skippableText: "`Bỏ qua quảng cáo`",
    nonSkippableText: "`Quảng cáo kết thúc sau ${countdown} giây`",
    isUsePartnerSkipButton: true,
    segments: "1,2,3,11,22,33"
  };
  // STEP 1: init Wii SDK
  window.wiiSdk = new WI.InstreamSdk(sdkConfig);
  // STEP 2: start SDK
  window.wiiSdk.start();
  // Optional STEP 2.1: wiiSdk.setDuration(duration)
  window.wiiSdk.setDuration(video.duration);
  
  // LOOP: Update currentTime content
  const timer = setInterval(() => {
    if (!window.wiiSdk || video.paused || video.ended) {
      return;
    }
    // console.log('[CLIENT] Updating SDK with current time:', video.currentTime);
    window.wiiSdk.updateTime(video.currentTime, video.duration);
  }, 200);
}

// --- Event Listening & UI Control ---
window.addEventListener("message", function (event) {
  if (!event.data || !event.data.type) return;
  
  const eventType = event.data.type;
  if (eventType.startsWith('WII_')) return;
  
  console.log(`[CLIENT] Received ad event: ${eventType}`, event.data);
  
  // Ad break end events
  const adBreakEndEvents = ['PAUSE_CONTENT', 'COMPLETE', 'SKIPPED', 'ERROR', 'ALL_ADS_COMPLETED', 'END'];
  
  // STEP 6: Event: PAUSE_CONTENT
  if (eventType === 'PAUSE_CONTENT') {
    // Ad has loaded, we can pause content if not already paused
    if (!video.paused) video.pause();
    console.log('[CLIENT] Ad break starting, content paused.', window.wiiSdk);
    // STEP 8: Callback playAd to trigger ad break
    console.log('[CLIENT] Triggering ad break playback.', video.muted);
    window.wiiSdk.playAd(video.muted); // Trigger the ad break to play
  }
  
  // STEP 14: Event: START
  if (eventType === 'START') {
    // Hide content controls and render the custom skip button
    controls.classList.add('hidden');
    isAdPlaying = true;
  }
  
  if (eventType === 'ALL_ADS_COMPLETED') {
    // Show content controls and destroy the custom skip button
    console.log('[CLIENT] Ad break ended, resuming content.', isAdPlaying, video.paused);
    if (isAdPlaying && video.paused) {
      video.play();
      controls.classList.remove('hidden');
      isAdPlaying = false;
    }
  }
  
  if (eventType === 'REQUEST_EXIT_FULLSCREEN') {
    if (isInFullscreen()) {
      const exit =
        document.exitFullscreen ||
        document.webkitExitFullscreen ||
        document.mozCancelFullScreen ||
        document.msExitFullscreen;
      if (exit) {
        try {
          exit.call(document);
          console.log('[CLIENT] Exit fullscreen by SDK request');
        } catch (err) {
          console.warn('[CLIENT] exitFullscreen error:', err);
        }
      }
    }
  }
  
  if (eventType === 'REQUEST_ENTER_AD_FULLSCREEN') {
    if (!isInFullscreen() && videoContainer) {
      const req =
        videoContainer.requestFullscreen ||
        videoContainer.webkitRequestFullscreen ||
        videoContainer.mozRequestFullScreen ||
        videoContainer.msRequestFullscreen;
      if (req) {
        try {
          req.call(videoContainer);
          console.log('[CLIENT] Enter ad fullscreen by SDK request');
        } catch (err) {
          console.warn('[CLIENT] requestFullscreen error:', err);
        }
      }
    }
  }
  
  // --- Xử lý AD_PROGRESS (Tùy chọn) ---
  if (eventType === 'AD_PROGRESS') {
    // Tùy chọn xử lý dựa vào currentTime và duration của quảng cáo
  }
});

// --- Standard Player Progress Bar (MODIFIED) ---
video.addEventListener('timeupdate', () => {
  // Nếu đang chạy ad thì không update progress bar
  if (isAdPlaying || isNaN(video.duration)) {
    return;
  }
  
  const progressPercent = (video.currentTime / video.duration) * 100;
  progress.value = progressPercent;
  
  const formatTime = (seconds) => {
    const min = Math.floor(seconds / 60);
    const sec = Math.floor(seconds % 60).toString().padStart(2, '0');
    return `${min}:${sec}`;
  };
  time.textContent = `${formatTime(video.currentTime)} / ${formatTime(video.duration)}`;
});

progress.addEventListener('input', () => {
  if (isAdPlaying || isNaN(video.duration)) return;
  const seekTime = (progress.value / 100) * video.duration;
  video.currentTime = seekTime;
});
```
2.1. Diagram for instream

![Instream Diagram](/diagram.jpg)

3. Parameter

| Key                   | Description                                                                       |     Type |
|:----------------------|:----------------------------------------------------------------------------------|---------:|
| tenantId              | Identifier of the account created by the Broadcast Center                         |  integer |
| channelId             | Danh sách id của category của nội dung & cách nhau bằng dấu ","                   |   string |
| streamId              | Id nội dung                                                                       |   string |
| vastLoadTimeout       | Vast Load Timeout                                                                 |  integer |
| mediaLoadTimeout      | Media Load Timeout                                                                |  integer |
| bufferingVideoTimeout | Buffering Video Timeout                                                           |  integer |                                  
| partnerSkipOffset     | Skip Ad Duration                                                                  |  integer |                                  
| env                   | Override the API host url for development and testing                             | constant |
| deviceType            | Device Type                                                                       | constant |
| thirdPartyToken       | JWT Token from partner                                                            |   string |
| playerType            | Player Type                                                                       | constant |
| alwaysCustomSkip      | Decided to use custom skip button                                                 |  boolean |
| isAutoRequestFocus    | Decided to focus on skip button after skip time                                   |  boolean |
| bitrate               | Bitrate                                                                           |   number |
| title                 | Tiêu đề của nội dung                                                              |   string |
| tranId                | Mã giao dịch tạo từ server đối tác - client liên hệ server để biết thêm thông tin |   string |
| category              | Danh sach tiêu đề category của nội dung & cách nhau bằng dấu ","                  |   string |
| keyword               | Từ khoá tìm kiếm của nội dung (nếu có)                                            |   string |
| age                   | Tuổi (Nếu có)                                                                     |   number |
| gender                | Giới tính (nếu có)                                                                | constant |
| uid20                 | Unified id 2.0 (nếu có)                                                           |   string |
| segments              | Danh sách segment id của user                                                     |   string |

4. Constant

| Key         | Description                                                                                      |     
|:------------|:-------------------------------------------------------------------------------------------------|
| playerType  | WI.PlayerType.VIDEO_JS <br> WI.PlayerType.SHAKA <br> WI.PlayerType.HLS <br/>WI.PlayerType.AKAMAI |  
| deviceType  | WI.DeviceType.TV <br/> WI.DeviceType.WEB                                                         |  
| env         | WI.Environment.SANDBOX <br/> WI.Environment.PRODUCTION                                           |   
| contentType | WI.ContentType.TV <br/>WI.ContentType.FILM <br/>WI.ContentType.VIDEO                             | 
| gender      | WI.Gender.MALE <br/>WI.Gender.FEMALE <br/>WI.Gender.OTHER <br/>WI.Gender.NONE                    | 

5. Ads Callback

| Type      | Value            | Description                                   |
|:----------|:-----------------|:----------------------------------------------|
| EventType | HAVE_ADS         | Fired when have welcome ad                    |
| EventType | NO_ADS           | Fired when not have welcome ad                |
| EventType | REQUEST          | Fired when the ad requests                    |
| EventType | GET_ADS          | Fired when the ad link gotten                 |
| EventType | LOADED           | Fired when the ad loaded                      |
| EventType | START            | Fired when the ad starts playing              |
| EventType | IMPRESSION       | Fired when the impression URL has been pinged |
| EventType | COMPLETE         | Fired when the ad completes playing           |
| EventType | SKIPPED          | Fired when the ad is skipped by the user      |
| EventType | BUFFERING        | Fired when the ad is buffering                |
| EventType | ERROR            | Fired when the ad has an error                |
| EventType | DESTROY          | Fires when the ad destroyed                   |
| EventType | ALL_ADS_COMPLETE | Fires when no more ad will show up            |
