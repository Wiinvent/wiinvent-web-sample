### Version 3.0.0

Change log:
- Update SDK version to `3.0.0`.
- Bổ sung 2 tham số mới cho tất cả request ads (gửi lên backend): `uid` (userId) và `userImpressionLimit`.
- Đổi tên tham số `uid20` thành `uid` trong `AdsRequestData` (InStream). **Breaking change**: `.uid20(...)` không còn, dùng `.uid(...)`.
- Kế thừa toàn bộ thay đổi từ version trước.

#### 1. SDK
```gradle
https://ima-sdk.netlify.app/3.0.0/banner-ima.js
https://ima-sdk.netlify.app/3.0.0/tv-ima.js
https://ima-sdk.netlify.app/3.0.0/web-ima.js
https://ima-sdk.netlify.app/3.0.0/welcome-ima.js
```

#### 2. Hướng dẫn cập nhật

Từ `3.0.0`, tất cả request data đều hỗ trợ 2 builder mới:

- `.uid(String)` — userId của người dùng, gửi lên backend qua param `uid`.
- `.userImpressionLimit(Int)` — giới hạn số lần hiển thị theo user, gửi lên backend qua param `uil`.

Áp dụng cho các manager: `wiiSdk`.

##### 2.1 InStream Ads — đổi `uid20` thành `uid` và thêm `userImpressionLimit`

`config init` không còn `"uid": ""`. Cập nhật như sau:

```js
wiiSdk = new AdSDK({  
  uid: "",
  uil: "",
});
```

##### 2.2 Banner Ads

```js
wiiSdk = new AdSDK({  
  uid: "",
  uil: "",
});
```

##### 2.3 Display Banner / Overlay Banner Ads

```js
wiiSdk = new AdSDK({  
  uid: "",
  uil: "",
});
```

##### 2.4 Welcome Ads

```js
wiiSdk = new AdSDK({  
  uid: "",
  uil: "",
});
```

#### 3. Mô tả các tham số

1. Parameter (bổ sung so với `version cũ`)

| Key                  | Description                                                                       |     Type |
|:---------------------|:----------------------------------------------------------------------------------|---------:|
| uid                  | User id của người dùng (thay cho `uid20`). Gửi lên backend qua param `uid`.        |   string |
| userImpressionLimit  | Giới hạn số lần hiển thị quảng cáo theo user. Gửi lên backend qua param `uil`.     |  integer |

> Lưu ý: `uid20` đã bị đổi tên thành `uid` trong `AdsRequestData` (InStream). Các DTO còn lại (`BannerAdsRequestData`, `DisplayBannerAdsRequestData`, `WelcomeAdsRequestData`) đã dùng `uid` từ trước, nay bổ sung thêm `userImpressionLimit`.