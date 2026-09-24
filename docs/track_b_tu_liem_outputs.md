# Kết quả Track B — Phường Từ Liêm (dữ liệu thật)

> **Tài liệu này ghi lại kết quả THẬT** sau khi chạy xong toàn bộ pipeline Track B
> trên phường Từ Liêm. Dùng để đối chiếu khi nhóm mở rộng sang các xã/phường
> tiếp theo. 
>
> Kết quả mẫu của xã Chương Dương (dùng để hiểu "hình dạng đúng") nằm ở
> [`docs/track_b_sample_outputs.md`](track_b_sample_outputs.md).
> 

---
*Link data Google Drive "https://drive.google.com/drive/folders/1TagT9TpM1_Qpy45nA1VCAst6Z6YkrNlA?usp=drive_link"*

## 1. Cấu trúc file kết quả

```text
data/processed/pilots/tu_liem/
├── cells.parquet                    # Lưới H3 thô (sau B2) — 13 dòng
├── cells_geometry.geojson           # Hình lục giác để xem trong QGIS
├── cells_b4.parquet                 # + đặc trưng đường (B4) — 13 dòng
├── cells_b4_qa.json                 # QA bước B4
├── cells_b5.parquet                 # + dân số / POI (B5) — 13 dòng
├── cells_b5_qa.json                 # QA bước B5
├── cells_b3.parquet                 # ⭐ BẢNG CUỐI CÙNG — đủ B2+B4+B5+B3 — 13 dòng, 19 cột
├── cell_images/                     # 13 ảnh PNG, tên = h3_index
│   ├── 874143690ffffff.png
│   ├── 874143694ffffff.png
│   └── ... (11 ảnh khác)
├── cell_images_contact_sheet.png    # Ghép 12 ảnh đầu để xem nhanh
├── weather_2019_01_04.parquet       # Thời tiết mẫu (4 tháng đầu 2019)
└── weather_2019_01_04_qa.json       # QA file thời tiết
```

**File quan trọng nhất để kiểm tra: `cells_b3.parquet`** — bảng gộp đủ mọi
thứ trừ thời tiết (thời tiết là file riêng vì hiện tại mới có dữ liệu 4 tháng
mẫu, chưa đủ 2019–2025).

---

## 2. `cells_b3.parquet` — 19 cột, 13 dòng

### Toàn bộ tên cột (thứ tự đúng)

```text
h3_index, resolution, centroid_lat, centroid_lon, district, ward,
n_intersections, road_length_primary, road_length_secondary,
road_length_residential, n_traffic_signals, n_crossings, has_major_road,
population, population_density, n_schools, n_hospitals, n_commercial_poi,
image_path
```

**Nếu bảng của bạn thiếu cột nào trong danh sách trên, hoặc thừa cột lạ không
có ở đây — dừng lại, kiểm tra lại từng bước B2/B4/B5/B3**, đừng tiếp tục làm
ảnh hay ghép dữ liệu.

### 2 dòng thật, resolution 7 (ô to, ~5.16 km²)

| h3_index | resolution | centroid_lat | centroid_lon | district | ward | n_intersections | road_length_primary | road_length_secondary | road_length_residential | n_traffic_signals | n_crossings | has_major_road | population | population_density | n_schools | n_hospitals | n_commercial_poi |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| `874143690ffffff` | 7 | 21.026797 | 105.756964 | *(trống)* | Từ Liêm | 132 | 0.0 | 7476.34 | 16218.58 | 12 | 214 | True | 36955.42 | 6536.38 | 9 | 1 | 26 |
| `874143694ffffff` | 7 | 21.009572 | 105.773839 | *(trống)* | Từ Liêm | 237 | 5044.46 | 9582.69 | 20039.08 | 13 | 243 | True | 50380.41 | 8910.58 | 8 | 2 | 29 |

### 2 dòng thật, resolution 8 (ô nhỏ, ~0.74 km²)

| h3_index | resolution | centroid_lat | centroid_lon | district | ward | n_intersections | road_length_primary | road_length_secondary | road_length_residential | n_traffic_signals | n_crossings | has_major_road | population | population_density | n_schools | n_hospitals | n_commercial_poi |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| `8841436901fffff` | 8 | 21.026797 | 105.756964 | *(trống)* | Từ Liêm | 6 | 0.0 | 92.85 | 1145.83 | 0 | 10 | True | 5590.32 | 6921.40 | 0 | 0 | 1 |
| `8841436905fffff` | 8 | 21.033648 | 105.762441 | *(trống)* | Từ Liêm | 70 | 0.0 | 2983.19 | 9743.05 | 4 | 97 | True | 9166.11 | 11349.91 | 6 | 0 | 18 |

### Ý nghĩa từng cột

