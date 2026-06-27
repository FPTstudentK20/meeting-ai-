# Meeting AI Assistant

Web app demo: upload file ghi âm cuộc họp → tự động **chuyển giọng nói thành văn bản**, **dịch sang tiếng Việt**, **tóm tắt** và **liệt kê action items**.

## Cấu trúc project

```
meeting-ai-assistant/
├── index.html        ← Frontend (UI, không cần build tool gì cả)
├── api/
│   └── process.js    ← Serverless Function (Vercel) gọi OpenAI API
├── package.json
└── README.md
```

## Các API/AI Model dùng trong app này (điền vào báo cáo)

| API/Model | Chức năng chính | Ưu điểm / Lý do chọn |
|---|---|---|
| **OpenAI Whisper (whisper-1)** | Speech-to-Text — chuyển audio cuộc họp thành văn bản | Độ chính xác cao, hỗ trợ tiếng Anh và tiếng Việt, dễ gọi qua REST API |
| **OpenAI GPT-4o-mini** | LLM — dịch văn bản sang tiếng Việt, tóm tắt nội dung, trích xuất action items | Rẻ hơn GPT-4o, đủ mạnh để xử lý văn bản ngắn-trung bình, trả JSON có cấu trúc dễ dùng (`response_format: json_object`) |

## Cách deploy lên Vercel

### Cách 1: Deploy bằng Vercel CLI (nhanh nhất)

1. Cài Vercel CLI (nếu chưa có):
   ```
   npm install -g vercel
   ```
2. Vào thư mục project, chạy:
   ```
   vercel
   ```
   Làm theo hướng dẫn (đăng nhập, chọn tên project...). Lần đầu deploy sẽ ra link dạng `https://ten-project.vercel.app`.
3. Set API key làm Environment Variable (để bảo mật, không hard-code key vào code):
   ```
   vercel env add OPENAI_API_KEY
   ```
   Dán API key (lấy ở https://platform.openai.com/api-keys), chọn môi trường Production.
4. Deploy lại để áp dụng env var:
   ```
   vercel --prod
   ```

### Cách 2: Deploy qua GitHub + Vercel Dashboard

1. Tạo repo GitHub, push toàn bộ project lên.
2. Vào https://vercel.com → **Add New Project** → chọn repo đó → **Import**.
3. Trong phần **Environment Variables**, thêm:
   - Key: `OPENAI_API_KEY`
   - Value: API key của bạn
4. Bấm **Deploy**.

## Lưu ý quan trọng

- **Giới hạn dung lượng file**: Vercel free tier giới hạn body request ~4.5MB, nên file audio nên dưới ~4MB (audio ngắn 1-3 phút, định dạng MP3 nén là ổn). Nếu cần xử lý file lớn hơn, cần nâng cấp logic upload (vd: upload trực tiếp lên storage rồi gửi link cho server xử lý) — ngoài phạm vi demo này.
- **Bảo mật API key**: Trong `index.html` hiện có ô nhập API key ở client để tiện demo nhanh không cần deploy ngay. Khi đã deploy thật và đặt `OPENAI_API_KEY` trong Environment Variables trên Vercel, bạn có thể xoá ô nhập key đó đi (server sẽ tự dùng key từ env var, không cần người dùng tự nhập) — an toàn hơn vì key không bị lộ ra phía trình duyệt.
- **Chi phí**: Whisper và GPT-4o-mini đều tính phí theo dung lượng/token dùng, khá rẻ cho demo (vài cent cho vài lần test).

## Test thử ở local (không cần deploy)

```
npx vercel dev
```
Lệnh này chạy local server giả lập đúng môi trường Vercel (kể cả thư mục `api/`), mở `http://localhost:3000` để test trước khi deploy thật.
