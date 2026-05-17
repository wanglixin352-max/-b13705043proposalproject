## 智慧任務管理系統
## Proposal Report
### 動機與目標
建立一個有效率的任務管理系統 利用資料結構（如 Heap、Queue、Linked List）提升排序與查詢效率 提供直觀的操作介面（CLI 或簡單 GUI）
### 競品比較
目前市面上其實已經有很多任務管理工具，例如 Google Keep、Todoist 等，不過它們大多是已經做好的應用程式，使用者只能操作，無法看到背後的資料結構設計。

像 Google Keep 比較偏簡單筆記型的任務管理，可以快速記錄事情，但在任務排序上比較沒有明確的優先級機制。

Todoist 則功能比較完整，有優先級、截止時間、分類等功能，使用上很方便，但內部如何實作排序（例如是否使用 Heap）是看不到的。

相較之下，本專題的重點不是做一個功能最完整的產品，而是從資料結構的角度出發，自己實作一個任務管理系統。

我們會明確使用：

Heap（Priority Queue）來處理任務排序
Linked List 來儲存任務資料
Queue / Stack（進階）來記錄操作

這樣的設計可以讓我們更清楚理解不同資料結構在實際應用中的效果，例如：

為什麼 Heap 適合做優先排序
為什麼 Linked List 在動態新增刪除上比較方便

總結來說，和現有產品相比，本系統的特色不是功能最多，而是強調資料結構的實作與分析，比較偏學習與實驗性質。
### 預期功能
任務新增與刪除 使用者可以新增任務（名稱、截止時間、優先級） 可刪除已完成或不需要的任務 任務排序（核心） 使用 Priority Queue（Heap） 自動排序任務 優先顯示「最重要或最急迫」的任務 任務查詢 可查詢所有任務 可依條件篩選（例如：今天要做的事） 任務完成標記 標記任務為完成並移除或存入歷史紀錄 資料儲存（進階） 將任務存成檔案（JSON / TXT） 下次開啟可讀取
### 使用技術
程式語言：Python / C++ 資料結構： Heap（Priority Queue）→ 任務排序 Linked List → 任務儲存 Queue / Stack → 操作紀錄（進階） 其他技術： 檔案處理（File I/O） 簡單 CLI 或 GUI（例如 Tkinter）
### Prototype 預計可驗證內容
週數
工作內容

第1週<br/>
題目發想與Proposal撰寫<br/>
第2週<br/>
系統設計（資料結構規劃)<br/>
第3週<br/>
基本功能實作（新增/刪除)<br/>
第4週<br/>
Priority Queue 排序實作<br/>
第5週<br/>
查詢與篩選功能<br/>
第6週<br/>
檔案儲存功能<br/>
第7週<br/>
測試與除錯<br/>
第8週<br/>
完成報告與展示目前已完成系統的基礎架構與部分核心功能


## Prototype Report

### 目前進度
目前已完成系統的基礎架構與部分核心功能：

任務資料結構設計
已定義任務格式（名稱、截止時間、優先級）
使用 Python class（或 C++ struct）表示單一任務
任務新增與刪除功能
可透過 CLI 新增任務
已實作刪除指定任務功能
Priority Queue（Heap）初步實作
已能根據「優先級」排序任務
插入任務時會自動維持 Heap 結構
基本查詢功能
可列出目前所有任務
以排序後的順序顯示（最重要的在前）
簡單 CLI 介面
使用文字選單操作（新增、刪除、查看任務）

### 遇到的困難
Priority Queue 排序邏輯設計
需要同時考慮「截止時間」與「優先級」
在設計比較規則（comparator）時較為困難
資料結構整合問題
Heap 適合排序，但不方便搜尋特定任務
Linked List 與 Heap 的角色分工需要重新設計
刪除任務的效率問題
在 Heap 中刪除非頂端元素較複雜
需要額外設計索引或重新整理 Heap
時間格式處理
使用者輸入的時間格式需要統一與驗證
比較時間時容易出現錯誤

### 下一步計畫
優化排序機制（核心）
設計結合「截止時間 + 優先級」的排序規則
完善 Priority Queue 的比較邏輯
完成查詢與篩選功能
加入「今日任務」、「即將到期任務」篩選
提供關鍵字搜尋功能
任務完成標記功能
新增「完成任務」操作
將完成任務移至歷史紀錄
檔案儲存（File I/O）
將任務儲存為 JSON 或 TXT
系統啟動時自動讀取資料
介面優化（進階）
改善 CLI 使用體驗
或嘗試簡單 GUI（如 Tkinter）

### 程式碼
import heapq
from datetime import datetime

class Task:
    def __init__(self, name, deadline, priority):
        self.name = name
        # deadline 格式：'2026-05-10 23:59'
        self.deadline = datetime.strptime(deadline, "%Y-%m-%d %H:%M")
        self.priority = priority  # 數字越小代表越重要

    def __lt__(self, other):
        # 先比 priority，再比 deadline
        if self.priority == other.priority:
            return self.deadline < other.deadline
        return self.priority < other.priority

    def __repr__(self):
        return f"{self.name} | Priority: {self.priority} | Due: {self.deadline}"


class TaskManager:
    def __init__(self):
        self.heap = []

    def add_task(self, task):
        heapq.heappush(self.heap, task)

    def remove_top_task(self):
        if self.heap:
            return heapq.heappop(self.heap)
        return None

    def show_tasks(self):
        # 不破壞 heap，顯示排序後結果
        sorted_tasks = sorted(self.heap)
        for t in sorted_tasks:
            print(t)

    def find_today_tasks(self):
        today = datetime.now().date()
        return [t for t in self.heap if t.deadline.date() == today]


# ====== 測試 ======
if __name__ == "__main__":
    manager = TaskManager()

    manager.add_task(Task("DS 作業", "2026-05-06 23:59", 1))
    manager.add_task(Task("買晚餐", "2026-05-05 18:00", 3))
    manager.add_task(Task("寫報告", "2026-05-05 20:00", 2))

    print("=== 所有任務（排序後）===")
    manager.show_tasks()

    print("\n=== 今日任務 ===")
    today_tasks = manager.find_today_tasks()
    for t in today_tasks:
        print(t)

    print("\n=== 完成一個任務 ===")
    done = manager.remove_top_task()
    print("完成：", done)

    print("\n=== 剩餘任務 ===")
    manager.show_tasks()

---

## Final Report

### 專案說明
說明

### 使用方式
<!-- 如何編譯、執行、使用你的程式 -->

### 與課程的關聯總結
<!-- 總結你的專題與進階程式設計及資料結構課程之間的關聯 -->
