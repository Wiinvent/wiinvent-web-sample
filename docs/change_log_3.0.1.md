### Version 3.0.1

Change log:
- Update SDK version to `3.0.1`.
- Bổ sung config `apt` cho tất cả request ads, gửi lên backend qua param `apt`.
- `apt` và `uil` hỗ trợ cả kiểu `string` và `number` trên tất cả SDK.
- `apt` và `uil` giữ nguyên giá trị `0`; khi không truyền hoặc truyền kiểu dữ liệu không hợp lệ, request gửi chuỗi rỗng.
- Kế thừa toàn bộ thay đổi từ version trước.

#### 1. SDK
```gradle
https://ima-sdk.netlify.app/3.0.1/banner-ima.js
https://ima-sdk.netlify.app/3.0.1/tv-ima.js
https://ima-sdk.netlify.app/3.0.1/web-ima.js
https://ima-sdk.netlify.app/3.0.1/welcome-ima.js
```

#### 2. Hướng dẫn cập nhật

Từ `3.0.1`, tất cả SDK hỗ trợ config `apt`; cả `apt` và `uil` nhận kiểu `string` hoặc `number`:

- `apt: string | number` — gửi lên backend qua param `apt`.
- `uil: string | number` — giới hạn số lần hiển thị quảng cáo theo user, gửi lên backend qua param `uil`.

Áp dụng cho InStream TV, InStream Web, Banner và Welcome Ads.

##### 2.1 InStream TV Ads

```js
wiiSdk = new WI.InstreamSdk({
  env: WI.ENV.SANDBOX,
  ...,
  uid: "user-id",
  apt: 0,
  uil: 0,
});
```

##### 2.2 InStream Web Ads

```js
wiiSdk = new WI.InstreamSdk({
  env: WI.ENV.SANDBOX,
  ...,
  uid: "user-id",
  apt: 0,
  uil: 0,
});
```

##### 2.3 Banner / Display Banner / Overlay Banner Ads

```js
wiiSdk = new AdSDK({
  env: AdSDK.ENV.SANDBOX,
  ...,
  uid: "user-id",
  apt: 0,
  uil: 0,
});
```

##### 2.4 Welcome Ads

```js
wiiSdkWelcome = new WiiSDKWelcomeTVC.WelcomeSdk({
  env: WiiSDKWelcomeTVC.Environment.SANDBOX,
  ...,
  uid: "user-id",
  apt: 0,
  uil: 0,
});
```

#### 3. Mô tả các tham số

| Config key | Request param | Description                                                        | Type     | Fallback |
|:-----------|:--------------|:-------------------------------------------------------------------|:---------|:---------|
| `uid`      | `uid`         | User ID của người dùng.                                            | `string` | `""`     |
| `apt`      | `apt`         | Giá trị `apt` gửi kèm request ads lên backend.                     | `string \| number` | `""` |
| `uil`      | `uil`         | Giới hạn số lần hiển thị quảng cáo theo user.                      | `string \| number` | `""` |

> Giá trị `0` và chuỗi rỗng đều được giữ nguyên trong request. Number không hữu hạn (`NaN`, `Infinity`) và các kiểu dữ liệu khác fallback thành chuỗi rỗng.
