# Báo cáo cá nhân — Ngày 11
Họ tên: Nguyễn Khoa Minh               Mã học viên: `03160` (`minhnk`)  
Phiên bản CVAT (Help → About): CVAT v2.82.0  
Guideline: Road v1.0    Lần nộp (`revision` trong manifest): `2` (bản gốc `revision: 1` lưu trong `r1/`)  
Thời điểm export (ngày giờ trên CVAT): 2026-11-20 16:28 (UTC+7)    Số lần nộp lại sau Rework: 1  

## 1. Kiểm project
| Mục | Đúng như guideline Road v1.0? | Ghi chú |
| --- | --- | --- |
| 6 label, đúng kiểu | Có | `lane` (polyline), `road_curb` (polyline), `crosswalk` (polygon), `drivable` (polygon), `traffic_light` (rectangle), `traffic_sign` (rectangle) — đã đối chiếu với `configs/road_v1_labels.json`. |
| Thuộc tính và giá trị | Có | Đủ các thuộc tính `select` và `checkbox` (`edge_zone`), `mutable = false`. Khi vẽ mới trên CVAT phải mở tab Details để sửa giá trị mặc định đầu danh sách (`L1 / solid / white / single`). |
| 4 tag ảnh | Có | 4 label kiểu Tag (`camera`, `timeofday`, `weather`, `source`), mỗi tag có đúng 1 thuộc tính select tên `value`. |

Cách kết thúc polyline trên bản CVAT này: Sau khi click điểm cuối cùng ở xa nhất còn thấy vạch, nhấn phím **`N`** trên bàn phím (hoặc bấm lại nút công cụ Draw/Finish trên thanh bên trái).  
Cách đặt tag ảnh trên bản CVAT này: Chọn công cụ **Tag** ở thanh công cụ bên trái → chọn tên label tag (`camera` đặt đầu tiên, tiếp theo là `timeofday`, `weather`, `source`) → bấm **Tag** rồi mở mục **Details** của từng tag bên bảng **Objects** để chọn lại `value` cho đúng với ảnh.  

## 2. Số lượng đã gán
| Task | Ảnh | lane | road_curb | crosswalk | drivable | traffic_light | traffic_sign |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| front | 12 | 29 | 2 | 2 | 15 | 7 | 6 |
| front-extra (đã làm 0 / 4) | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| fisheye | 8 | 12 | 2 | 4 | 8 | 1 | 1 |
| fisheye-extra (đã làm 0 / 4) | 0 | 0 | 0 | 0 | 0 | 0 | 0 |

## 3. Bảng ca khó
| Ảnh | Nhãn | Phân vân giữa | Đã chọn | Lý do |
| --- | --- | --- | --- | --- |
| A03 | `lane#0` (`L1`) | Dừng polyline trước thân xe che khuất rồi vẽ đoạn mới hay vẽ xuyên qua vật che | Vẽ 1 polyline liền xuyên qua và bật `occluded = true` | Theo Guideline mục 3 (Làn): đoạn bị che ngắn bởi xe/người phải nối liền qua chỗ che và bật cờ `occluded`, không được dừng trước vật che hay tách làm 2 polyline. |
| A04 | `drivable` (`direct` vs `alternative`) | Có gộp phần sát lề phải hoặc làn bên trái vạch vàng đôi vào `direct` không | Tách riêng `direct` cho làn đang đi và `alternative` cho làn cùng chiều kế bên; bỏ dải đỗ xe sát lề | Theo Guideline mục 3 (Vùng lái): dải đỗ xe sát lề có chức năng dừng/đỗ nên không tính vào `direct`; làn bên cạnh đi được thì gán polygon riêng với `area_type = alternative`. |
| A08 | `traffic_light#5@(300,230)` & `traffic_light#6@(950,260)` | Đèn quay ngang không nhìn rõ bóng sáng (`off` hay `unknown`); đèn nhỏ ở xa cao ~6 px có gán không | Đèn ngang chọn `facing = side`, `color = unknown`; đèn nhỏ ở xa vẫn gán `red` | Đèn quay ngang bị khuất chóa không xác định chắc chắn đang tắt hay sáng nên gán `unknown` an toàn hơn `off`; cụm đèn nhỏ ở xa (< 8 px) không bắt buộc nhưng nhìn rõ màu đỏ nên vẫn vẽ box sát vỏ. |
| A10 | `traffic_sign#4@(1000,342)` | Gộp tấm biển chữ nhật nhỏ bên dưới vào chung 1 box với biển chính phía trên hay tách riêng | Tách thành 2 box riêng: biển chính `group = prohibitory`, tấm nhỏ bên dưới `group = supplementary` | Theo Guideline mục 3 (Đèn và biển): biển phụ (tấm nhỏ dưới biển chính) bắt buộc phải có hộp chữ nhật riêng với `group = supplementary`. |
| B02 | `traffic_light#3` & `traffic_sign#4` | Tâm box nằm gần viền cong của ống kính fisheye có cần bật `edge_zone` không | Bật `edge_zone = true` cho cả hai box | Khoảng cách từ tâm ảnh `(640, 483)` tới tâm hai box lần lượt khoảng `680 px` và `646 px`, đều lớn hơn 80% bán kính ngang (`0.8 * 640 = 512 px`) nên phải bật `edge_zone`. |
| B05–B08 | `lane#0` trên camera `MVL` / `MVR` | Gán `position = L1 / R1` theo hướng nhìn của camera hông hay gán `other` | Chọn `position = other` cho mọi vạch làn trên ảnh `B05, B06, B07, B08` | Theo Guideline mục 4 (Fisheye): `L1/R1/L2/R2` chỉ dùng cho `fisheye_front` và `fisheye_rear`; trên camera hai bên (`fisheye_left`, `fisheye_right`) luôn phải đặt `position = other`. |

