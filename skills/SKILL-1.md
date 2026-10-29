# Video Lời Phật

Làm video dọc 9:16 phong cách Phật giáo/chữa lành: ảnh tĩnh được AI làm thành cảnh chuyển động thật, giọng đọc trầm ấm, phụ đề karaoke chạy theo từng chữ, nhạc nền nhẹ (tùy chọn). Dành để bàn giao cho người mới; khi họ gõ "Hãy hướng dẫn tôi", thu thập thông tin từng bước rồi mới làm.

## Bắt đầu: Hướng dẫn thiết lập

- Khi người dùng gõ "Hãy hướng dẫn tôi", chào họ và giải thích ngắn gọn: skill này làm video lời Phật từ ảnh của họ — ảnh động thật, giọng đọc trầm ấm, phụ đề karaoke, nhạc nền tùy chọn.

- Hỏi lần lượt từng thông tin sau, mỗi tin một câu hỏi rõ ràng, chờ họ trả lời xong mới hỏi tiếp:
  1. **Kênh muốn tham khảo phong cách**: gửi link kênh TikTok (ví dụ kênh An Nhiên). Nếu không có, bỏ qua — dùng công thức mặc định.
  2. **File ảnh**: nhờ họ tải lên 4–6 ảnh Phật/cảnh thiền (ảnh dọc hoặc vuông đều được). Ảnh các video **không được trùng nhau**.
  3. **Nội dung lời thoại**: họ gửi nguyên văn lời thoại (7–8 câu ngắn, mỗi câu 1–2 dòng), hoặc cho chủ đề (ví dụ "biết ơn", "buông bỏ") để AI viết giúp, hoặc nhờ chép lại từ video mẫu của kênh trên.
  4. **File nhạc nền (không bắt buộc)**: nếu muốn có nhạc, nhờ họ gửi file nhạc (mp3). Nhắc họ: nhạc phải **không có lời hát**. Nếu không gửi, video sẽ chỉ có giọng đọc.
  5. **Giọng đọc**: mặc định dùng giọng C (nam trầm ấm, `avocado_v2:vd2_smoky_teacup`, tốc độ 95, cao hơn 7%). Hỏi họ có muốn giữ mặc định không.
  6. **Phụ đề**: mặc định karaoke serif nghiêng trắng đen, chữ chạy theo từng chữ khi đọc. Hỏi họ có muốn giữ mặc định không.

- Khi đã thu đủ thông tin, trình bày lại toàn bộ thành bảng tóm tắt và hỏi: "Bạn kiểm tra lại giúp mình, đúng hết chưa? Nếu đúng thì mình bắt đầu làm."

- Chỉ khi họ xác nhận đúng, mới điền các giá trị vào những chỗ trống [...] trong các phần dưới đây rồi tiến hành. Không tự điền giá trị đoán mò.

## Chuẩn bị

- Tạo thư mục làm việc: `~/workspace/[ten-video]/work/`, file thoại `script.txt`, ảnh nguồn, file nhạc (nếu có).
- Video thành phẩm lưu ở `~/workspace/your_files/[ten-file].mp4`.

## Quy trình làm video

### 1. Phân tích mẫu (nếu có link kênh)

- Dùng `yt-dlp` tải 1–2 video viral nhất của kênh về xem: phong cách hình ảnh, nhịp cắt, giọng đọc, nhạc, phụ đề.
- Ghi nhận lại để giữ đúng "chất" khi làm video mới. Không sao chép y nguyên nội dung nếu người dùng muốn nội dung mới.

### 2. Làm ảnh động (quan trọng nhất)

- Dùng `media.generate_video` với từng ảnh: mỗi ảnh thành **1 clip 10 giây chuyển động thật** (người bước đi, áo bay, lá đung đưa, nước chảy, mây trôi...), không dùng hiệu ứng zoom tĩnh (Ken Burns) vì trông đứng yên.
- Prompt mẫu: "Animate this [mô tả ảnh] into a living scene: [3–4 chuyển động cụ thể]. Slow, calm, meditative motion throughout."
- Lưu ý: AI có thể vẽ lại cảnh hơi khác ảnh gốc — chấp nhận được, miễn là đẹp và hợp chủ đề.
- Tối đa 6 video mỗi tin nhắn người dùng.

