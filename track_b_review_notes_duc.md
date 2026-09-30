# Ghi chú review — kết quả Xuân Phương (gửi Đức)

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

## 1. Raster dân số khác chuẩn Chương Dương — phải đổi lại

**Vấn đề:** Xuân Phương dùng `vnm_ppp_2020_100m.tif`, trong khi chuẩn đã chốt
là `vnm_ppp_2020_UNadj_constrained.tif` (Chương Dương đang dùng). Đây là 2 sản
phẩm WorldPop khác phương pháp tính dân số — dùng khác nhau giữa các phường sẽ
làm cột `population`/`population_density` không so sánh được khi ghép dữ liệu
toàn Hà Nội.

**Đây không hoàn toàn là lỗi của Đức** — tài liệu hướng dẫn trước đây chỉ ghi
chung chung "tải trên trang WorldPop", không trỏ đến file cụ thể. Nhóm sẽ
được cấp lại đúng 1 file duy nhất (xem việc cần làm của leader ở cuối).

**Việc cần làm:**
- [ ] Xóa `data/raw/worldpop/vnm_ppp_2020_100m.tif`.
- [ ] Nhận file `vnm_ppp_2020_UNadj_constrained.tif` chuẩn từ leader, đặt vào
      `data/raw/population/vnm_ppp_2020_UNadj_constrained.tif` (đúng tên thư
      mục `population`, không phải `worldpop`).
- [ ] Chạy lại `build_population_poi_features.py` với `--population-raster`
      trỏ đúng file mới.
- [ ] Cập nhật lại số liệu `population`/`population_density` trong các tài
      liệu output.

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

1. Đổi raster dân số đúng file chuẩn, chạy lại B5.
2. Gửi lại bản sửa `collect_weather.py` cho leader để đồng bộ vào repo chung.
3. Sửa đường dẫn 2 file dùng chung (`maps/hanoi.osm.pbf`,
   `data/raw/population/`) cho khớp quy ước.
4. Sửa lỗi chính tả múi giờ trong tài liệu.

**Không cần làm lại:** phần thời tiết 2019-2025 và cách render ảnh vector —
cả hai đều đã đúng chuẩn hoặc tốt hơn chuẩn.
