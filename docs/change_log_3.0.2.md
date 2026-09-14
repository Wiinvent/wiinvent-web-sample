### Version 3.0.2

Change log:
- Update SDK version to `3.0.2`.
- Banner SDK hỗ trợ multi-slot cho một player: đối tác khai báo 3 `domId` (`center`, `bottom`, `square`) và gọi `start()` một lần. SDK render vào slot khớp `bannerAdSize` do ad server trả về.
- Bổ sung loại banner `PAUSE_LARGE_BANNER`, `CENTER_BANNER`, `TRANSPARENT_BANNER`.
- Bổ sung hằng số `AdSDK.SLOT_BY_AD_SIZE`, bảng ánh xạ `bannerAdSize` sang slot.
- `dismiss()` nhận thêm map hoặc array `domId` để dọn cả nhóm slot của một player.
- Kế thừa toàn bộ thay đổi từ version trước.

#### 1. SDK
```gradle
https://ima-sdk.netlify.app/3.0.2/banner-ima.js
https://ima-sdk.netlify.app/3.0.2/tv-ima.js
https://ima-sdk.netlify.app/3.0.2/web-ima.js
https://ima-sdk.netlify.app/3.0.2/welcome-ima.js
```

#### 2. Hướng dẫn cập nhật

##### 2.1 Bảng ánh xạ `bannerAdSize` sang slot

| `bannerAdSize` ad server trả về | Slot render | Tỷ lệ creative  |
|---|---|-----------------|
| `PAUSE_BANNER` | `bottom` | banner ngang cũ |
| `PAUSE_LARGE_BANNER` | `bottom` | banner ngang cũ |
| `CENTER_BANNER` | `center` | `16:9`          |
| `TRANSPARENT_BANNER` | `square` | `1:1`           |

SDK giữ nguyên tỷ lệ gốc của creative và canh giữa slot. Slot lệch tỷ lệ vẫn hiển thị đúng nhưng creative bị thu nhỏ, nên đặt slot `center` là khung `16:9` và slot `square` là khung vuông.

##### 2.2 Khai báo slot

Mỗi player khai báo 3 slot, tự đặt vị trí và kích thước. SDK chỉ render creative vào slot, không chỉnh layout:

```html
<div class="player">
  <video id="mainVideo" controls></video>
  <div id="pauseBannerBottom" class="ad-slot bottom"></div>
  <div id="pauseBannerCenter" class="ad-slot center"></div>
  <div id="pauseBannerSquare" class="ad-slot square"></div>
</div>
```

##### 2.3 Gọi `start()` một lần cho cả 3 slot

```js
var PAUSE_SLOTS = {
  bottom: 'pauseBannerBottom',   // PAUSE_BANNER, PAUSE_LARGE_BANNER
  center: 'pauseBannerCenter',   // CENTER_BANNER
  square: 'pauseBannerSquare'    // TRANSPARENT_BANNER
};

video.addEventListener('pause', function () {
  bannerSdk.start(PAUSE_SLOTS, AdSDK.BANNER_TYPE.OVERLAY, '', '<PAUSE_POSITION_ID>', function (result) {
    // result.domId: slot đã render, null khi lỗi hoặc bannerAdSize không khớp slot nào
  });
});

video.addEventListener('play', function () {
  bannerSdk.dismiss(PAUSE_SLOTS);   // dọn cả 3 slot
});
```

Hành vi:

- Một `start()` là một request. SDK chọn slot theo `bannerAdSize` trong response.
- Chỉ một slot hiển thị tại một thời điểm: SDK tự `dismiss()` hai slot còn lại trước khi render.
- Delay `delayOffSet` của `OVERLAY` tính cho cả nhóm 3 slot.
- `bannerAdSize` không nằm trong bảng ánh xạ thì không render, callback trả `status: 'error'` và `data` là response gốc.
- Event `start`, `loaded`, `rendered`, `dismiss` vẫn phát kèm `domId` là slot thực tế.
- Key slot chấp nhận `bottom`, `bottomDomId` hoặc `bottomId`, tương tự cho `center` và `square`.

##### 2.4 Tương thích ngược

`start('<domId>', bannerType, adSize, positionId, callback)` với `domId` dạng chuỗi giữ nguyên hành vi cũ. Đối tác chỉ có một vị trí pause banner thì không cần chuyển sang multi-slot.
