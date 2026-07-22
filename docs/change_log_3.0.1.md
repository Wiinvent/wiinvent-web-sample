### Version 3.0.1

Change log:
- Update SDK version to `3.0.1`.
- Bổ sung config `adPendingTime` cho tất cả request ads, gửi lên backend qua param `apt`.
- `adPendingTime` và `userImpressionLimit` hỗ trợ cả kiểu `string` và `number` trên tất cả SDK.
- `adPendingTime` và `userImpressionLimit` giữ nguyên giá trị `0`; khi không truyền hoặc truyền kiểu dữ liệu không hợp lệ, request gửi chuỗi rỗng.
- Kế thừa toàn bộ thay đổi từ version trước.

#### 1. SDK
```gradle
https://ima-sdk.netlify.app/3.0.1/banner-ima.js
https://ima-sdk.netlify.app/3.0.1/tv-ima.js
https://ima-sdk.netlify.app/3.0.1/web-ima.js
https://ima-sdk.netlify.app/3.0.1/welcome-ima.js
```

#### 2. Hướng dẫn cập nhật

Từ `3.0.1`, tất cả SDK hỗ trợ config `adPendingTime`; cả `adPendingTime` và `userImpressionLimit` nhận kiểu `string` hoặc `number`:

- `adPendingTime: string | number` — gửi lên backend qua param `apt`.
- `userImpressionLimit: string | number` — giới hạn số lần hiển thị quảng cáo theo user, gửi lên backend qua param `uil`.

Áp dụng cho InStream TV, InStream Web, Banner và Welcome Ads.

##### 2.1 InStream TV Ads

```js
wiiSdk = new WI.InstreamSdk({
  env: WI.ENV.SANDBOX,
  ...,
  userId: "user-id",
  adPendingTime: 0,
  userImpressionLimit: 0,
});
```

##### 2.2 InStream Web Ads

```js
wiiSdk = new WI.InstreamSdk({
  env: WI.ENV.SANDBOX,
  ...,
  userId: "user-id",
  adPendingTime: 0,
  userImpressionLimit: 0,
});
```

##### 2.3 Banner / Display Banner / Overlay Banner Ads

```js
wiiSdk = new AdSDK({
  env: AdSDK.ENV.SANDBOX,
  ...,
  userId: "user-id",
  adPendingTime: 0,
  userImpressionLimit: 0,
});
```

##### 2.4 Welcome Ads

```js
wiiSdkWelcome = new WiiSDKWelcomeTVC.WelcomeSdk({
  env: WiiSDKWelcomeTVC.Environment.SANDBOX,
  ...,
  userId: "user-id",
  adPendingTime: 0,
  userImpressionLimit: 0,
});
```

#### 3. Mô tả các tham số

| Config key | Request param | Description                                                        | Type     | Fallback |
|:-----------|:--------------|:-------------------------------------------------------------------|:---------|:---------|
| `userId`      | `uid`         | User ID của người dùng.                                            | `string` | `""`     |
| `adPendingTime`      | `apt`         |  Thời gian chờ xuất hiện giữa 2 lần quảng cáo, đơn vị giây.                     | `string \| number` | `""` |
| `userImpressionLimit`      | `uil`         | Giới hạn số lần hiển thị quảng cáo theo user trong ngày.                      | `string \| number` | `""` |

> Giá trị `0` và chuỗi rỗng đều được giữ nguyên trong request. Number không hữu hạn (`NaN`, `Infinity`) và các kiểu dữ liệu khác fallback thành chuỗi rỗng.