## 4. Phiếu QC 1 nhận được
Bạn kiểm lô của tôi: `tranhoangan`  Quyết định nhận được: **Release** (kèm 2 góp ý lỗi chính ở bản `revision 1`, đã sửa trong giờ Buffer và xuất lại `revision 2`)  
| Mã lỗi | Số lỗi | Đồng ý? | Đã sửa? |
| --- | ---: | --- | --- |
| QC1-06 (`A06`, `lane#0` L1 để mặc định `style=solid` thay vì `dashed`) | 1 | Đồng ý (do CVAT tự điền `solid` khi mới vẽ mà lúc đầu quên mở Details đổi sang `dashed`) | Đã sửa sang `style = dashed` ở bản `revision 2` |
| QC1-11 (`B02`, `traffic_sign#4@(1180,150)` quên tích `edge_zone=true`) | 1 | Đồng ý (cảnh báo `lint` cũng chỉ ra tâm box nằm ngoài 80% bán kính) | Đã tích `edge_zone = true` ở bản `revision 2` |

*(Trước khi xuất lại bản sửa trong giờ Buffer, toàn bộ hai file zip ban đầu và manifest `revision 1` đã được lưu vào thư mục [`r1/`](file:///c:/K5-L23-Day11-Road-Elements-SVM/r1/) theo đúng đặc tả bàn giao).*

## 5. Kiểm file export
| File | Mở lại được? | Số shape ảnh 1 (job / file) | Số shape ảnh 2 (job / file) | Tag camera có trong XML? |
| --- | --- | --- | --- | --- |
| `minhnk-front.zip` | Có (`lint` 0 lỗi nặng, 0 lỗi chính) | `A01`: 5 / 5 (`4 lane, 1 drivable`) | `A02`: 3 / 3 (`2 lane, 1 drivable`) | Có (`<tag label="camera"><attribute name="value">front</attribute></tag>`) |
| `minhnk-fisheye.zip` | Có (`lint` 0 lỗi nặng, 0 lỗi chính) | `B01`: 4 / 4 (`2 lane, 1 drivable, 1 crosswalk`) | `B02`: 5 / 5 (`2 lane, 1 drivable, 1 traffic_light, 1 traffic_sign`) | Có (`fisheye_front`, `fisheye_rear`, `fisheye_left`, `fisheye_right`) |

## 6. Ảnh chụp màn hình
1. **Tab Labels của project trên CVAT (6 label hình + 4 label tag):**  
   ![Tab Labels của project](docs/images/huong-dan-cvat/02-project-task.jpg)
2. **Ảnh camera trước đã gán đủ (4 tag, polyline làn L1/R1/L2, vùng lái `direct` và `alternative`):**  
   ![Ảnh camera trước đã gán](docs/images/huong-dan-cvat/11-drivable-alternative.jpg)
3. **Kiểm tra trạng thái Job `completed` và hộp thoại Export `CVAT for images 1.1` (tắt `Save images`):**  
   ![Job completed](docs/images/huong-dan-cvat/15-job-completed.jpg)  
   ![Export CVAT for images 1.1](docs/images/huong-dan-cvat/13-export-dialog.jpg)
4. **Đoạn XML thực tế trích từ `annotations.xml` trong `minhnk-front.zip` và `minhnk-fisheye.zip` (có `<polyline label="lane">` và đoạn polyline cong 9 điểm trên ảnh fisheye `B01`):**
   ```xml
   <!-- Trích từ minhnk-front.zip (A01) -->
   <polyline label="lane" source="manual" occluded="0" z_order="0" points="450.56,714.69;478.60,644.18;514.54,572.34;561.75,501.41;615.05,429.68">
     <attribute name="position">L1</attribute>
     <attribute name="style">dashed</attribute>
     <attribute name="color">white</attribute>
     <attribute name="kind">single</attribute>
   </polyline>

   <!-- Trích từ minhnk-fisheye.zip (B01 - polyline cong 9 điểm bám theo cung fisheye) -->
   <polyline label="lane" source="manual" occluded="0" z_order="0" points="515.10,826.54;520.90,794.00;527.14,761.03;534.33,729.00;543.73,696.42;553.03,665.29;564.33,634.09;579.89,603.78;593.02,573.92">
     <attribute name="position">L1</attribute>
     <attribute name="style">dashed</attribute>
     <attribute name="color">white</attribute>
     <attribute name="kind">single</attribute>
   </polyline>
   ```

## 7. Tự đánh giá
- **Lỗi bạn mắc nhiều nhất hôm nay (theo mã QC1-xx), và cách tránh ở Ngày 20:**  
  Lỗi dễ mắc nhất là **`QC1-06`** (quên đổi thuộc tính mặc định của `lane` do CVAT luôn tự điền `L1 / solid / white / single` ngay khi vừa nhấn `N` kết thúc polyline) và **`QC1-11`** (quên bật checkbox `edge_zone` cho đèn/biển nằm sát rìa ngoài 80% bán kính trên ảnh fisheye). Cách tránh ở Ngày 20: tạo thói quen vừa nhấn `N` xong là nhìn ngay sang bảng **Objects → Details** bên phải để chỉnh đủ 4 thuộc tính trước khi vẽ nét tiếp theo; và cuối mỗi task luôn chạy `python -m road_lab.road_check lint` để rà lại toàn bộ gợi ý `edge_zone` cũng như chiều vẽ polyline.
- **Nhãn của bạn đi vào model ở đâu? Chọn một lỗi đã sửa và nói nó làm model học sai thế nào:**  
  Nhãn đi trực tiếp vào làm **tín hiệu giám sát (ground-truth target $y$)** trong hàm mất mát (loss function) khi huấn luyện các mạng perception trên xe tự hành (như mô hình phát hiện làn đường theo row-anchor Ultra-Fast Lane Detection, mạng phân đoạn vùng lái, hay detector đèn/biển báo).  
  Ví dụ với lỗi đã sửa ở ảnh `A06` (`QC1-06`: vạch `L1` thực tế là vạch đứt `dashed` nhưng ở bản `r1` để nhầm thành vạch liền `solid`) hoặc ở `B02` (`QC1-11`: biển báo ở rìa mép ống kính fisheye bị biến dạng quang học mạnh nhưng quên bật `edge_zone = true`):
  - Nếu gán nhầm `dashed` thành `solid`, đầu phân loại thuộc tính làn (lane attribute classification head) sẽ bị phạt sai gradient trên đặc trưng vạch đứt, khiến model dự đoán vạch đứt thành vạch liền cấm chuyển làn, làm module Lập kế hoạch (Planner) của xe không dám thực hiện chuyển làn hợp lệ.
  - Nếu quên bật `edge_zone = true` ở vùng rìa fisheye (> 80% bán kính), khi huấn luyện hoặc đánh giá detector SVM quanh xe, các box bị méo nặng ở rìa không được lọc hoặc giảm trọng số (loss weighting), làm model học lệch bounding box regression trên vùng trung tâm.

## 8. (Tùy chọn, buffer) Từ polyline đến đích row-anchor
Chạy kiểm tra trên ảnh `A01`, vạch `R1` (`lane#1@(829,715)`, nối từ `y = 714.9` lên `y = 430.1`):
| y | 720 | 680 | 640 | 600 | 560 |
| --- | ---: | ---: | ---: | ---: | ---: |
| x của chúng tôi (`minhnk`) | không có làn (tại `y=714.9` là `829.3`) | 803.0 | 773.5 | 747.2 | 723.0 |
| x của bạn kia (`tranhoangan`) | không có làn (tại `y=715.1` là `830.8`) | 804.0 | 773.5 | 747.8 | 723.6 |

Trả lời 3 câu:

1. **Năm số x đó chính là đích mà một model row-anchor học cho vạch này. Nếu bạn dừng polyline sớm 40 px, hàng nào đổi thành "không có làn"?**  
   Nếu dừng polyline sớm 40 px ở đầu gần xe (ví dụ bắt đầu từ `y = 675` thay vì `y = 715`), hàng **`y = 680`** (và `y = 720`) sẽ nằm ngoài đoạn polyline và đổi thành **"không có làn"**. Tương tự, nếu xét trong 5 hàng neo `720, 680, 640, 600, 560` mà dừng sớm 40 px ở phía xa (ví dụ dừng ở `y = 595` thay vì kéo tới `y = 555`), thì hàng **`y = 560`** sẽ bị mất và đổi thành **"không có làn"**.
2. **So với bạn được kiểm (cùng ảnh): ở hàng 720 và hàng 560, hai bạn lệch nhau bao nhiêu pixel? Lệch ở hàng nào ảnh hưởng LKA nhiều hơn (slide 25)?**  
   Ở đầu sát đáy ảnh (`y ≈ 715–720` hoặc hàng neo gần xe nhất `y = 680`), hai bạn lệch nhau khoảng **`1.0 – 1.5 px`**, còn ở hàng **`y = 560`** hai bạn lệch nhau **`0.6 px`** (`723.0` so với `723.6`).  
   Về mặt điều khiển giữ làn (**LKA - Lane Keeping Assist**):
   - Lệch ở **hàng gần xe (`y = 720` / `y = 680`)** làm sai lệch trực tiếp **độ lệch ngang tức thời (lateral offset / cross-track error $e_y$)** ngay trước mũi xe, khiến bộ điều khiển đánh lái giật ngay lập tức.
   - Ngược lại, ở **hàng xa (`y = 560`)**, do phép chiếu phối cảnh camera (perspective projection), mỗi pixel ở gần chân trời tương ứng với một khoảng cách vật lý ngoài đời thực lớn hơn nhiều (hàng chục cm/px so với vài mm/px ở sát cản trước), nên lệch nhiều pixel ở hàng xa sẽ làm sai lệch mạnh **góc hướng xe (heading error $e_\psi$) và độ cong đường phía trước (curvature $\kappa$)**. Do đó, Guideline quy định điểm đầu sát xe là điểm quan trọng nhất cho vị trí làn tức thời và toàn bộ polyline không được lệch quá 5 px.
3. **Nếu polyline vẽ từ xa về gần (ngược quy tắc 2), cách tính trên có đổi không? Script nào sẽ gặp khó?**  
   - **Cách tính nội suy `x_at_y` trong `rowanchor.py`**: **Không đổi** kết quả số học, vì hàm `geometry.x_at_y` duyệt qua từng đoạn thẳng `(x1, y1)–(x2, y2)` và kiểm tra điều kiện `(y1 - y) * (y2 - y) <= 0` (không phụ thuộc thứ tự đoạn đi lên hay đi xuống), dù cuối hàm `rowanchor.py` vẫn phải in cảnh báo *"Chú ý: polyline đầu tiên vẽ từ xa về gần"*.
   - **Script và module gặp khó**:
     1. Script chấm **`road_check.py` (cả `lint` và `check`)**: Trong `lint`, điều kiện `s.points[0][1] < s.points[-1][1]` sẽ bắt lỗi chính **`QC1-04`**. Trong `check`, hàm `_lanes` tính tham số dọc theo polyline GT (`s0`, `s1`) và kiểm tra `s1 < s0` sẽ đánh lỗi chính **`QC1-04` ("vẽ ngược chiều (phải từ gần xe ra xa)")** và trừ điểm tiêu chí `dir`. Ngoài ra, khi ghép nhiều mảnh (`_merge`), script sắp xếp theo điểm đầu `p.points[0]` nên vẽ ngược chiều sẽ làm sai thứ tự ghép điểm.
     2. Các script tiền xử lý dữ liệu huấn luyện (như `bdd_to_cvat_road.py` hoặc data loader của model lane detection dự đoán chuỗi điểm tuần tự từ điểm gốc `start_point` ở đáy ảnh đi lên): nếu đảo ngược chiều, điểm đầu `points[0]` bị nhảy lên chân trời thay vì ở đáy ảnh, làm model học sai hoàn toàn vector hướng khởi phát của làn đường.
