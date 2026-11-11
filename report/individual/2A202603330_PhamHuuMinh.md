# Báo cáo thực hành Day 3 — Image Segmentation

- Mã học viên theo lớp: 2A202603330 (Pham Huu Minh, L2)
- Ngày / CVAT local: buổi lab 2026-11-11, hoàn thiện đêm 2026-11-12 / CVAT Community v2.74.1 chạy local qua Docker Desktop + WSL2 Ubuntu, truy cập ở `http://localhost:8080`
- Công cụ đã dùng: Brush và Polygon trong CVAT cho toàn bộ mask. Có thử bật SAM dưới dạng nuclio serverless function trên CVAT local nhưng không dùng được (chi tiết ở cuối mục 4). Có dùng trợ lý AI (Claude) cho các việc không phải gán nhãn: dựng và sửa môi trường Docker/CVAT/nuclio, kiểm cấu trúc ZIP sau mỗi lần export, chạy và giải thích scorer, và soạn bản nháp báo cáo này. Mọi mask đều do tôi tự vẽ và tự quyết định class.

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `submissions/easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `submissions/medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `submissions/hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `submissions/cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `submissions/cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `submissions/cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `submissions/cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `submissions/cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `submissions/cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Format export theo loại task: semantic (`easy_semantic`, `cp3_thin`, `cp4_curb`, `cp6_coverage`) xuất `Segmentation mask 1.1`; instance và panoptic (`medium_instance`, `hard_panoptic`, `cp1_holes`, `cp2_slice`, `cp5_occlusion`) xuất `COCO 1.0`.

Cả 9 task đều đã vẽ, đã Save và đã export. Không có task nào bỏ trống, không có ZIP rỗng.

## 2. Một quyết định trước khi dùng gợi ý

> Quy tắc của bài: object đầu tiên ở `medium_instance` phải **tự vẽ trước khi xem bất kỳ đề xuất tự động nào** cho object đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: ảnh `000000181542.jpg` (ảnh đầu của task, 640×600). Object đầu tiên tôi vẽ là một `person`, polygon 102 đỉnh, nằm lệch về bên trái so với tâm ảnh, khung bao `x 182–305, y 128–464`. Đây là người ngồi trên xe máy ở phía trái-giữa khung hình: khung bao của người nằm gọn trong dải ngang của object `motorcycle` bên dưới và vươn lên cao hơn mép trên của xe.

- Class và quy tắc tôi dùng để chọn biên: class `person`. Tôi chỉ bám theo phần người nhìn thấy được. Chiếc xe máy người đó ngồi lên là một object `motorcycle` riêng (khung bao `x 29–363, y 218–407`), không gộp chung vào mask người, vì phiếu quy tắc yêu cầu mỗi vật đếm được là một mask riêng. Mask người kéo xuống tới `y = 464`, tức là qua khỏi mép dưới của xe, lấy hết phần chân còn nhìn thấy và dừng ở chỗ chân bị thân xe che. Tôi không vẽ phần bị che và không lấy bóng đổ vào mask.

- Thứ tự làm của tôi: vẽ hết `person` trên ảnh trước, bắt đầu từ người có diện tích lớn nhất, rồi mới sang phương tiện. Bốn object tiếp theo sau object đầu cũng đều là `person`; object lớn nhất của ảnh là một `motorcycle` nhưng tôi vẽ nó ở lượt thứ mười.

- Nếu dùng gợi ý sau đó: không dùng. SAM trên CVAT local không chạy được (chi tiết ở cuối mục 4), nên toàn bộ 63 object của task này đều là polygon tôi tự vẽ tay, không có object nào sinh ra từ gợi ý tự động.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `cp3_thin` (`839f7736-abe28069.jpg`) và `cp6_coverage` (`7daa6479-67988f3f.jpg`). Lỗi ở mức cả task, không phải một vùng riêng lẻ.
- Lỗi thuộc loại: khác — chọn sai format khi export.
- Bằng chứng tôi nhìn thấy: sau khi export tôi mở lại hai file ZIP để kiểm. Bên trong chỉ có `annotations/instances_default.json`. Một bản `Segmentation mask 1.1` đúng phải có thư mục `SegmentationClass/` chứa PNG và file `labelmap.txt`. Đối chiếu với `easy_semantic.zip` và `cp4_curb.zip` của chính tôi thì hai file đó có đúng cấu trúc ấy, nên khác biệt nằm ở format chứ không phải ở dữ liệu.
- Quy tắc và hành động sửa: `GUIDE.md` và `CHECKPOINTS.md` quy định task semantic xuất `Segmentation mask 1.1`, còn tôi đã chọn nhầm `COCO 1.0`. Tôi mở lại job `cp3_thin` và `cp6_coverage` trong CVAT rồi export lại đúng format. Annotation trong CVAT còn nguyên nên không phải vẽ lại. Tôi không sửa tay file bên trong ZIP, vì README nói rõ "Đừng sửa JSON/PNG trong ZIP để làm cho kiểm tra xanh".
- Sau sửa đã Save và export lại chưa? Rồi. Kiểm lại sau khi export: `cp3_thin.zip` có 1 PNG trong `SegmentationClass/` và `labelmap.txt` 5 dòng (`background`, `pole`, `road`, `sky`, `traffic sign`); `cp6_coverage.zip` có 1 PNG và `labelmap.txt` 8 dòng, đủ 7 class của task cộng `background`.

Kết quả tự đánh giá: **25.0 / 82** — easy 13.6/20 (mIoU 0.707), medium 3.9/32 (metric 0.454), hard 7.5/30 (PQ 0.312). Các số này có **sau khi** ground truth ba tier được phát, nên chúng đo mức khớp với reference chứ không chứng minh chất lượng bản tự làm trước đó. Bản export trước lúc xem đáp án đạt 20.3/82. Sáu checkpoint không có reference nên tôi không tự chấm. Scorecard ba tier tối đa 82, không phải điểm cuối trên 100.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `cp4_curb`, `7d83710e-4697c3b2.jpg` — dải đất và cỏ có cây và túi rác, nằm giữa bó vỉa và phần vỉa hè lát | (a) thuộc `sidewalk` vì nằm trong phạm vi dành cho người đi bộ; (b) không thuộc class nào vì không phải mặt đường cũng không phải lối đi lát | Task chỉ có 2 class `road` và `sidewalk`. Trong taxonomy đầy đủ của Cityscapes vùng này là `terrain`, nhưng `terrain` không có trong task. Phiếu quy tắc: "Không ép đoán cho đủ coverage" | Tôi chọn (b), để trống dải đất đó. Hỏi coach: trong task chỉ có 2 class, vùng `terrain` nên để trống hay gộp vào `sidewalk`? |
| 2. `cp6_coverage`, `7daa6479-67988f3f.jpg` — các vùng không chắc có thuộc 7 class của task hay không | (a) phủ hết mọi pixel để không còn khoảng trống; (b) chỉ phủ vùng xác định rõ, để trống vùng không chắc | Hai tài liệu của bài mâu thuẫn: `data/manifest.json` ghi "label every pixel — no unlabeled gaps", còn `RUBRIC.md` liệt "tô bừa vùng không chắc" là lỗi và phiếu quy tắc ghi "Không ép đoán cho đủ coverage" | Tôi theo `RUBRIC.md` và phiếu quy tắc, tức là (b). Hỏi coach: câu trong `manifest.json` có phải kế thừa từ starter Day 5 và không áp dụng cho Day03 không? |
| 3. `hard_panoptic`, cả hai ảnh — người đang ngồi trên xe máy hoặc xe đạp | (a) là một `person` riêng, tách khỏi `motorcycle`; (b) không gán nhãn riêng vì người và xe nhìn như một khối | Danh sách 12 class của task có `person`, `motorcycle`, `bicycle` nhưng không có class `rider`. Tài liệu `Semantic_Segmentation_Annotation_Guideline_V1.docx` của chương trình có phân biệt Person và Rider, nhưng đó là taxonomy 19 class của track khác | Tôi chọn (a), vẽ người thành `person` riêng. Hỏi coach: khi taxonomy không có `rider`, người điều khiển phương tiện có đúng là gán `person` không? |

Ghi chú thêm về công cụ. Tôi có thử bật SAM trên CVAT local dưới dạng nuclio serverless function. Function deploy xong và báo trạng thái `ready`, nhưng lần tương tác đầu CVAT trả về `500 Server Error`. Sau một lần restart container, port publish của function đổi từ 55496 sang 52032 và CVAT báo `Network is unreachable` vì vẫn gọi port cũ. Tôi không sửa kịp trong thời gian còn lại nên làm toàn bộ bài bằng Brush và Polygon. Ghi lại ở đây vì `RULES.md` yêu cầu ghi đúng công cụ thật đã dùng.
