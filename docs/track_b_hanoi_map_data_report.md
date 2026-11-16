# Kết quả review dữ liệu bản đồ Track B — toàn Hà Nội (17/11/2026)

> **Tài liệu này ghi lại KẾT QUẢ THẬT** của gói dữ liệu bản đồ **toàn thành phố
> Hà Nội** đã được kiểm tra ngày **17/11/2026**, để leader và các thành viên
> khác đối chiếu mà không cần chạy lại từ đầu.

---

## 0. Link tải dữ liệu (Google Drive)

| Trường                | Giá trị                                                                                |
| --------------------- | -------------------------------------------------------------------------------------- |
| **Link Google Drive** | `https://drive.google.com/drive/folders/1f2KIDeD6gM88j1lhqtRg3_l1lC1mi_xs?usp=sharing` |
| **Tên tệp**           | `track_b_map_hanoi_2026-11-17.zip`                                                     |
| **Dung lượng**        | ~22,3 MB (sau khi nén, có thể nhỏ hơn)                                                 |
| **Nội dung**          | Toàn bộ thư mục `data/review/track_b_map_hanoi_2026-11-17/`                            |

**Quy ước:** mỗi lần cập nhật dữ liệu, tạo zip mới **cùng tên + ngày**, upload
thành phiên bản mới trên Drive, rồi sửa `Ngày upload` ở trên. Không ghi đè file
zip cũ nếu leader đã tải — để còn đối chiếu SHA256.

## 1. Phạm vi bàn giao

| Hạng mục          | Nội dung                                                       |
| ----------------- | -------------------------------------------------------------- |
| Khu vực           | **Toàn thành phố Hà Nội** (126 xã/phường theo địa giới 2025)   |
| Loại dữ liệu      | **Chỉ dữ liệu bản đồ**: ranh giới hành chính + OpenStreetMap   |
| **Không** bao gồm | Raster dân số WorldPop, dữ liệu thời tiết, kết quả B2–B5       |
| Ngày tạo          | 2026-11-17                                                     |
| Trạng thái        | **HỢP LỆ — 32 PASS, 0 FAIL, 2 WARN** (exit code 0)             |
| Nơi lưu           | `data/review/track_b_map_hanoi_2026-11-17/` (xem mục 0 để tải) |

Gói này thay thế bản bàn giao thử 1 phường trước đó. Bản 1 phường đã được xoá
để tránh nhầm lẫn; có thể tạo lại bằng một lệnh nếu cần (mục 9).

---

## 2. Kết quả kiểm tra

### 2.1. Kết quả từng nhóm

| Nhóm                   | Số kiểm tra | Kết quả | Nội dung chính                                           |
| ---------------------- | ----------- | ------- | -------------------------------------------------------- |
| 1. Tệp bắt buộc        | 6           | 6 PASS  | đủ 6 tệp, vượt ngưỡng dung lượng tối thiểu               |
| 2. Ranh giới thành phố | 5           | 5 PASS  | 1 hình học, MultiPolygon, vành khép kín, toạ độ hợp lệ   |
| 3. 126 xã/phường       | 10          | 10 PASS | đủ 126, mã/tên duy nhất, diện tích khớp 100,06%          |
| 4. PBF toàn Hà Nội     | 4           | 4 PASS  | `check-refs` = 0, bao trùm thành phố và 126/126 đơn vị   |
| 5. MANIFEST.csv        | 2           | 2 PASS  | 11/11 tệp khớp SHA256, không có tệp lạ                   |
| 6. Nhật ký chạy        | 5           | 5 PASS  | đủ 4 bước, 126 đơn vị, không tải dân số, không traceback |

### 2.2. Hai cảnh báo đã biết (không phải lỗi của gói)

```text
[WARN] có polygon tự cắt (self-intersection) trong dữ liệu nguồn
       3/126: Ba Vì, Đoài Phương, Yên Bài
[WARN] một số polygon 2025 nhô ra ngoài ranh giới GADM 4.1
       23/126 đơn vị (hai nguồn khác nhau, sai lệch nhỏ ở viền)
```

- 3 polygon tự cắt thuộc **dữ liệu nguồn 2025**; QGIS vẫn mở được, nhưng nên
  chạy `shapely.make_valid` trước khi tính diện tích/giao cắt. Phường Từ Liêm
  không bị.
