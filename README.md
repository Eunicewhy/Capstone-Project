# Capstone-Project

本專案實作一套用於 **Fixed-Outline Floorplanning** 的演算法框架，目標是在固定晶片外框下，安排 soft modules 與 fixed modules 的位置，並同時達成：

- 提高空間利用率
- 降低總線長（HPWL, Half-Perimeter Wirelength）
- 避免模組重疊
- 滿足 fixed-outline 的限制

本方法結合了：

- **Fiduccia-Mattheyses (FM) Partitioning**
- **Quad-Btree**
- **Linear Ordering Algorithm**
- **Fast Simulated Annealing (Fast-SA)**
- **Deformation for soft modules**

---

## 專案概念

在 VLSI 實體設計中，floorplanning 是很重要的一步。  
本專案主要針對以下問題：

1. 晶片外框大小固定
2. 同時存在 fixed modules 與 soft modules
3. soft modules 可調整形狀，但仍需符合面積利用率與長寬比限制
4. 希望在合法擺放的前提下，盡可能縮短 HPWL

---

## 方法流程

整體流程如下：

1. **FM Partitioning**
   - 依照模組間的連線關係，將晶片劃分成四個子區域
   - 讓彼此連線較密切的模組盡量靠近

2. **Linear Ordering**
   - 決定模組的初始擺放順序
   - 作為後續優化的起點

3. **Quad-Btree Placement**
   - 使用自訂的 Quad-Btree 結構描述模組相對位置
   - 從四個角落往中心進行擺放

4. **Fast Simulated Annealing**
   - 透過多種 perturbation 操作搜尋更好的解
   - 以 HPWL 與 boundary penalty 作為 cost function

5. **Deformation**
   - 調整 soft module 的形狀
   - 減少空白區域並提升整體 placement 品質

---

## Cost Function

本專案以 HPWL 作為主要優化目標，並加入超出固定外框的懲罰項：

- **W**：目前的 HPWL
- **Wnorm**：正規化用的平均 HPWL
- **P**：超出晶片邊界的 penalty
- **α**：權重參數

演算法會根據不同案例的 whitespace ratio，調整 cost function 的比重。

---

## 特色

- 支援 **fixed modules + soft modules** 的混合情況
- 使用 **Quad-Btree** 改善傳統 B\*tree 在此問題上的限制
- 搭配 **Fast-SA** 提升搜尋效率
- 可對 soft modules 做 **deformation**
- 兼顧 **合法性** 與 **線長最佳化**

---

## 實驗結果摘要

根據論文中的實驗結果：

- 此方法可成功將模組放入固定外框內
- 相較於只從單一角落開始擺放，搭配 **FM 分區** 能得到更好的初始解
- **Global placement** 比 local placement 更能降低 HPWL
- **Fast-SA** 相較於傳統 Classical-SA 有更好的收斂效率
- 在部分案例中，結果甚至優於官方提供的 benchmark 資料

---

## 適用場景

這個專案適合用於：

- VLSI floorplanning 研究
- Fixed-outline floorplanning 問題
- Simulated Annealing 相關最佳化研究
- EDA / CAD 課程專題與實驗

---

## 參考關鍵字

- Floorplanning
- Simulated Annealing
- Fixed-Outline
- HPWL
- FM Partitioning
- Quad-Btree
- Soft Module Deformation

---

## 備註

本 README 為依據論文內容整理的簡要版本，適合作為 GitHub 專案首頁說明。  
若需要更完整版本，可再補上：

- 專案結構
- 執行方式
- 輸入輸出格式
- benchmark 說明
- 視覺化結果展示