| Cột | Ý nghĩa | Lưu ý |
|---|---|---|
| `h3_index` | Mã định danh duy nhất của 1 ô lục giác | Không bao giờ trùng trong cùng 1 phường |
| `resolution` | 7 (ô to, ~5.16 km²) hoặc 8 (ô nhỏ, ~0.74 km²) | 2 resolution **chồng lên nhau về không gian** — xem lưu ý mục 3 |
| `centroid_lat` / `centroid_lon` | Tọa độ tâm ô | WGS84, vĩ độ trước kinh độ |
| `district` | Luôn **trống** trong bộ dữ liệu mới | Địa giới 2025 không còn cấp quận/huyện — không tự suy đoán điền vào |
| `ward` | Tên xã/phường | Lấy đúng từ `hanoi_wards_2025.geojson`, không gõ tay |
| `n_intersections` | Số điểm giao giữa các đường trong ô | Từ Liêm: ô res 7 có 132–237 giao lộ/ô (dày hơn hẳn Chương Dương do đô thị hoá cao) |
| `road_length_*` | Tổng chiều dài đường theo cấp, đơn vị **mét** | `road_length_primary = 0.0` ở ô res 7 đầu tiên là bình thường — ô đó không có đường cấp 1 chạy qua |
| `n_traffic_signals`, `n_crossings` | Đếm theo tag OSM | Giá trị **0** nghĩa là OSM chưa gắn tag, **không phải** thực địa không có |
| `has_major_road` | Có đường lớn (motorway / trunk / primary / secondary) đi qua ô | Kiểu boolean `True` / `False` |
| `population` | Tổng dân số trong ô (từ raster WorldPop 2020) | Đơn vị: người |
| `population_density` | Dân số / diện tích ô (km²) | |
| `n_schools`, `n_hospitals`, `n_commercial_poi` | Đếm điểm POI từ OSM | |
| `image_path` | Đường dẫn đến ảnh PNG 224×224 của ô | Trỏ vào thư mục `cell_images/` |

---

## 3. Lưu ý quan trọng: resolution 7 và 8 chồng không gian — không cộng dồn

Hai ô `874143690ffffff` (res 7) và `8841436901fffff` (res 8) có **cùng tọa độ
tâm** (`21.026797, 105.756964`). Đây **không phải lỗi trùng lặp** — ô res 8
là ô nhỏ nằm *bên trong* ô res 7 lớn hơn, giống như bản đồ 2 lớp lưới chồng
lên nhau ở 2 mức phóng to khác nhau.

**Hệ quả bắt buộc phải nhớ:** không được cộng `population` hay
`n_intersections` của cả 13 dòng rồi nói đó là tổng của phường Từ Liêm — con
số đó sẽ đếm trùng phần chồng lấn. Muốn biết tổng thật của phường, chỉ cộng
các ô của **một resolution duy nhất** (ví dụ chỉ 2 ô res 7).

`cells_b5_qa.json` đã ghi rõ:

```json
"note_on_total": "H3 resolutions 7 and 8 overlap in this pilot, so this sum is not a physical ward total."
```

---

## 4. Ảnh bản đồ — đối chiếu trực quan

![Ảnh ghép 12 ô đầu của phường Từ Liêm — cell_images_contact_sheet.png](../data/processed/pilots/tu_liem/cell_images_contact_sheet.png)

Đây là `cell_images_contact_sheet.png` thật, ghép 12 ảnh đầu tiên trong số 13
ô của phường Từ Liêm. Ảnh đúng chuẩn phải có:

- ✅ Nền màu xám nhạt đồng nhất.
- ✅ Đường tô màu theo cấp: đỏ/cam đậm = đường lớn, vàng/trắng mảnh = đường nhỏ.
- ✅ Sông/hồ tô màu xanh nhạt, khối nhà tô xám.
- ✅ **Tuyệt đối không có chữ nào trên ảnh** — không tên đường, không watermark, không chữ lỗi kiểu `"API KEY REQUIRED"`.
- ✅ Các ô cùng resolution trông có độ phóng to gần giống nhau.

Ảnh vẽ trực tiếp từ `maps/tu_liem.osm.pbf` đã cắt sẵn — **không gọi bất kỳ
dịch vụ bản đồ nào trên mạng**.

---

## 5. `weather_2019_01_04.parquet` — thời tiết ERA5

### Schema (8 cột)

| Cột | Kiểu | Ý nghĩa |
|---|---|---|
| `h3_index` | string | Mã ô H3 |
| `time_bin` | string (ISO 8601 +07:00) | Khung 6 giờ trong giờ Việt Nam |
| `temp_mean` | float64 | Nhiệt độ trung bình (°C) |
| `precipitation_sum` | float64 | Tổng lượng mưa (mm) |
| `visibility_min` | float64 | Tầm nhìn tối thiểu — **toàn bộ null** (xem ghi chú) |
| `wind_speed_max` | float64 | Tốc độ gió mạnh nhất (m/s) |
| `humidity_mean` | float64 | Độ ẩm trung bình (%) |
| `cloud_cover_mean` | float64 | Độ che phủ mây trung bình (%) |