- 23/126 đơn vị nhô ra ngoài GADM một chút vì ranh giới 2025 và GADM là hai
  nguồn khác nhau. Tổng diện tích vẫn khớp 100,06% (mục 3).

---

## 3. Số liệu thu được

### 3.1. Ranh giới và địa giới

| Chỉ số                               | Giá trị                      | Ghi chú                                 |
| ------------------------------------ | ---------------------------- | --------------------------------------- |
| Số xã/phường Hà Nội 2025             | **126**                      | `ward_id` và tên không trùng            |
| `ward_id` / `ward` có mặt            | 126/126                      | không có giá trị rỗng                   |
| Kiểu hình học                        | MultiPolygon = 126           | đồng nhất, mở được bằng QGIS            |
| Tổng diện tích 126 đơn vị            | **3 358,8 km²**              | đo geodesic trên WGS84                  |
| Diện tích thành phố (GADM)           | **3 356,9 km²**              | `hanoi_boundary.geojson`                |
| Tỷ lệ                                | **100,06%**                  | lệch 0,06% — đạt yêu cầu ±3%            |
| Diện tích đơn vị nhỏ nhất / lớn nhất | 1,64 / 80,60 km²             |                                         |
| Ranh giới thành phố                  | 1 MultiPolygon, **634 đỉnh** | bbox 105,288–106,020 E; 20,564–21,385 N |
| Toạ độ ngoài khung Hà Nội            | **0**                        |                                         |

Phép kiểm tra tổng diện tích là kiểm tra mạnh nhất cho tính đầy đủ: nếu thiếu,
thừa hoặc trùng đơn vị, tỷ lệ 100,06% sẽ lệch ngay.

### 3.2. OpenStreetMap

| Chỉ số                     | Giá trị                                   |
| -------------------------- | ----------------------------------------- |
| Tệp                        | `maps/hanoi.osm.pbf`                      |
| Dung lượng                 | **21 855 180 B** (20,8 MiB)               |
| Số node                    | **2 403 185**                             |
| Số way                     | **459 243**                               |
| Số relation                | **4 453**                                 |
| Khung bao                  | 105,0271–106,7531 E; 20,2769–21,6374 N    |
| `osmium check-refs`        | **Nodes in ways missing: 0**              |
| Mốc dữ liệu OSM            | **2026-09-19T19:27:58Z**                  |
| Công cụ cắt                | osmium/1.19.1, `--strategy=complete_ways` |
| Bao trùm 126/126 xã/phường | **có**                                    |

Khung PBF rộng hơn ranh giới hành chính vì `complete_ways` giữ nguyên way có ít
nhất một node trong Hà Nội. Đây là hành vi đúng; B4 sẽ lọc theo từng ô H3.

### 3.3. So với dữ liệu đang dùng trong repo

| Tệp                        | SHA256 (gói = repo)                                                |
| -------------------------- | ------------------------------------------------------------------ |
| `hanoi.osm.pbf`            | `f851811c2be7476b61adb689975d432d34274b6768fce8022a6b3576f46347c7` |
| `hanoi_wards_2025.geojson` | `a6eefd3eacb30a3af8d00ab403298499fb03cee4a3dd198c09164210a083bed3` |

Hai tệp trùng **byte tuyệt đối** với bản đang dùng trong repo, nên các kết quả
pilot đã có (Từ Liêm, Chương Dương) vẫn tái lập được với gói này.

---

## 4. Cấu trúc gói dữ liệu

```text
track_b_map_hanoi_2026-11-17/
├── README_hanoi_map_data.md    # phạm vi + kết quả kiểm tra (bản ngắn)
├── QA_REPORT.md                # báo cáo QA chi tiết, rủi ro, khuyến nghị
├── commands.txt                # lệnh đã chạy, phiên bản công cụ, SHA256 nguồn
├── MANIFEST.csv                # SHA256 + dung lượng của 11 tệp (niêm phong)
├── verify.py                   # script tự kiểm tra
├── run.log                     # nhật ký đầy đủ lần chạy tạo gói (UTF-8)
└── maps/
    ├── gadm41_VNM_1.json.zip     # 231 497 B — GADM 4.1 cấp 1, gói tải gốc
    ├── gadm41_VNM_1.json         # 821 030 B — GADM 4.1 cấp 1, đã giải nén
    ├── hanoi_boundary.geojson    # 11 968 B — ranh giới thành phố Hà Nội
    ├── 01.json                   # 183 743 B — dữ liệu gốc 126 xã/phường 2025
    ├── hanoi_wards_2025.geojson  # 209 713 B — 126 xã/phường, GeoJSON chuẩn
    └── hanoi.osm.pbf             # 21 855 180 B — OSM cắt theo toàn Hà Nội
```

