# Stats Engine — những con số tạo nên nhân vật

Stats Engine cho phép game ghi lại đủ thứ về nhân vật: sức mạnh và máu trong RPG, lòng can đảm hay lòng tin trong tiểu thuyết thị giác. Game mô tả chỉ số của mình bằng dữ liệu — tên gọi, cách tính từ nhau, cách lớn theo cấp — còn bộ máy tính bảng nhân vật cho từng người, hiển thị trong **Bảng nhân vật**, cho kịch bản đọc và thay đổi các con số, và trao máu, tấn công, phòng ngự cho các trận chiến.

Bản thân bộ máy không biết chỉ số nào cả. Sức mạnh, nhanh nhẹn, máu và mana trong các hình dưới đây đến từ gói luật **Classic RPG Rules** mà RPG Plus có sẵn; cùng bộ máy đó, một game khác vẫn có thể mang những chỉ số hoàn toàn khác.

## Bảng nhân vật

Mở bằng biểu tượng bảng trên thanh công cụ.

![Bảng nhân vật trong RPG Plus: Anh hùng — Chiến binh cấp 1 với 0/20 kinh nghiệm; Thuộc tính (Sức mạnh 9, Nhanh nhẹn 5, Trí tuệ 2, Thể lực 8, Sức hút 4, May mắn 4), Chiến đấu (Máu 42 +4, Mana 6/6, Tấn công 12 +2, Giáp 4, Ma thuật 3) và khối Trang bị với thanh kiếm gỗ.](assets/sheet.jpg)

- **Đội** bên trái, khi có từ hai nhân vật trở lên: bấm tên để xem bảng của nhân vật đó. Người đang xem được tô sáng, đội trưởng (nhân vật của người chơi) có ghi «đội trưởng».
- **Lớp, cấp và kinh nghiệm** ở trên, kèm mô tả lớp nhân vật.
- **Chỉ số chia theo nhóm**, tên do game đặt. Con số xanh nhỏ là phần đến từ cộng thêm — trang bị, đặc tính của lớp. Chỉ số tiêu hao rồi hồi lại, như mana, hiển thị hiện tại / tối đa.
- **Khối của mod khác** bên dưới, ví dụ Trang bị với các nút Trang bị, Thay và Tháo.

![Bảng nhân vật với hai nhân vật: danh sách đội bên trái có Hero (đội trưởng) và Ally; người đang xem được tô sáng.](assets/sheet-party.png)

## Một lựa chọn, hai nhân vật khác nhau

Trong phần mở đầu của RPG Plus, người chơi kể nhân vật chính từng là ai. Câu trả lời trao cho anh ta một lớp nhân vật — một **nguyên mẫu** — và cả bảng nhân vật đều suy ra từ đó:

![Hai bảng cạnh nhau sau phần mở đầu. Chiến binh: Sức mạnh 9, Trí tuệ 2, Thể lực 8, Máu 42, Mana 6/6, Tấn công 12, Ma thuật 3. Pháp sư: Sức mạnh 3, Trí tuệ 10, Thể lực 5, Máu 29, Mana 34/34, Tấn công 4, Ma thuật 15.](assets/warrior-mage.jpg)

Dòng kịch bản đằng sau câu trả lời đó:

```scenario
action stat.archetype who=rpg_hero id=classic/warrior
```

Nguyên mẫu Chiến binh trong Classic RPG Rules:

```json
{
  "Id": "classic/warrior",
  "Name": "classic/archetype/warrior",
  "Stats": { "str": "7 + level * 2", "agi": "4 + level", "int": "2 + floor(level / 2)",
             "vit": "6 + level * 2", "cha": "4 + floor(level / 2)", "luk": "4 + floor(level / 2)" },
  "Traits": [ { "Stat": "hp", "Percent": 10 } ],
  "Learnings": [ { "Level": 1, "Action": "classic/power_strike" }, { "Level": 3, "Action": "classic/guard_break" } ]
}
```

và hai chỉ số của nó:

```json
{ "Id": "hp",  "Group": "combat", "Formula": "12 + vit * 3 + level * 2", "Min": 1, "Binds": "health" },
{ "Id": "atk", "Group": "combat", "Formula": "str + level", "Min": 0, "Binds": "attack" }
```

