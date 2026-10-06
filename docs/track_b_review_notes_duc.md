# Ghi chú review — kết quả Xuân Phương (gửi Đức)

> **Trạng thái (06/10/2026): ĐÃ XỬ LÝ.** Đức đã sửa `.gitignore`, khôi phục
> `ingest_era5_weather.py`, gỡ file PBF khỏi Git, đổi đường dẫn và múi giờ (commit
> `457b342`). Raster đã xác minh đúng bằng SHA256 (mục 1 đã được đính chính). Bản vá
> `collect_weather.py` đã vào `main` ở commit `07d4e64`. Còn lại: dọn
> `track_b_execution_report.md` (bản nháp lỗi thời) và squash-merge nhánh của Đức.
> Nội dung bên dưới giữ nguyên để làm lịch sử review.

> Ghi chú này review kết quả Track B phường Xuân Phương (repo
> `nmd-coder/NCKH_CrashFormer`, 4 tài liệu: `track_b_xuan_phuong_sample_outputs.md`,
> `track_b_comparison_and_guide.md`, `track_b_execution_report.md`,
> `track_b_final_report.md`). Nhóm đã chốt lấy **chuẩn xã Chương Dương** làm
> chuẩn chung — ghi chú này liệt kê chỗ Xuân Phương chưa khớp chuẩn đó.

## Trước tiên: phần đã làm đúng, không cần sửa

- Thời tiết làm **đúng và đầy đủ hơn** cả Chương Dương: phủ trọn 2019-2025
  (153.420 dòng), không dừng ở mẫu 4 tháng. Đây là **hình mẫu tốt**, không
  phải lỗi — không cần làm lại phần này.
- Tự phát hiện đúng: dữ liệu tái phân tích ERA5 (kể cả qua Open-Meteo) **không
  có biến `visibility` thật** — đây là giới hạn dữ liệu gốc, không phải lỗi xử
  lý. Phát hiện này đúng và có giá trị cho cả nhóm.
- Đã tự nhận ra và sửa đúng vấn đề "web tile cần API key/kết nối mạng" (từng
  dùng CartoDB tile, sau tự chuyển sang vẽ vector trực tiếp từ PBF local) —
  đúng hướng mà toàn bộ pipeline chuẩn đang dùng.
- Có log lỗi/khắc phục chi tiết (Conda solver, H3 v4 API, Unicode console) —
  tư liệu hữu ích, nên giữ lại.

## 1. Raster dân số — ĐÃ XÁC MINH ĐÚNG (bản đầu của ghi chú này nhận định sai)

Bản đầu ghi rằng Xuân Phương dùng raster khác chuẩn (`vnm_ppp_2020_100m.tif`) và phải
đổi lại. **Nhận định đó sai.** Đức đã công bố SHA256 của file đang dùng; leader đối
chiếu thì **khớp bit-for-bit** với `vnm_ppp_2020_UNadj_constrained.tif` chuẩn — tên file
cũ chỉ là tên đặt khác. Tái tạo độc lập cũng ra đúng tổng `108031.635`.

Điểm duy nhất cần đổi là **tên file/thư mục** (`data/raw/population/…`), và Đức đã đổi.
Không cần chạy lại B5.

## 2. Tự sửa `collect_weather.py` nhưng không đồng bộ lại cho nhóm

**Vấn đề:** bản `collect_weather.py` gốc sẽ báo lỗi (crash) khi gặp
`visibility = null` từ Open-Meteo. Đức đã sửa lại để bỏ qua lỗi này — **sửa
đúng**, nhưng chỉ tồn tại trên bản sao riêng, chưa đưa về lại repo chung
(`huyhoccode452/NCKH_strong_ant`). Người khác (kể cả Lan) chạy bản gốc trên
GitHub sẽ dính đúng lỗi crash này mà không biết đã có người tìm ra cách sửa.

**Việc cần làm:**
- [ ] Gửi lại phần code đã sửa trong `collect_weather.py` cho leader (hoặc
      tạo pull request vào repo chính) để cả nhóm dùng chung một bản đã sửa.
- [ ] Không tự giữ bản sửa riêng cho mình.

## 3. Đường dẫn file dùng chung không khớp quy ước Chương Dương

**Vấn đề:** file riêng của phường thì đặt đúng chuẩn (`maps/xuan_phuong_boundary.geojson`,
`maps/xuan_phuong.osm.pbf`), nhưng 2 file dùng chung cho toàn Hà Nội lại đặt
sai thư mục:

| File | Chuẩn Chương Dương | Đức đang dùng |
|---|---|---|
| OSM Hà Nội gốc | `maps/hanoi.osm.pbf` | `data/raw/osm/hanoi.osm.pbf` |
| Raster dân số | `data/raw/population/*.tif` | `data/raw/worldpop/*.tif` |

**Việc cần làm:**
- [ ] Di chuyển `hanoi.osm.pbf` vào đúng `maps/hanoi.osm.pbf`.
- [ ] Đổi tên thư mục `data/raw/worldpop/` thành `data/raw/population/` (khớp
      luôn với việc nhận file raster chuẩn ở mục 1).
- [ ] Sửa lại toàn bộ lệnh trong 4 tài liệu output cho khớp đường dẫn mới.

## 4. Lỗi nhỏ trong tài liệu (không ảnh hưởng số liệu)

`docs/track_b_comparison_and_guide.md` ghi múi giờ "Asia/Bangkok" ở phần mô tả
B6 — đúng ra phải là "Asia/Ho_Chi_Minh" theo quy ước chung (`context.md` mục
8). Cùng lệch UTC+7 nên số liệu không sai, chỉ cần sửa lại tên cho đúng.

- [ ] Sửa "Asia/Bangkok" thành "Asia/Ho_Chi_Minh" trong tài liệu.

## Tóm tắt việc cần làm trước khi coi Xuân Phương là hoàn thiện

1. ~~Đổi raster, chạy lại B5~~ — không cần, đã xác minh đúng (SHA256).
2. Gửi lại bản sửa `collect_weather.py` cho leader để đồng bộ vào repo chung.
3. Sửa đường dẫn 2 file dùng chung (`maps/hanoi.osm.pbf`,
   `data/raw/population/`) cho khớp quy ước.
4. Sửa lỗi chính tả múi giờ trong tài liệu.

**Không cần làm lại:** phần thời tiết 2019-2025 và cách render ảnh vector —
cả hai đều đã đúng chuẩn hoặc tốt hơn chuẩn.