### 3. Giọng đọc

- Thu bằng TTS: giọng `avocado_v2:vd2_smoky_teacup`, tiếng Việt, tốc độ 95.
- Xử lý hậu kỳ: `rubberband=pitch=1.07` (cao vang hơn chút), `aecho=0.8:0.9:45:0.18` (vang nhẹ), `adelay=300|300` (trễ 0.3s cho khớp hình).
- Đo thời lượng narration để tính số cảnh: mỗi cảnh 10s, trừ 1s chuyển cảnh. Ví dụ narration 44s → 5 cảnh = 46s video.

### 4. Phụ đề karaoke

- Dùng faster-whisper với `word_timestamps=True` để lấy mốc thời gian **từng chữ**.
- Tạo file `.ass` với hiệu ứng `{\kf}` cho từng chữ: chữ đã đọc hiện màu trắng, chữ chưa đọc màu xám, viền đen.
- Phông mặc định: **Noto Serif Italic, cỡ 40**, căn giữa dưới. Mỗi câu thoại là 1 event, ngắt dòng mỗi ~6 chữ.
- Lưu ý kỹ thuật: trong ASS, dấu `\N` xuống dòng phải viết cẩn thận; kiểm tra số chữ khớp giữa script và whisper.

### 5. Nhạc nền (nếu có file)

- Kiểm tra file nhạc người dùng gửi: đo âm lượng bằng `volumedetect`. Nếu quá nhỏ (mean < -30dB), tăng gain cho đủ nghe (ví dụ `volume=12dB`), kiểm tra không rè/vỡ.
- Nhạc phải không có lời hát — nếu phát hiện có lời, báo người dùng đổi bản khác.
- Trộn nhạc dưới giọng đọc bằng **sidechain ducking** (nhạc tự nhỏ xuống khi có giọng đọc), mức nhạc khoảng 0.5–0.8.
- Nếu amix cho ra file câm/lỗi: tách làm 2 bước (bước 1 xử lý giọng, bước 2 trộn nhạc), rồi mới ráp vào video.

### 6. Ráp video cuối

- Chuẩn hóa mọi clip về cùng kích thước trước khi nối: `scale=720:1280:force_original_aspect_ratio=increase,crop=720:1280,fps=30,setsar=1` (các clip AI thường lệch nhau vài pixel, không chuẩn hóa sẽ lỗi xfade).
- Nối bằng `xfade` (fade 1s, offset cách nhau 9s).
- Phủ lớp hạt sáng bay: blend `screen` — **bắt buộc chạy ở RGB** (`format=gbrp`), blend ở YUV sẽ bị ám tím.
- Thêm `vignette` nhẹ, burn phụ đề `.ass`, xuất **30fps CFR** (`fps=30`, `-r 30`) để tránh lỗi không chạy được trên máy người xem.
- Thêm `-movflags +faststart` để video tải nhanh.

### 7. Kiểm tra trước khi giao

- `ffprobe`: video và audio đủ thời lượng, đúng 30fps.
- Decode toàn file không lỗi.
- Trích vài khung hình kiểm tra: phụ đề hiển thị đúng, chữ karaoke chạy đúng, không che mặt nhân vật.
- Nghe kiểm tra: giọng đọc rõ, nhạc vừa phải, không có đoạn vang đột ngột (kiểm tra max_volume từng đoạn 10s nếu nghi ngờ).

## Bài học kỹ thuật (đừng lặp lại lỗi cũ)

- faster-whisper: unset `no_proxy`/`NO_PROXY` trước khi tải model; dùng PyAV bị lỗi thì đưa numpy audio trực tiếp vào `model.transcribe`, không truyền đường dẫn file.
- `adelay=300|300` trên audio mono vẫn chạy được.
- Chuỗi filter dài `rubberband+aecho+adelay+apad+afade` + `amix` trong một lần có thể cho ra audio câm — tách 2 pass cho chắc.
- Đừng tin metadata giọng TTS ghi "nam" — kiểm tra tần số cơ bản (F0) thực tế: nam thường < 165Hz.
- Đặt tên file thành phẩm rõ ràng, có số phiên bản (v2, v3...) khi làm lại.
