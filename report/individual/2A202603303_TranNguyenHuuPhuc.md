# Báo cáo Day 13 — Trần Nguyễn Hữu Phúc, 2A202603303

## 1. Kết luận trước
Mã số 2A202603303, task `D13-2A202603303`, 10 frame: s01f31, s01f58, s02f02, s02f41, s03f32, s03f54, s04f39, s04f54, s05f18, s05f37 (2 frame mỗi sequence). Hệ điều hành Windows 11; tài khoản CVAT `hhuuphucc`, CVAT 2.82/2.83 (bản của lớp).

Tổng cộng 80 cuboid (4 + 12 + 5 + 8 + 6 + 6 + 10 + 12 + 9 + 8 theo thứ tự frame). Frame ít nhất là s01f31 với 4 cuboid, vì quanh xe thưa xe, vật xa nhất chỉ 44,3 m. `selfcheck` cờ 5 trên 80 cuboid: 3 cuboid đáy thấp hơn mặt đường 0,29–0,35 m (s01f58 idx 11, s04f39 idx 3 và idx 4) và 2 cuboid có điểm sát ngay ngoài mép (s02f41 idx 2, s04f54 idx 4). Tôi tin nhất phần hướng (yaw): xe cùng chiều đều trong khoảng 266,3°–276,8°, xe ngược chiều 86,2°–93,3°. Yếu nhất là vật xa và bị che (s01f58 idx 7–12, 20–30 m, chỉ 28–102 điểm) và độ cao đáy ở các xe xa trên 25 m.