Tổng dung lượng: **22,3 MB**. Toàn bộ 11 tệp được niêm phong trong
`MANIFEST.csv`; `verify.py` băm lại và báo FAIL nếu có tệp bị sửa/xoá.

---

## 5. Mô tả từng tệp dữ liệu

### `maps/hanoi_boundary.geojson`

- 1 feature, `MultiPolygon`, WGS84 (EPSG:4326), **634 đỉnh**.
- Đây là **bare geometry** (không bọc `FeatureCollection`) để dùng trực tiếp cho
  `osmium extract -p`.
- Nguồn: GADM 4.1, `GID_1 = "VNM.27_1"`.

### `maps/hanoi_wards_2025.geojson`

- `FeatureCollection` gồm **126 feature**, mỗi feature là một `MultiPolygon`.
- Mỗi feature có **9 thuộc tính**:

```text
ward_id, ward, unit_type, area_km2_source, population_source,
density_source, center_lon_source, center_lat_source,
admin_geography_version
```

- Ví dụ feature `ward_id = "00592"`:

| Thuộc tính                                | Giá trị                                     |
| ----------------------------------------- | ------------------------------------------- |
| `ward`                                    | Từ Liêm                                     |
| `unit_type`                               | Phường                                      |
| `area_km2_source`                         | 10,18                                       |
| `population_source`                       | 119 997                                     |
| `density_source`                          | 11 787,52                                   |
| `center_lon_source` / `center_lat_source` | 105,76974 / 21,02074                        |
| `admin_geography_version`                 | `Hanoi wards/communes effective 2025-07-01` |

> `population_source` chỉ là **metadata của lớp ranh giới** (số liệu nguồn
> 2025), **không phải** raster dân số. Gói này không kèm dữ liệu dân số.

### `maps/hanoi.osm.pbf`

- PBF nhị phân, đọc bằng `pyosmium`/`osmium`, **không mở bằng Excel/QGIS**.
- Đã cắt từ snapshot `vietnam-260919.osm.pbf` bằng `complete_ways`, sau đó xác
  nhận `Nodes in ways missing: 0`.
- Dùng cho B2 (không cần), B3 (render ảnh), B4 (đặc trưng đường) và B5 (POI).

### Hai tệp nguồn

- `01.json`: JSON gốc 126 xã/phường 2025 (cấu trúc riêng, không phải GeoJSON).
- `gadm41_VNM_1.json(.zip)`: GADM 4.1 cấp 1, dùng để tách Hà Nội.

Hai tệp này để **tái lập**, không cần dùng trực tiếp trong pipeline.

---

## 6. Nguồn và giấy phép

| Dữ liệu            | URL                                                                                        | Dung lượng | Giấy phép                                              |
| ------------------ | ------------------------------------------------------------------------------------------ | ---------- | ------------------------------------------------------ |
| OSM Việt Nam       | `https://download.geofabrik.de/asia/vietnam-latest.osm.pbf`                                | ~317 MiB   | **ODbL** — ghi "© OpenStreetMap contributors"          |
| GADM 4.1 cấp 1     | `https://geodata.ucdavis.edu/gadm/gadm4.1/json/gadm41_VNM_1.json.zip`                      | 226 KB     | miễn phí cho học thuật/phi thương mại                  |
| 126 xã/phường 2025 | `https://raw.githubusercontent.com/lamngockhuong/vietnam-3d-map/main/public/wards/01.json` | 179 KB     | dữ liệu GitHub công khai, **chưa phải bản chính thức** |

Snapshot OSM dùng để cắt: `maps/vietnam-260919.osm.pbf`, **328 988 384 B**,
SHA256:

```text
3e5bcadc39531a1688030027f4d6cb2d64681c2d5ce32b681db7e772dfd42f14
```

