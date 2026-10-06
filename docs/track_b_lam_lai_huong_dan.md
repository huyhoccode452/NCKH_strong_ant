# Hướng dẫn làm lại Track B — Từ Liêm (Lan) và Xuân Phương (Đức)

> **Trạng thái (06/10/2026):** cả Lan và Đức đã làm xong phần việc trong tài liệu
> này (xem trạng thái ở đầu `track_b_review_notes_lan.md` và `track_b_review_notes_duc.md`).
> Tài liệu giữ lại làm hướng dẫn tham khảo cho các phường sau. Lưu ý: `collect_weather.py`
> trên `main` đã có bản vá cho phép `visibility` null, nên không cần tự sửa script như Lan
> đã phải làm.

> Tài liệu này gộp lại thành **việc cần làm cụ thể** cho từng người, sau khi
> đã review kết quả 2 phường. Chi tiết lý do từng lỗi xem ở
> [`docs/track_b_review_notes_lan.md`](track_b_review_notes_lan.md) và
> [`docs/track_b_review_notes_duc.md`](track_b_review_notes_duc.md) — tài liệu
> này chỉ tập trung vào **làm gì, theo lệnh nào**, không nhắc lại lý do.

## Bước 0 — Chung cho cả 2 người, làm trước tiên

Leader sẽ gửi trực tiếp file raster dân số chuẩn
**`vnm_ppp_2020_UNadj_constrained.tif`** (không cần tự tìm trên trang WorldPop
nữa — tự tìm là nguyên nhân gây ra 3 phường 3 file khác nhau).

1. Nhận file từ leader.
2. Đặt đúng vào: `D:\NCKH\data\raw\population\vnm_ppp_2020_UNadj_constrained.tif`
   (chú ý tên thư mục là `population`, không phải `worldpop`).
3. Nếu trước đó có tải file raster khác (`vnm_ppp_2020_UNadj.tif`,
   `vnm_ppp_2020_100m.tif`...) thì xóa đi, chỉ giữ đúng 1 file chuẩn để tránh
   nhầm lẫn sau này.

---

## Phần A — Lan (phường Từ Liêm)

### A1. Chạy lại B5 với raster chuẩn

```cmd
conda activate base
cd /d D:\NCKH
python build_population_poi_features.py --cells data\processed\pilots\tu_liem\cells_b4.parquet --cell-geometry data\processed\pilots\tu_liem\cells_geometry.geojson --population-raster data\raw\population\vnm_ppp_2020_UNadj_constrained.tif --pbf maps\tu_liem.osm.pbf --output data\processed\pilots\tu_liem\cells_b5.parquet
```

Kiểm tra: script phải chạy xong không báo lỗi CRS. So sánh lại số `population`
mới với số cũ trong `docs/track_b_tu_liem_outputs.md` — số sẽ đổi vì khác
raster, đây là bình thường.

### A2. Chạy lại B3 để cập nhật `cells_b3.parquet` (dùng `cells_b5.parquet` mới)

```cmd
python render_cell_images.py --pbf maps\tu_liem.osm.pbf --cells data\processed\pilots\tu_liem\cells_b5.parquet --cell-geometry data\processed\pilots\tu_liem\cells_geometry.geojson --image-dir data\processed\pilots\tu_liem\cell_images --output data\processed\pilots\tu_liem\cells_b3.parquet --contact-sheet data\processed\pilots\tu_liem\cell_images_contact_sheet.png
```

(Ảnh không đổi vì render ảnh không phụ thuộc raster dân số — bước này chỉ để
ghi lại đúng `cells_b3.parquet` có cột `population` mới.)

### A3. Chạy lại thời tiết đủ 2019-2025

```cmd
python collect_weather.py --cells data\processed\pilots\tu_liem\cells.parquet --output data\processed\pilots\tu_liem\weather.parquet
```

Không cần truyền `--start-date`/`--end-date` (mặc định đã đúng 2019-2025).
Xóa file cũ `weather_2019_01_04.parquet` sau khi có file mới. **`visibility_min`
vẫn sẽ null — đó là đúng, không phải lỗi** (xem đính chính ở
`track_b_review_notes_lan.md`).

### A4. Cập nhật lại tài liệu

