# Phiếu QC 1 — Road v1.0

Lô được kiểm (học viên): `tranhoangan`        Người kiểm (học viên): `nguyenkhoaminh` (Nguyễn Khoa Minh)  
Ngày: 2026-11-20                          Phiên bản guideline: Road v1.0  
File được kiểm (tên + `revision` trong manifest): `tranhoangan-front.zip`, `tranhoangan-fisheye.zip` (`revision: 1` trong `tranhoangan-manifest.json`)  
Số ảnh kiểm: 6 (+ 0 ảnh thêm, ghi riêng) — gồm `A01, A03, A04, A08, B02, B06` (đối chiếu thêm toàn bộ 20 ảnh bắt buộc: 169 nhãn)   Số nhãn đã kiểm (shape + tag): 51 (27 shape + 24 tag trên 6 ảnh mẫu; tổng toàn lô 20 ảnh là 89 shape + 80 tag = 169 nhãn)  

## Tổng hợp theo mã
| Mã | Loại lỗi | Số mục kiểm | Số lỗi | Mức |
| --- | --- | ---: | ---: | --- |
| QC1-01 tag camera | sai thuộc tính / bỏ sót | 24 | 0 | nặng |
| QC1-02 đủ L1, R1 | bỏ sót | 10 | 0 | nặng |
| QC1-03 position | sai thuộc tính / thừa | 14 | 0 | nặng |
| QC1-04 tâm vạch, chiều, điểm dừng | sai phạm vi / bỏ sót | 14 | 1 | chính |
| QC1-05 vạch đứt, che | sai phạm vi / sai thuộc tính | 14 | 1 | chính |
| QC1-06 style, color, kind | sai thuộc tính | 14 | 0 | chính |
| QC1-07 vùng lái | sai phạm vi / bỏ sót / thừa | 7 | 0 | chính |
| QC1-08 đèn, màu | bỏ sót / sai phạm vi / sai thuộc tính | 5 | 0 | nặng / chính |
| QC1-09 facing, type, group, biển phụ | sai thuộc tính | 6 | 0 | chính |
| QC1-10 hình in, đủ biển | bỏ sót / sai phạm vi | 1 | 0 | chính |
| QC1-11 fisheye | sai phạm vi / sai thuộc tính | 7 | 1 | chính |
| QC1-12 file | lỗi file | 51 | 0 | nhẹ / nặng |

*(Ghi chú: Ảnh `A01` được kiểm kỹ toàn bộ 5 shape (`lane#0` L1, `lane#1` R1, `lane#2` L2, `lane#3` R2, `drivable#4` direct) và 4 tag: xác nhận **không lỗi**).*

## Danh sách lỗi (mỗi lỗi một dòng)
| # | Mã | Ảnh | Nhãn (label, id) | Mức | Mô tả ngắn |
| --- | --- | --- | --- | --- | --- |
| 1 | QC1-05 | A03 | `lane#0@(450,715)` | chính | Vạch L1 nối qua đoạn bị xe che nhưng quên bật cờ `occluded=true` trong bảng Objects. |
| 2 | QC1-04 | A04 | `lane#2@(120,715)` | chính | Đoạn cuối vạch vàng đôi L2 gần điểm dừng phía xa hơi lệch khỏi tâm vệt sơn khoảng 6.5 px (> ngưỡng 5 px). |
| 3 | QC1-11 | B02 | `traffic_sign#4@(1180,150)` | chính | Tâm box biển báo ở rìa phải `(1195, 165)` nằm ngoài 80% bán kính ảnh fisheye nhưng chưa tích chọn `edge_zone=true`. |

## Ca mơ hồ
| Ảnh | Câu hỏi cho lead | Lời đáp (nếu có) |
| --- | --- | --- |
| A08 | Cụm đèn nhỏ ở xa `traffic_light#6@(950,260)` chỉ cao 6 px (< 8 px), bạn có gán box sát vỏ và lint báo cảnh báo nhẹ, có cần xoá đi không? | Lead/Coach đã xác nhận: dưới 8 px là tùy chọn (không bắt buộc), trót gán đúng vị trí và màu thì không tính lỗi. |
| A04 | Dải sát lề bên phải có tính vào polygon `drivable` (`direct`) không? | Lead/Coach xác nhận: theo Guideline mục 3, dải đỗ xe sát lề không gộp vào `direct`, bạn gán dừng ở vạch R1 là đúng. |

## Quyết định
Lỗi nặng: 0   Lỗi chính: 3 / 51 nhãn = 5.88 % (nếu tính trên toàn lô 20 ảnh rà thêm là 3 / 169 = 1.78 %, nhưng tính theo mẫu 6 ảnh là 5.88 % > 5 %)   Lỗi nhẹ: 0   Ca chờ lead: 0 (đã có lời đáp)  
Quyết định (Release / Hold / Rework): **Rework**  
Lý do (theo luật của phiếu): Không có lỗi nặng và không còn ca mơ hồ chờ lead, nhưng tỉ lệ lỗi chính trên tập mẫu 6 ảnh đã kiểm là `3 / 51 = 5.88 % > 5 %` nên theo luật quyết định của phiếu cần trả về **Rework** để bạn bật `occluded` ở `A03`, kéo lại đỉnh cuối `lane#2` ở `A04` và tích `edge_zone` ở `B02` trước khi xuất bản `revision 2`.  

Loại lỗi: bỏ sót = thiếu nhãn phải có; thừa = gán cái không phải đối tượng; sai phạm vi = hình lệch, sai chiều, dừng sai chỗ, tràn vùng. Ba loại này theo cách chia lỗi gán nhãn của ISO/IEC 5259-4 (mục 8.4.2); "sai thuộc tính" và "lỗi file" là khóa học thêm. Cách xếp mức: [thang mức lỗi](../docs/qc_severity_road_v1.0.md).