## 7. Hạn chế đã biết

| #   | Vấn đề                                                                      | Mức                       | Ảnh hưởng                                             | Cách xử lý                                               |
| --- | --------------------------------------------------------------------------- | ------------------------- | ----------------------------------------------------- | -------------------------------------------------------- |
| 1   | Ranh giới 126 đơn vị là dữ liệu GitHub công khai, **chưa đối chiếu iHanoi** | **Cao**                   | Nếu sai ranh giới, mọi ô H3 và số liệu phường đều sai | **Bắt buộc** đối chiếu mắt với iHanoi trước kết quả cuối |
| 2   | 3 polygon tự cắt: Ba Vì, Đoài Phương, Yên Bài                               | Thấp                      | QGIS vẫn mở; tính diện tích/giao cắt có thể sai       | `shapely.make_valid` trước khi tính; Từ Liêm không bị    |
| 3   | 23/126 đơn vị nhô ra ngoài GADM                                             | Thấp                      | Sai lệch nhỏ ở viền; tổng diện tích vẫn khớp          | Chấp nhận; GADM chỉ dùng để cắt PBF                      |
| 4   | OSM là snapshot 2026-09-19                                                  | Trung bình theo thời gian | Đường/POI cũ dần                                      | Ghi mốc dữ liệu trong báo cáo; tải mới khi cần           |
| 5   | `hanoi.osm.pbf` rộng hơn ranh giới hành chính                               | Thấp                      | Có way kéo ra ngoài Hà Nội                            | Đúng chủ ý (`complete_ways`); B4 lọc theo ô H3           |
| 6   | Không có raster dân số / thời tiết                                          | —                         | B5/B6 chưa chạy được từ gói này                       | Bổ sung khi cần (mục 9)                                  |

Chi tiết đầy đủ và khuyến nghị: `QA_REPORT.md` mục 8–9 (trong gói tải về).

---

## 8. Tự kiểm tra sau khi tải từ Drive

```powershell
Expand-Archive track_b_map_hanoi_2026-11-17.zip -DestinationPath .
cd track_b_map_hanoi_2026-11-17
python verify.py
```

Kết quả mong đợi — đúng bằng kết quả đã ghi ở mục 2:

```text
KẾT QUẢ: 32 PASS, 0 FAIL, 2 WARN
KẾT LUẬN: HỢP LỆ - mọi kiểm tra bắt buộc đều PASS.
```

Yêu cầu:

- Python 3.11 + `osmium-tool` (bắt buộc cho mục 4).
- `shapely`, `pyproj` (tuỳ chọn; thiếu thì bỏ qua 1 kiểm tra diện tích, số PASS
  giảm 1 nhưng kết luận không đổi).
- Nếu `osmium` không có trên PATH:
  `python verify.py --osmium "C:\...\osmium.exe"`.

Kiểm tra độc lập, không qua `verify.py`:

```cmd
osmium check-refs maps\hanoi.osm.pbf
osmium fileinfo -e maps\hanoi.osm.pbf
```

---

## 9. Việc tiếp theo

| Bước                 | Dùng gói này được không | Cần bổ sung                                 |
| -------------------- | ----------------------- | ------------------------------------------- |
| B2 — lưới H3         | ✅ được ngay            | `hanoi_wards_2025.geojson`                  |
| B3 — render ảnh      | ✅ được ngay            | `hanoi.osm.pbf`                             |
| B4 — đặc trưng đường | ✅ được ngay            | `hanoi.osm.pbf`                             |
| B5 — dân số/POI      | ⚠️ một phần             | Raster WorldPop (không có trong gói)        |
| B6 — thời tiết       | ❌ chưa                 | `collect_weather.py` (Open-Meteo 2019–2025) |

Lệnh bổ sung dữ liệu còn thiếu (từ thư mục repo):

```cmd
rem Raster dân số cho B5
python b0_fetch_map_data.py --maps-dir maps --with-population

rem PBF riêng 1 phường nếu cần chạy thử nhanh
python b0_fetch_map_data.py --maps-dir maps --reuse-local-pbf --ward "Tu Liem"
```

Trước khi dùng cho kết quả cuối, cần **đối chiếu ranh giới 126 đơn vị với
iHanoi** (rủi ro #1 ở mục 7).
