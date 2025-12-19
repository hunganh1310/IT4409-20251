# Hướng dẫn Deploy Application

## 📋 Tổng quan
- **Backend**: Render (kết nối với GitHub)
- **Frontend**: Firebase Hosting
- **Database**: MongoDB Atlas

---

## 🔧 1. Cấu hình Backend trên Render

### Bước 1: Thêm Environment Variables trên Render
Vào dashboard Render → chọn service → **Environment** → thêm các biến:

```
MONGODB_URI=mongodb+srv://20225164:hunganh1310@20225164.vaxqvdk.mongodb.net/?appName=20225164
PORT=3001
FRONTEND_URL=https://YOUR-FIREBASE-APP.web.app
```

**Lưu ý**: 
- `PORT` có thể để Render tự động gán (thường là 10000)
- `FRONTEND_URL` sẽ cập nhật sau khi deploy Firebase

### Bước 2: Cấu hình Build Settings
Nếu chưa có, đảm bảo Render có settings:
- **Build Command**: `cd backend && npm install`
- **Start Command**: `cd backend && npm start`
- **Root Directory**: `/` (hoặc `/backend` nếu deploy riêng backend)

### Bước 3: Lấy Backend URL
Sau khi deploy thành công, Render sẽ cung cấp URL dạng:
```
https://your-app-name.onrender.com
```
**LƯU LẠI URL NÀY** để cấu hình frontend!

---

## 🎨 2. Cấu hình Frontend cho Firebase

### Bước 1: Cập nhật config.js với Backend URL
Mở file `frontend/public/config.js` và thay đổi:

```javascript
const API_CONFIG = {
    BASE_URL: 'https://your-app-name.onrender.com'  // ← Thay URL Render vào đây
};
```

### Bước 2: Deploy lên Firebase
```bash
cd frontend/public

# Nếu chưa cài Firebase CLI
npm install -g firebase-tools

# Đăng nhập Firebase
firebase login

# Khởi tạo (nếu chưa)
firebase init hosting

# Deploy
firebase deploy
```

**Cấu hình firebase.json** (nếu hỏi):
```json
{
  "hosting": {
    "public": ".",
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```

### Bước 3: Lấy Firebase URL
Firebase sẽ cung cấp URL dạng:
```
https://your-project-id.web.app
```

---

## 🔄 3. Cập nhật CORS trên Backend

Sau khi có Firebase URL, quay lại **Render Environment Variables** và cập nhật:

```
FRONTEND_URL=https://your-project-id.web.app
```

Sau đó **Redeploy** backend trên Render để áp dụng thay đổi.

---

## ✅ 4. Kiểm tra

### Test Backend API
```bash
curl https://your-app-name.onrender.com/api/users
```

### Test Frontend
Truy cập: `https://your-project-id.web.app`

Kiểm tra:
- [ ] Trang load được
- [ ] Danh sách users hiển thị
- [ ] Tạo user mới thành công
- [ ] Sửa user thành công
- [ ] Xóa user thành công
- [ ] Tìm kiếm hoạt động

---

## 🚨 Troubleshooting

### Lỗi CORS
**Triệu chứng**: "CORS policy: No 'Access-Control-Allow-Origin' header"

**Giải pháp**:
1. Kiểm tra `FRONTEND_URL` trên Render có đúng không
2. Redeploy backend sau khi thay đổi
3. Nếu vẫn lỗi, tạm thời đặt `FRONTEND_URL=*` để test

### Lỗi kết nối MongoDB
**Triệu chứng**: "MongoDB Error: connect ECONNREFUSED"

**Giải pháp**:
1. Kiểm tra `MONGODB_URI` trên Render
2. Vào MongoDB Atlas → Network Access → thêm IP `0.0.0.0/0` (allow all)

### Frontend không call được API
**Triệu chứng**: "Failed to fetch" trên console

**Giải pháp**:
1. Kiểm tra `config.js` có đúng Backend URL không
2. Kiểm tra Backend có đang chạy không (test bằng curl)
3. Mở DevTools → Network tab để xem request

### Backend sleep trên Render (free tier)
**Triệu chứng**: Request đầu tiên mất 30-50s

**Giải pháp**: Render free tier sẽ sleep sau 15 phút không dùng. Đợi backend wake up.

---

## 📝 Tóm tắt URLs cần có

| Service | URL Placeholder | Thay bằng |
|---------|----------------|-----------|
| Backend (Render) | `YOUR_RENDER_APP_URL` | `https://your-app-name.onrender.com` |
| Frontend (Firebase) | `YOUR_FIREBASE_URL` | `https://your-project-id.web.app` |
| MongoDB | Đã có sẵn | `mongodb+srv://...` |

---

## 🎯 Checklist Deploy

- [ ] Backend đã cài `dotenv` dependency
- [ ] Environment variables đã được thêm vào Render
- [ ] Backend deploy thành công trên Render
- [ ] Đã lấy và test Backend URL
- [ ] Đã cập nhật `config.js` với Backend URL
- [ ] Frontend deploy thành công lên Firebase
- [ ] Đã lấy Firebase URL
- [ ] Đã cập nhật `FRONTEND_URL` trên Render
- [ ] Đã redeploy Backend sau khi thay CORS
- [ ] Test toàn bộ chức năng trên production

---

**Chúc bạn deploy thành công! 🚀**
