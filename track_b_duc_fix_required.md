# Đức cần sửa lại — review nhánh `duc/track_b/xuanphuong` (commit `1123a29`)

> Đây là vòng review thứ 2, sau khi Đức đã push commit
> `1123a29 "Update track_b Xuan Phuong data and reports"`. Tài liệu này chỉ rõ
> **3 lỗi cụ thể phát hiện bằng cách so sánh trực tiếp nội dung file trước/sau
> commit** — không phải suy đoán — kèm hướng dẫn sửa từng bước.

## Lỗi 1 (nghiêm trọng nhất): Có dấu hiệu CHƯA thực sự chạy lại script với raster mới

**Bằng chứng cụ thể** — so sánh `docs/track_b_xuan_phuong_sample_outputs.md`
trước và sau commit `1123a29`:

| | Trước | Sau |
|---|---|---|
| Đường dẫn raster ghi trong file | `data/raw/worldpop/vnm_ppp_2020_100m.tif` | `data/raw/population/vnm_ppp_2020_UNadj_constrained.tif` |
| `total_population_across_cells` | `108031.635` | `108031.635` (**giống hệt**) |

**Vì sao đây là bằng chứng của lỗi, không phải trùng hợp:** `vnm_ppp_2020_100m.tif`
và `vnm_ppp_2020_UNadj_constrained.tif` là **2 sản phẩm WorldPop khác phương
pháp tính** (một bản không giới hạn, một bản giới hạn dân số vào khu vực có
công trình xây dựng theo ảnh vệ tinh). Đổi sang raster khác thì tổng dân số
**bắt buộc phải thay đổi** — dù ít hay nhiều, không thể giống nhau đến từng
chữ số thập phân. Số liệu giống hệt nhau chỉ có thể xảy ra nếu:
- Chưa thực sự chạy lại `build_population_poi_features.py`, mà chỉ sửa lại
  chữ trong tài liệu/file QA JSON cho khớp yêu cầu, HOẶC
- Chưa nhận được file raster thật, chỉ đoán trước đường dẫn sẽ dùng.

**Cách sửa — làm đúng và kiểm tra lại được:**

1. Xác nhận đã có file thật `vnm_ppp_2020_UNadj_constrained.tif` trong máy,
   đặt đúng tại `data/raw/population/vnm_ppp_2020_UNadj_constrained.tif`.
2. Chạy lại thật:
   ```powershell
   python build_population_poi_features.py --cells data/processed/pilots/xuan_phuong/cells_b4.parquet --cell-geometry data/processed/pilots/xuan_phuong/cells_geometry.geojson --population-raster data/raw/population/vnm_ppp_2020_UNadj_constrained.tif --pbf maps/xuan_phuong.osm.pbf --output data/processed/pilots/xuan_phuong/cells_b5.parquet --qa-output data/processed/pilots/xuan_phuong/cells_b5_qa.json
   ```
3. Mở lại `cells_b5_qa.json` vừa sinh ra, đọc số `total_population_across_cells`
   — **số này phải khác `108031.635`** (khác bao nhiêu tùy dữ liệu, nhưng phải
   khác). Nếu vẫn ra đúng số cũ, dừng lại và báo lại ngay, đừng tự sửa tài liệu
   để khớp — có thể raster chưa đúng file hoặc script đang đọc nhầm cache.
4. Chỉ sau khi có số liệu MỚI thật từ bước 3, mới cập nhật lại
   `docs/track_b_xuan_phuong_sample_outputs.md` (mục 2, 5, 6) bằng con số vừa
   chạy ra — không copy số liệu từ bản cũ.
5. Chạy lại B3 để cập nhật `cells_b3.parquet` với dữ liệu B5 mới:
   ```powershell
   python render_cell_images.py --pbf maps/xuan_phuong.osm.pbf --cells data/processed/pilots/xuan_phuong/cells_b5.parquet --cell-geometry data/processed/pilots/xuan_phuong/cells_geometry.geojson --image-dir data/processed/pilots/xuan_phuong/cell_images --output data/processed/pilots/xuan_phuong/cells_b3.parquet --contact-sheet data/processed/pilots/xuan_phuong/cell_images_contact_sheet.png
   ```

## Lỗi 2: Đã xóa dòng `.gitignore` chặn file bản đồ lớn, khiến PBF bị commit thẳng vào Git

**Bằng chứng:** commit `1123a29` xóa hẳn đoạn sau khỏi `.gitignore`:

```gitignore
maps/*.pbf
maps/01.json
maps/gadm41_VNM_1.json
```

Hậu quả: `maps/hanoi.osm.pbf` (**21.8 MB**), `maps/chuong_duong.osm.pbf`,
`maps/xuan_phuong.osm.pbf` đã bị commit thẳng vào lịch sử Git. Đây là dữ liệu
lớn, tải lại được từ Geofabrik/cắt lại được bằng `osmium` — không được phép
nằm trong Git (nguyên tắc `docs/context.md` mục 8: "không commit dữ liệu lớn
lên git"). Nếu nhánh này được merge nguyên trạng, mọi người sau này clone repo
sẽ phải tải thêm hơn 20MB không cần thiết, và sẽ tăng dần theo mỗi lần có
người khác lặp lại lỗi tương tự.

**Cách sửa:**

1. Khôi phục lại đúng 3 dòng đã xóa vào `.gitignore` (copy nguyên văn từ trên).
2. Gỡ các file PBF khỏi Git nhưng **giữ nguyên trên đĩa** (không xóa file, chỉ
   ngừng theo dõi):
   ```powershell
   git rm --cached maps/hanoi.osm.pbf maps/chuong_duong.osm.pbf maps/xuan_phuong.osm.pbf
   ```
3. Commit lại việc gỡ này kèm `.gitignore` đã khôi phục.

**Bài học:** khi gặp lỗi "file bị chặn không add được", việc cần làm là kiểm
tra xem file đó CÓ NÊN bị chặn không (thường là có lý do), chứ không phải xóa
luôn dòng chặn trong `.gitignore` cho hết vướng.

## Lỗi 3: Đã xóa `ingest_era5_weather.py` — file này vẫn đang được dùng chung

**Bằng chứng:** commit `1123a29` xóa toàn bộ 145 dòng của
`ingest_era5_weather.py`.

File này không phải code thừa — nó vẫn được nhắc trong
`docs/chuong_duong_track_b_pilot_guide.md` và là script Lan (Từ Liêm) đang
dùng thực tế. Xóa file này trong nhánh của Đức không ảnh hưởng ai khác *cho
đến khi* nhánh này được merge vào `main` — lúc đó sẽ làm hỏng luôn tài liệu và
công cụ chung.

**Cách sửa:** khôi phục lại file đúng như trên `main`:
```powershell
git checkout main -- ingest_era5_weather.py
```

**Nguyên tắc chung:** không xóa file dùng chung chỉ vì mình không dùng đến —
nếu thấy file nào có vẻ thừa, hỏi lại nhóm trước khi xóa, đừng tự quyết trong
nhánh riêng của mình.

## Tóm tắt việc cần làm, đúng thứ tự

1. Xác nhận có file raster thật, đặt đúng đường dẫn.
2. Chạy lại B5 → B3, xác nhận số dân số thay đổi so với bản cũ trước khi cập
   nhật tài liệu.
3. Khôi phục lại 3 dòng đã xóa trong `.gitignore`.
4. `git rm --cached` gỡ các file PBF khỏi Git (giữ trên đĩa).
5. Khôi phục lại `ingest_era5_weather.py` bằng `git checkout main --`.
6. Commit lại toàn bộ, báo lại để review vòng tiếp theo.