Cách dùng CVAT:
- Đổi góc nhìn: kéo chuột để xoay, giữ chuột giữa hoặc dùng phím mũi tên để dời, nút U/I/O và J/K/L để xoay và phóng to; chuyển sang nhìn từ trên xuống để đọc bố cục các xe.
- Vẽ cuboid bằng Shape, nháy đúp vào cuboid để chỉnh, rồi kiểm sát điểm ở ba khung Top, Side, Front; nhập L, W, H trong DETAILS của thẻ cuboid.
- Bật Cuboid orientation ở Appearance để thấy mũi tên hướng (vạch đỏ ngắn là +X của cuboid); chạy `python scripts/lab.py preview` để xem cuboid trên ảnh camera.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f31-3d`, `t1-s01f31-top`, `t1-s01f58-3d`, `t1-s01f58-top`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f31 | Cạnh trái (hướng −y); +x chỉ xuống, +y chỉ sang phải | Phẳng; thấp hơn LiDAR ~1,7 m (z đáy 1,57–1,73 m) | ~44,3 m (idx 4, 60 điểm) | 4 Car | Phía sau: idx 2 (8,9 m), idx 3 (16,7 m), idx 4 (44,3 m); phía trước: idx 1 (10,2 m) | Không có |
| s01f58 | Cạnh trái (hướng −y); +x chỉ xuống, +y chỉ sang phải | Phẳng; thấp hơn LiDAR ~1,8 m (z đáy 1,70–1,93 m) | ~29,5 m (idx 12, 28 điểm) | 12 Car | Bên cạnh: idx 1 (x = +2,9 m, cách 3,0 m); phía sau: idx 2 (6,9 m), idx 7 (29,3 m) | Cụm xe đông phía trước 20–30 m (idx 7–12, bị che, 28–102 điểm) |

3 điều **chỉ camera** cho biết: (1) loại xe, màu sắc và đầu xe hay đuôi xe, nên phân biệt được xe đang đi cùng chiều hay ngược chiều; (2) vạch kẻ đường, làn xe và đèn tín hiệu, biển báo trên cao; (3) xe bị che một phần trong dòng xe đông ở s01f58, thấy rõ từng xe riêng.
3 điều **chỉ LiDAR** cho biết: (1) khoảng cách và vị trí chính xác bằng mét, ví dụ xe xa nhất cách 44,3 m; (2) chiều cao đáy so với mặt đường (z đáy khoảng −1,7 m) và hình khối 3D của xe; (3) các xe phía sau lưng xe thu (idx 2, 3 ở s01f31; idx 2, 7 ở s01f58) mà camera trước không thấy.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f02 | Cạnh trái (−y), giống hai frame trên | Có 1 xe buýt rất gần, cách 3,3 m, tới 15869 điểm trong cuboid |
| s02f41 | Cạnh trái (−y) | idx 2 yaw 93,3° (xe ngược chiều), idx 3 yaw 357,3° (xe đi ngang); có 2 người đi bộ và 1 xe đạp; 59 điểm sát ngay ngoài mép cuboid idx 2 |
| s03f32 | Cạnh trái (−y) | Có xe buýt 35,8 m có z = −0,000 m, và xe xa nhất của toàn bộ bài ở 65,3 m (21 điểm, bị che) |
| s03f54 | Cạnh trái (−y) | Xe idx 4 ở 36,3 m có đáy thấp hơn mặt đường 0,20 m, bị che; có 1 xe hai bánh (Cyclist) và 1 xe buýt 37,1 m |
| s04f39 | Cạnh trái (−y) | idx 3, 4 đáy thấp hơn mặt đường 0,35 m và 0,29 m; idx 5, 6 yaw 87,3° (ngược chiều); có 1 xe tải bị che ở 37,9 m |
| s04f54 | Cạnh trái (−y) | Người đi bộ idx 4 có 41 điểm sát ngay ngoài mép; idx 10 ở 52,6 m có z đáy −2,532 m, ngoài phạm vi 50 m |
| s05f18 | Cạnh trái (−y) | Hai xe tải và một xe buýt cỡ lớn; xe tải idx 7 dài 9,783 m |
| s05f37 | Cạnh trái (−y) | Xe buýt idx 7 ở 60,4 m nên ngoài phạm vi lab; nhiều cuboid không có `ground_gap_m` (không đo được mặt đường) |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f31, idx 2 | Car | Có, yaw 270,9° (cùng chiều, mũi tên chỉ về phía trước) | Có, z đáy −1,568 m, lệch mặt đường 0,20 m (< 0,3 m) | Có, khớp 3 view và ảnh camera | l 4,900 m, h 1,465 m trong khoảng sedan; w 1,979 m vượt 1,90 m nhưng nằm trong khoảng SUV (1,80–2,10 m) |
| (b) xe buýt hoặc xe tải | s02f02, idx 1 | Bus | Có, yaw 270,9° | Có, z đáy −1,719 m, lệch 0,12 m | Có, khớp 3 view và ảnh camera | l 11,808 m và h 3,043 m trong khoảng; w 2,824 m vượt 2,60 m (gương và thân xe) |
| (c) người đi bộ hoặc xe hai bánh | s02f41, idx 5 | Pedestrian | Có, yaw 256,4° (đi cùng chiều xe) | Gần ngưỡng: z đáy −1,442 m, lệch 0,30 m | Có, khớp 3 view và ảnh camera | w 0,664 m và h 1,628 m trong khoảng; l 0,969 m vượt 0,9 m |

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s01f58, idx 8 (22,6 m) / `t4-1-bong-che` / task của tôi | Xe phía trước che xe phía sau trong dòng xe đông | Chỉ 102 điểm ở 22,6 m, hình xe không đủ 6 mặt, cuboid nét đứt | Bật occluded = 1; cuboid vẫn bao trọn phần suy luận được theo kích thước chuẩn (4,5 × 1,944 × 1,583 m) |
| 2. Thưa ở xa | s01f58, idx 12 (29,5 m) / `t4-2-thua-o-xa` / task của tôi | Tia LiDAR thưa dần theo khoảng cách, thêm bị che | Chỉ 28 điểm, chỉ vài vệt điểm mờ | Vẫn vẽ vì còn đủ cơ sở (≥ 15–20% thể tích, ảnh camera rõ); nếu ít hơn thì bỏ qua và ghi báo cáo |
| 3. Phản xạ thấp / ít điểm bất thường | s04f39, idx 3 (26,0 m) / `t4-3-phan-xa-thap` / task của tôi | Mặt đường ở đó thấp hơn hoặc điểm mặt đường thưa nên đáy đo lệch | Đáy cuboid thấp hơn mặt đường 0,35 m (z đáy −2,163 m), chỉ 168 điểm | Đáy phải chạm mặt đường; nếu không đủ điểm mặt đường thì chỉnh đáy về z ≈ −1,8 m và ghi lại cờ |
| 4. Điểm lạ | s02f35 / `t4-4-diem-la` / task chung | Có thể là nhiễu, vật nhỏ, hoặc điểm phản xạ đơn lẻ giữa vùng trống | Một vệt điểm nhỏ đơn lẻ giữa vùng trống, dải chấm thẳng hàng phía xa; frame không có cuboid nào (Items: 0) | Không vẽ cuboid cho điểm lạ; chỉ gán khi đối chiếu ảnh camera nhận ra được một vật thật |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603303-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f31, 2, Car | −0,835 m | 1,465 m | −1,568 m | 8,92 m | 8,96 m | 270,9° | Chạm đường (lệch 0,13 m so với −1,7 m) |
| s02f02, 1, Bus | −0,197 m | 3,043 m | −1,719 m | 3,28 m | 3,29 m | 270,9° | Chạm đường |
| s04f39, 3, Car | −1,388 m | 1,550 m | −2,163 m | 26,03 m | 26,07 m | 270,7° | Chìm 0,35 m (cờ `selfcheck`) |
| s04f54, 10, Car | −1,676 m | 1,712 m | −2,532 m | 52,58 m | 52,61 m | 271,7° | Chìm 0,83 m, xa 52,6 m và bị che |

(a) Cuboid **gần nhất** là s01f58 idx 1: `dist_m` 2,97 m, r = 3,12 m. Cuboid **xa nhất** là s03f32 idx 2: `dist_m` 65,35 m, r = 65,35 m (21 điểm, bị che).

(b) Người đi bộ s02f41 idx 5: l = 0,969 m, w = 0,664 m, h = 1,628 m so với bảng 4.3 (người lớn: 0,4–0,9 / 0,5–0,9 / 1,50–1,95 m). Chiều dài 0,969 m vượt 0,9 m vì cuboid phải bao cả tay chân đang vung, nhưng chỉ vượt 7,7%, nằm trong sai số ±10% của Pedestrian. Xe con s01f31 idx 3: l = 3,843 m, w = 1,900 m, h = 1,586 m, cả ba chiều nằm trong khoảng sedan (3,8–5,0 / 1,70–1,90 / 1,40–1,60 m). Xe s01f31 idx 2 có w = 1,979 m vượt khoảng sedan nhưng nằm trong khoảng SUV (1,80–2,10 m).

(c) Xe cùng chiều phía trước, ví dụ s01f58 idx 3 có yaw 269,1° (gần 270°). Xe ngược chiều, ví dụ s04f39 idx 5 có yaw 87,3° và s05f18 idx 6 có yaw 91,8° (gần 90°). Cả hai nhóm đều gần 270° và 90°.

Mật độ theo khoảng cách (4 cuboid Car ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s01f58, 1, Car | 3,0 m | 3155 |
| s01f31, 1, Car | 10,2 m | 990 |
| s05f37, 2, Car | 25,8 m | 232 |
| s01f31, 4, Car | 44,3 m | 60 |

Quy luật: xa gấp khoảng 4 lần (10,2 m → 44,3 m) thì số điểm còn khoảng 1/16 (990 → 60), gần với tỉ lệ nghịch bình phương khoảng cách. So với đường cong ở slide (1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m, 19 ở 60–80 m): số của tôi cùng bậc, 232 điểm ở 25,8 m so với 214 điểm ở 20–30 m, và 60 điểm ở 44,3 m (xa hơn bin 30–40 m nên ít hơn 72 điểm).

Phần tự kiểm: `selfcheck` còn cờ 5 cuboid trong bản nộp và tôi giữ nguyên các cảnh báo sau:
- s01f58 idx 11 (23,9 m) và s04f39 idx 3, idx 4 (26,0 m và 26,9 m): đáy thấp hơn mặt đường 0,35 m, 0,35 m và 0,29 m. Tôi giữ nguyên vì cả ba ở xa (23,9–26,9 m), và giả thuyết là mặt đường ở đó thấp hơn mức −1,7 m chứ xe không thật sự chìm.
- s02f41 idx 2 (24,0 m, 59 điểm sát mép) và s04f54 idx 4 (người đi bộ, 18,4 m, 41 điểm sát mép): tôi giữ nguyên vì giả thuyết là điểm sát mép thuộc vật bên cạnh (xe khác, hàng rào), không phải của vật đang gán.

Đối chiếu với bảng A: `dist_m` của s01f31 idx 1 là 10,22 m trong bảng A và 10,2 m trong phiếu tự kiểm (khớp); `dist_m` của s03f32 idx 2 là 65,35 m và 65,3 m (khớp, sai lệch do làm tròn).

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Model học trên nhãn 3D phải biết vị trí, kích thước, hướng của vật bằng mét thật để dùng cho lái xe tự động; nhãn pixel không cho biết khoảng cách thật và đổi theo camera. Vật thưa điểm cần quy tắc riêng (vẽ khi thấy ≥ 15–20% thể tích, còn lại bỏ qua và ghi báo cáo) để model không học từ cuboid đoán mò.

(b) Dùng AI: Claude Code. Câu hỏi chính: tính z đáy

(c) Điều còn chưa chắc: độ cao đáy của các xe xa trên 25 m (s04f39 idx 3, 4), vì không rõ mặt đường ở đó thực sự thấp hơn hay cuboid bị đặt sai; và các xe bị che trong cụm xe đông ở s01f58 (idx 7–12).
