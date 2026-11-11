# Báo cáo thực hành Day 3 — Image Segmentation

- Mã học viên theo lớp: 2A202603330 (Pham Huu Minh, L2)
- Ngày / CVAT local: 2026-11-11 / CVAT Community v2.74.1 chạy local qua Docker Desktop + WSL2 Ubuntu, truy cập ở `http://localhost:8080`
- Công cụ đã dùng: «ĐIỀN — ghi đúng công cụ thật sự đã dùng. Ví dụ: Brush và Polygon trong CVAT; có/không dùng Intelligent Scissors; không có SAM trên CVAT local. Nếu có dùng trợ lý AI cho phần chuẩn bị môi trường hoặc kiểm cấu trúc ZIP thì ghi rõ ở đây.»

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `submissions/easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `submissions/medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `submissions/hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | chưa có | 0 / 1 | 3 |
| cp2_slice | chưa có | 0 / 1 | 3 |
| cp5_occlusion | chưa có | 0 / 1 | 3 |
| cp3_thin | chưa có | 0 / 1 | 3 |
| cp4_curb | chưa có | 0 / 1 | 3 |
| cp6_coverage | chưa có | 0 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Format export theo loại task: semantic (`easy_semantic`, `cp3_thin`, `cp4_curb`, `cp6_coverage`) xuất `Segmentation mask 1.1`; instance và panoptic (`medium_instance`, `hard_panoptic`, `cp1_holes`, `cp2_slice`, `cp5_occlusion`) xuất `COCO 1.0`.

Task chưa làm hoặc export lỗi: «ĐIỀN "chưa có" cho từng task tương ứng, kèm dữ liệu đã Save đến đâu và lỗi đã báo coach. Không tạo ZIP rỗng.»

## 2. Một quyết định trước khi dùng gợi ý

> Quy tắc của bài: object đầu tiên ở `medium_instance` phải **tự vẽ trước khi xem bất kỳ đề xuất tự động nào** cho object đó. Ghi đủ để coach tìm lại được vùng đó trên ảnh.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: «ĐIỀN tên ảnh (một trong `000000181542.jpg`, `000000373353.jpg`, `000000458325.jpg`), vị trí trong ảnh, và object là gì»
- Class và quy tắc tôi dùng để chọn biên: «ĐIỀN class trong 6 class của task (person, bicycle, car, motorcycle, bus, truck) và lý do bạn dừng mask ở ranh đó — ví dụ bám phần nhìn thấy, không vẽ phần bị che, không lấy bóng đổ»
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: «ĐIỀN hoặc ghi "không dùng gợi ý"»
- Nếu không dùng gợi ý: «ĐIỀN một quyết định gán nhãn của mình và lý do»

## 3. Một lỗi tôi tìm thấy và sửa

> Phải là một lỗi **có thật** bạn quan sát được trong bài của mình. Nếu chưa sửa được thì ghi đã thử gì và cần coach hỗ trợ gì, không ghi "đã sửa".

- Task/ảnh/vùng: «ĐIỀN»
- Lỗi thuộc loại (sai lớp / thiếu-thừa vật / gộp-tách / biên / phủ vùng / khác): «ĐIỀN»
- Bằng chứng tôi nhìn thấy: «ĐIỀN dấu hiệu quan sát được trên ảnh hoặc trong danh sách Objects của CVAT»
- Quy tắc và hành động sửa: «ĐIỀN quy tắc đã áp dụng và việc đã làm»
- Sau sửa đã Save và export lại chưa? «ĐIỀN»

Kết quả tự đánh giá (nếu đã xem Summary trên GitHub Actions hoặc tự chạy script): «ĐIỀN hoặc ghi "chưa có điểm"». Scorecard ba tier tối đa 82, không phải điểm cuối trên 100.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

> Mỗi ca là một **vùng cụ thể**, không phải nhận xét chung. Ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1 «ĐIỀN» | «ĐIỀN» | «ĐIỀN» | «ĐIỀN» |
| 2 «ĐIỀN» | «ĐIỀN» | «ĐIỀN» | «ĐIỀN» |
| 3 «ĐIỀN» | «ĐIỀN» | «ĐIỀN» | «ĐIỀN» |

> Gợi ý ba vùng thường gây cân nhắc thật trong bộ ảnh này, nhưng **phải tự xem ảnh rồi mới ghi**:
> ranh `road`–`sidewalk` ở `cp4_curb` khi bó vỉa bị xe hoặc bóng che; phần cột đỡ của biển báo ở
> `cp3_thin` thuộc `pole` hay `traffic sign`; và ở `cp6_coverage` những vùng không chắc có thuộc
> 7 class của task hay không — `data/manifest.json` nói "label every pixel" nhưng RUBRIC.md và
> phiếu quy tắc đều cấm tô ép vùng chưa chắc, nên đây là một câu hỏi hợp lệ cho coach.
