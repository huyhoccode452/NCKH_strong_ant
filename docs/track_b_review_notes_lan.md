# Ghi chú review — kết quả Từ Liêm (gửi Lan)

> **Trạng thái (06/10/2026): ĐÃ HOÀN THÀNH.** Lan đã chạy lại B5 với raster chuẩn và
> thu thập thời tiết đủ 2019–2025 (nhánh `lan/docs/tu_liem_outputs`, commit `a03e77d`).
> Leader đã tái tạo độc lập dân số và B4 — khớp. Mục 2 ("tạm dừng, chờ nhóm chốt")
> **hết hiệu lực**: nhóm đã chốt raster `vnm_ppp_2020_UNadj_constrained.tif`. Bản vá
> `collect_weather.py` (cho phép `visibility` null) đã vào `main` ở commit `07d4e64`.
> Còn lại nhỏ: `bins_per_cell` của Chương Dương là 481 (không phải 488).
> Nội dung bên dưới giữ nguyên để làm lịch sử review.

> Ghi chú này review `docs/track_b_tu_liem_outputs.md` (nhánh
> `lan/docs/tu_liem_outputs`). Mục đích: liệt kê rõ cái gì cần sửa/bổ sung
> trước khi coi phường Từ Liêm là hoàn thiện, và trước khi bắt đầu phường tiếp
> theo — để không lặp lại cùng lỗi 126 lần.

## Trước tiên: phần đã làm đúng, không cần sửa

- Bám sát đúng khuôn mẫu `docs/track_b_sample_outputs.md` — 19 cột, đúng thứ
  tự, có dữ liệu thật (không bịa số) cho cả resolution 7 và 8.
- Giải thích đúng và đủ vụ res 7/8 chồng không gian, không cộng dồn — đúng
  tinh thần "không suy đoán, không tự gộp số liệu".
- Bảng so sánh Từ Liêm vs Chương Dương ở mục 6 làm tốt hơn cả bản gốc — nhờ đó
  mới phát hiện được vấn đề raster dân số bên dưới. Cứ giữ thói quen ghi
  log/so sánh kiểu này cho các phường sau.
- Trích lục đầy đủ lệnh đã chạy ở mục 7 — tái hiện được, đúng nguyên tắc "mọi
  biến đổi phải chạy lại được bằng script".

## 1. Thời tiết — phải làm lại, đây là việc bắt buộc trước khi Từ Liêm được coi là xong

**Vấn đề:** file thời tiết hiện tại chỉ có 4 tháng đầu 2019 (giống hệt bản demo
Chương Dương). Yêu cầu thật của đề tài là phủ **toàn bộ 2019-2025**, không
phải chỉ demo pipeline chạy được.

**Đây không phải lỗi của Lan** — tài liệu hướng dẫn cũ mô tả bước tải ERA5 4
tháng như một bước bình thường, không nói rõ đó chỉ là giới hạn của riêng bản
demo. Tài liệu đã được sửa lại, xem chi tiết ở
[`docs/chuong_duong_track_b_pilot_guide.md`](chuong_duong_track_b_pilot_guide.md) mục 7.

**Cách làm lại — đơn giản hơn cách cũ, không cần tải tay ERA5 nữa:**

Dùng `collect_weather.py` (gọi thẳng Open-Meteo API) thay vì
`ingest_era5_weather.py` (cần tải NetCDF thủ công từng đợt qua CDS). Script
này đã mặc định đúng khung 2019-2025, chỉ cần 1 lệnh:

```cmd
conda activate base
cd /d D:\NCKH
python collect_weather.py --cells data\processed\pilots\tu_liem\cells.parquet --output data\processed\pilots\tu_liem\weather.parquet
```

Không cần truyền `--start-date`/`--end-date` vì mặc định đã là
`2019-01-01`/`2025-12-31`. Script tự cache kết quả API (không tốn lại request
nếu chạy lại), tự kiểm tra không thiếu khung giờ, không có ô nào trống.

**Đính chính:** `visibility_min` sẽ **vẫn null** sau khi chạy lại — đây không
phải lỗi script. Dữ liệu tái phân tích ERA5 (Open-Meteo Historical API cũng
dựa trên nền này) **không có biến tầm nhìn thật ở quy mô lưới vi mô**, đã được
bạn làm Xuân Phương xác nhận độc lập. Không cần tìm cách "sửa" cho hết null —
cứ để null và ghi rõ lý do trong tài liệu, giống Xuân Phương đã làm.

**Việc cần làm:**
- [ ] Chạy `collect_weather.py` cho Từ Liêm, thay thế hoàn toàn
      `weather_2019_01_04.parquet` bằng file mới phủ đủ 2019-2025.
- [ ] Cập nhật lại mục 5 trong `docs/track_b_tu_liem_outputs.md`: đổi tên file,
      số dòng thật (~13 ô × 7 năm × 365 ngày × 4 khung ≈ 132.860 dòng, con số
      chính xác lấy từ output thật), và xác nhận `visibility_min` không còn null.
- [ ] Không cần chạy lại B2/B4/B5/B3 — chỉ thời tiết bị ảnh hưởng.

## 2. Raster dân số khác Chương Dương — KHÔNG tự quyết, chờ cả nhóm chốt

**Vấn đề:** Chương Dương dùng `vnm_ppp_2020_UNadj_constrained.tif`, Từ Liêm
dùng `vnm_ppp_2020_UNadj.tif` (không có `constrained`). Đây là 2 sản phẩm
WorldPop khác phương pháp tính (bản "constrained" giới hạn dân số vào khu vực
có công trình xây dựng theo ảnh vệ tinh, bản thường thì không) — dùng lẫn lộn
giữa các phường sẽ làm cột `population`/`population_density` không nhất quán
khi ghép toàn bộ 126 phường/xã vào `panel.parquet`.

**Lan đã làm đúng việc quan trọng nhất** — ghi rõ tên file khác nhau vào bảng
so sánh thay vì giấu đi. Nhờ vậy nhóm mới phát hiện được sớm.

**Việc cần làm — không phải việc của một mình Lan:**
- [ ] **Tạm dừng**, không tự đổi raster hay làm lại B5 ngay.
- [ ] Đưa ra nhóm quyết định dùng thống nhất **một** loại raster (constrained
      hay không) cho toàn bộ 126 phường/xã.
- [ ] Sau khi nhóm chốt, nếu Từ Liêm đang dùng sai loại thì mới chạy lại
      `build_population_poi_features.py` với raster đúng.

## Tóm tắt việc cần làm trước khi coi Từ Liêm là hoàn thiện

1. Chạy lại thời tiết bằng `collect_weather.py`, đủ 2019-2025, có `visibility`.
2. Chờ nhóm chốt raster dân số — không tự đổi một mình.
3. Sau khi xong mục 1, cập nhật lại số liệu trong
   `docs/track_b_tu_liem_outputs.md` cho khớp với dữ liệu mới (đặc biệt là
   mục 5 và mục 6, vì cả hai đều đang ghi số liệu của bản thời tiết 4 tháng cũ).

**Ảnh minh họa và data thô:** không cần commit vào Git — link Google Drive Lan
đã để ở đầu tài liệu là đủ, đã được duyệt.