### Số lượng dòng và khoảng thời gian

- **Số dòng:** 6 253 dòng
- **Khoảng thời gian:** `2019-01-01T06:00:00+07:00` → `2019-05-01T06:00:00+07:00`
- **Cách tính:** 13 ô H3 × 4 tháng × ~30.5 ngày × 4 khung 6h/ngày ≈ 6 253 dòng
- **Nguồn:** ERA5 hourly data on single levels, khu vực bounding box phường Từ Liêm
- **Múi giờ:** `Asia/Ho_Chi_Minh` (`+07:00`) — nếu ra `+00:00` nghĩa là chưa chuyển đổi

### 3 dòng thật (cùng 1 ô, 3 khung giờ liên tiếp ngày 01/01/2019)

| h3_index | time_bin | temp_mean | precipitation_sum | visibility_min | wind_speed_max | humidity_mean | cloud_cover_mean |
|---|---|---|---|---|---|---|---|
| `874143690ffffff` | 2019-01-01T06:00:00+07:00 | 11.37 | 0.0038 | *(null)* | 3.21 | 70.87 | 99.98 |
| `874143690ffffff` | 2019-01-01T12:00:00+07:00 | 12.21 | 0.0 | *(null)* | 3.18 | 68.61 | 99.50 |
| `874143690ffffff` | 2019-01-01T18:00:00+07:00 | 12.16 | 0.0315 | *(null)* | 2.63 | 70.46 | 99.53 |

> **Ghi chú `visibility_min`:** cột này null toàn bộ — không phải lỗi xử lý.
> Dữ liệu ERA5 tải theo hướng dẫn hiện tại không bao gồm biến tầm nhìn. Đây là
> giới hạn đã biết, ghi rõ trong `weather_2019_01_04_qa.json`. Cần tìm nguồn
> dữ liệu tầm nhìn riêng trước khi huấn luyện mô hình chính thức.

---

## 6. Bảng số liệu tổng hợp — kiểm tra và đối chiếu

| Chỉ số | Giá trị thực (Từ Liêm) | Giá trị mẫu (Chương Dương) | Ghi chú |
|---|---|---|---|
| Số ô resolution 7 | **2** | 6 | Từ Liêm nhỏ hơn về số ô res 7 do diện tích phường khác nhau |
| Số ô resolution 8 | **11** | 35 | |
| Tổng ô trong `cells_b3.parquet` | **13** | 41 | |
| Tổng `n_intersections` (cả 13 ô) | **825** | 209 | Chỉ tham khảo — không phải tổng chính thức do chồng lấn res 7/8 |
| Tổng `n_traffic_signals` (cả 13 ô) | **57** | 0 | Từ Liêm có đèn tín hiệu OSM; Chương Dương chưa được gắn tag |
| Tổng `n_crossings` (cả 13 ô) | **986** | 0 | Tương tự trên |
| Ô có `has_major_road = True` | **13/13** | 26/41 | Toàn bộ ô Từ Liêm đều tiếp giáp đường lớn |
| Tổng `population` (cả 13 ô, không tính res chồng) | **173 136** | — | Theo `cells_b5_qa.json`; **không dùng làm dân số thật của phường** do chồng res |
| `population_density` nhỏ nhất / lớn nhất | **6 536 / ~11 350** người/km² | 1 070 / ~2 145 | Từ Liêm đô thị hoá cao hơn nhiều so với Chương Dương |
| `population` nhỏ nhất / lớn nhất trong 1 ô | **5 590 / 50 380** người | 0.0 / 17 190 | Không có ô nào ra 0 dân — phường Từ Liêm không có ô ven sông như Chương Dương |
| Số file ảnh PNG trong `cell_images/` | **13** | 41 | Mỗi ô 1 ảnh 224×224 px |
| `check-refs` khi cắt OSM | `Nodes in ways missing: 0` | `Nodes in ways missing: 0` | **Bắt buộc phải ra đúng số 0** |
| Số dòng `weather_2019_01_04.parquet` | **6 253** | 19 721 | 13 ô × 4 tháng × 4 khung/ngày ≈ 6 253 |
| Khoảng thời gian thời tiết | 2019-01-01 → 2019-05-01 | 2019-01-01 → 2019-05-01 | Cùng bản mẫu 4 tháng |
| Trạng thái `visibility_min` | **null toàn bộ** | null toàn bộ | Đã biết — chưa có nguồn dữ liệu này |
| Số OSM way hợp lệ được xử lý | 2 484 | — | Từ `cells_b4_qa.json` |
| Raster dân số | `vnm_ppp_2020_UNadj.tif` | `vnm_ppp_2020_UNadj_constrained.tif` | **Khác tên file** — ghi rõ trong `cells_b5_qa.json` để theo dõi |
| CRS raster dân số | `EPSG:4326` | `EPSG:4326` | Phải khớp, script tự báo lỗi nếu sai |