Ở cấp 1, Chiến binh có Sức mạnh 7 + 2 = **9** và Thể lực 6 + 2 = **8**. Máu là 12 + 8 × 3 + 2 = 38, đặc tính của Chiến binh cộng thêm 10% — **42**, chính là "+4" trên bảng. Tấn công là 9 + 1 = 10, cộng 2 từ đặc tính của kiếm gỗ — **12**. Con số của Pháp sư đến từ những chỉ số ấy cùng nguyên mẫu Pháp sư.

## Dành cho tác giả: mô tả chỉ số

**Chỉ số** là thực thể loại `stat`:

- `Id`, cùng `Name`, `Short`, `Description` làm khóa dịch;
- `Group` và `Order` — nó hiện ở đâu trên bảng (`stats/group/<group>` đặt tên nhóm);
- `Default`, hoặc `Formula` từ các chỉ số khác và `level`; `Min`, `Max`;
- `Pool: true` — chỉ số tiêu hao rồi hồi lại, như mana;
- `Binds` — con số chiến đấu nó trở thành: `health`, `attack`, `defence` hoặc `point`;
- `Hidden` — vẫn tính nhưng không hiển thị.

**Nguyên mẫu** (loại `archetype`) là lớp nhân vật, hay tính cách trong tiểu thuyết: `Stats` với giá trị hoặc công thức cho từng chỉ số, `Traits`, `Learnings` (hành động học được theo cấp), `ExpCurve` và `MaxLevel`.

**Đặc tính** — dạng `{ "Stat": "atk", "Add": 2 }`, với `Add`, `Percent` hoặc `Formula` — có thể ghi vào bất kỳ thực thể nào: nguyên mẫu, nhân vật, vật phẩm. Thanh kiếm gỗ cộng tấn công mà mod vật phẩm chẳng biết gì về chỉ số, nhờ cách này.

**Công thức** dùng `+ - * / % ^`, so sánh, `&& || !`, `cond ? a : b` và các hàm như `min`, `max`, `floor`, `round`, `clamp`, `rand(lo, hi)`, `chance(p)`. Tên lạ tính bằng 0, còn công thức hỏng được báo một lần trong nhật ký.

Giá trị được tìm thế nào: mục `Stats` riêng của nhân vật, nếu không thì của nguyên mẫu, nếu không thì `Formula` của chỉ số, nếu không thì `Default`; sau đó là các cộng thêm vĩnh viễn; rồi mọi đặc tính; cuối cùng ghép vào `Min`/`Max`.

## Trong kịch bản

```scenario
action stat.add stat=cha value=1 who=rpg_hero
action stat.exp value=35 who=rpg_hero
when "stat(rpg_hero, cha) >= 8" -> :charming
```

- `stat.add`, `stat.archetype`, `stat.exp` (kinh nghiệm, nâng cấp theo đường cong), `stat.restore` (hồi máu và các chỉ số tiêu hao); `who=` là id nhân vật, `player` hoặc `party`.
- Điều kiện: `stat(who, stat)`, `level(who)`, `archetype(who, 'id')`. Id có dấu gạch chéo phải đặt trong ngoặc kép.
- Phép kiểm chỉ số có xúc xắc (`check cha 10 -> :label`) đến từ Stat Checks; trang bị từ Equipment.

## Chiến đấu

Chỉ số có `Binds` được ghi vào con số chiến đấu của thành viên đội, nên cả hai lõi chiến đấu đều dùng mà chẳng biết gì về chỉ số. Nhân vật không có nguyên mẫu, chỉ số hay đặc tính thì giữ nguyên số từ tệp nhân vật; thêm một gói luật vào game không dùng chỉ số thì chẳng thay đổi gì.

## Khi có trục trặc

1. **Một chỉ số hiện 0.** Công thức của nó gọi một chỉ số không tồn tại (tên lạ tính bằng 0); nhật ký báo công thức hỏng một lần.
2. **Nhân vật không lên cấp.** Chỉ nhân vật có nguyên mẫu mới lên cấp, trừ khi cài đặt `stats.leveling` của game là `all`.
3. **Con số chiến đấu không đổi.** Chỉ số cần `Binds`, và nhân vật phải tham gia hệ thống chỉ số (có nguyên mẫu, chỉ số hoặc đặc tính riêng, hoặc đang mang trang bị).