Sửa `docs/track_b_tu_liem_outputs.md`:
- Mục 2: số `population`/`population_density` mới (do đổi raster).
- Mục 5: đổi tên file thành `weather.parquet`, số dòng thật (13 ô × ~10.228
  khung/ô — lấy số chính xác từ output), khoảng thời gian 2019-01-01 →
  2025-12-31.
- Mục 6 (bảng so sánh): cập nhật lại toàn bộ số liệu liên quan đến dân số và
  thời tiết cho khớp dữ liệu mới.

---

## Phần B — Đức (phường Xuân Phương)

> **Cập nhật 06/10/2026:** B1 và B2 bên dưới **không còn cần làm** — raster của Đức
> đã được xác minh trùng file chuẩn (SHA256) và số liệu tái tạo khớp 100%. Chỉ còn
> B3–B6 (đường dẫn, gửi lại `collect_weather.py`, múi giờ, cập nhật tài liệu); Đức đã
> làm phần lớn trong commit `457b342`.

### B1. Chạy lại B5 với raster chuẩn

```powershell
python build_population_poi_features.py --cells data/processed/pilots/xuan_phuong/cells_b4.parquet --cell-geometry data/processed/pilots/xuan_phuong/cells_geometry.geojson --population-raster data/raw/population/vnm_ppp_2020_UNadj_constrained.tif --pbf maps/xuan_phuong.osm.pbf --output data/processed/pilots/xuan_phuong/cells_b5.parquet --qa-output data/processed/pilots/xuan_phuong/cells_b5_qa.json
```

### B2. Chạy lại B3 để cập nhật `cells_b3.parquet`

```powershell
python render_cell_images.py --pbf maps/xuan_phuong.osm.pbf --cells data/processed/pilots/xuan_phuong/cells_b5.parquet --cell-geometry data/processed/pilots/xuan_phuong/cells_geometry.geojson --image-dir data/processed/pilots/xuan_phuong/cell_images --output data/processed/pilots/xuan_phuong/cells_b3.parquet --contact-sheet data/processed/pilots/xuan_phuong/cell_images_contact_sheet.png
```

### B3. Sửa đường dẫn 2 file dùng chung

Không cần tải lại gì, chỉ cần **di chuyển file đã có** sang đúng vị trí:

```powershell
mkdir maps -Force
move data\raw\osm\hanoi.osm.pbf maps\hanoi.osm.pbf
```

Từ giờ mọi lệnh dùng `hanoi.osm.pbf` phải trỏ vào `maps\hanoi.osm.pbf`, không
phải `data\raw\osm\hanoi.osm.pbf` nữa.

### B4. Gửi lại phần code đã sửa cho leader

Phần sửa `collect_weather.py` (bỏ qua lỗi khi `visibility` null) là đúng và
có ích cho cả nhóm — nhưng cần gửi lại cho leader để đưa vào repo chính
(`huyhoccode452/NCKH_strong_ant`), không giữ riêng trên máy mình. Có thể gửi
qua Google Drive hoặc leader sẽ hướng dẫn tạo pull request.

### B5. Sửa lỗi nhỏ trong tài liệu

Trong `docs/track_b_comparison_and_guide.md`, tìm "Asia/Bangkok" và sửa thành
"Asia/Ho_Chi_Minh".

### B6. Cập nhật lại tài liệu

Cập nhật `docs/track_b_xuan_phuong_sample_outputs.md` mục 2, 5, 6 với số liệu
`population`/`population_density` mới sau khi đổi raster ở B1.

---

## Sau khi cả 2 người xong

1. Leader gộp bản sửa `collect_weather.py` của Đức vào repo chính, để lần sau
   không ai dính lỗi crash vì `visibility` null nữa.
2. Cả Từ Liêm và Xuân Phương lúc này đều dùng chung 1 raster dân số — có thể
   so sánh `population_density` giữa 2 phường một cách công bằng.
3. Leader cân nhắc cập nhật `docs/context.md`: ghi rõ raster dân số chuẩn là
   `vnm_ppp_2020_UNadj_constrained.tif`, và `visibility_min` sẽ luôn null cho
   toàn bộ 126 phường/xã (không phải thiếu sót cần khắc phục, mà là giới hạn
   dữ liệu đã biết trước — nên phát biểu rõ trong phần giới hạn của báo cáo).