> **Đừng cố làm ra đúng những con số của Từ Liêm ở phường khác** — mỗi phường
> có diện tích, mật độ đường, dân số khác nhau nên số liệu chắc chắn khác.
> Bảng này chỉ để thấy "con số cỡ này là hợp lý", không phải đáp án cần khớp.

---

## 7. Trích lục lệnh đã chạy (để tái hiện hoặc kiểm tra)

Mọi lệnh dưới đây chạy từ thư mục gốc dự án (`D:\NCKH`), trong môi trường
Anaconda đã kích hoạt (`conda activate base` trừ bước cắt Osmium).

### Cắt OSM cho phường Từ Liêm

```cmd
rem Bước 4 — tách polygon phường Từ Liêm
rem (chọn trong QGIS, xuất ra maps\tu_liem_boundary.geojson)

rem Bước 5 — cắt OSM
conda activate osmium
cd /d D:\NCKH
osmium extract --strategy=complete_ways -p maps\tu_liem_boundary.geojson maps\hanoi.osm.pbf -o maps\tu_liem.osm.pbf
osmium check-refs maps\tu_liem.osm.pbf
rem ✅ Kết quả: Nodes in ways missing: 0
```

### B2 — Sinh lưới H3

```cmd
conda activate base
cd /d D:\NCKH
python build_h3_grid.py ^
    --boundary maps\tu_liem_boundary.geojson ^
    --ward "Từ Liêm" ^
    --output data\processed\pilots\tu_liem\cells.parquet ^
    --geometry-output data\processed\pilots\tu_liem\cells_geometry.geojson
rem ✅ Output: 13 ô (2 res 7 + 11 res 8)
```

### B4 — Đặc trưng đường

```cmd
python build_road_features.py ^
    --pbf maps\tu_liem.osm.pbf ^
    --cells data\processed\pilots\tu_liem\cells.parquet ^
    --cell-geometry data\processed\pilots\tu_liem\cells_geometry.geojson ^
    --output data\processed\pilots\tu_liem\cells_b4.parquet
rem ✅ Output: cells_b4.parquet + cells_b4_qa.json
```

### B5 — Dân số và POI

```cmd
python build_population_poi_features.py ^
    --cells data\processed\pilots\tu_liem\cells_b4.parquet ^
    --cell-geometry data\processed\pilots\tu_liem\cells_geometry.geojson ^
    --population-raster data\raw\population\vnm_ppp_2020_UNadj.tif ^
    --pbf maps\tu_liem.osm.pbf ^
    --output data\processed\pilots\tu_liem\cells_b5.parquet
rem ✅ Output: cells_b5.parquet + cells_b5_qa.json
rem ✅ CRS raster: EPSG:4326 — khớp, không có lỗi
```

### B3 — Ảnh bản đồ và bảng cuối

```cmd
python render_cell_images.py ^
    --pbf maps\tu_liem.osm.pbf ^
    --cells data\processed\pilots\tu_liem\cells_b5.parquet ^
    --cell-geometry data\processed\pilots\tu_liem\cells_geometry.geojson ^
    --image-dir data\processed\pilots\tu_liem\cell_images ^
    --output data\processed\pilots\tu_liem\cells_b3.parquet ^
    --contact-sheet data\processed\pilots\tu_liem\cell_images_contact_sheet.png
rem ✅ Output: 13 ảnh PNG + cells_b3.parquet + cell_images_contact_sheet.png
```

### Thời tiết ERA5 (mẫu 4 tháng đầu 2019)

```cmd
python ingest_era5_weather.py ^
    --cells data\processed\pilots\tu_liem\cells.parquet ^
    --source-dir data\raw\weather\era5\tu_liem\2019_01_04 ^
    --output data\processed\pilots\tu_liem\weather_2019_01_04.parquet
rem ✅ Output: 6 253 dòng, khoảng 2019-01-01 → 2019-05-01 (+07:00)
rem ⚠️  visibility_min: null toàn bộ — đã biết, chưa cần khắc phục
```

---

*Tài liệu này tạo tự động từ dữ liệu thật ngày 24/09/2026. Nếu chạy lại
pipeline với dữ liệu OSM mới hơn, một số số liệu đường/POI có thể thay đổi
nhỏ do cộng đồng OSM cập nhật.*
