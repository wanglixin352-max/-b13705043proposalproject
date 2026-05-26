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
本專題為「智慧任務管理系統」，目的是利用資料結構設計一個可以管理任務的系統。

目前很多工具像 Google Google Keep 或 Todoist Todoist 都可以管理任務，但看不到背後的資料結構設計。因此本專題重點是自己實作資料結構。

### 使用方式
系統目前提供：

新增任務
查看任務
完成任務
查詢今日任務

使用者只需要依照畫面提示輸入：

任務名稱
截止時間
優先級

系統會自動完成排序並顯示結果。

### 與課程的關聯總結
本專題成功完成基本的智慧任務管理系統，並實際應用了 Heap、Linked List 等資料結構。雖然系統仍可繼續擴充，但已完成核心功能與基本架構。
### 程式碼

import heapq
import json
from datetime import datetime
from collections import deque
from typing import Optional, List, Any

# ==================== 任務類別 ====================
class Task:
    """單一任務，支援 heapq 比較（優先級 + 截止時間）"""
    def __init__(self, name: str, deadline: str, priority: int, task_id: int):
        self.id = task_id                     # 唯一識別碼，用於比對
        self.name = name
        # deadline 格式：'YYYY-MM-DD HH:MM'
        self.deadline = datetime.strptime(deadline, "%Y-%m-%d %H:%M")
        self.priority = priority              # 數字越小越重要

    def __lt__(self, other):
        # 先比 priority，再比 deadline
        if self.priority == other.priority:
            return self.deadline < other.deadline
        return self.priority < other.priority

    def __repr__(self):
        return f"{self.name} | 優先級:{self.priority} | 到期:{self.deadline.strftime('%Y-%m-%d %H:%M')}"

    def to_dict(self):
        return {
            "id": self.id,
            "name": self.name,
            "deadline": self.deadline.strftime("%Y-%m-%d %H:%M"),
            "priority": self.priority
        }

    @classmethod
    def from_dict(cls, data):
        return cls(data["name"], data["deadline"], data["priority"], data["id"])


# ==================== 雙向鏈結串列節點 ====================
class Node:
    def __init__(self, task: Task):
        self.task = task
        self.prev: Optional['Node'] = None
        self.next: Optional['Node'] = None


# ==================== 雙向鏈結串列（儲存所有任務） ====================
class LinkedList:
    def __init__(self):
        self.head: Optional[Node] = None
        self.tail: Optional[Node] = None
        self.size = 0

    def append(self, task: Task) -> Node:
        """在尾部新增節點，回傳節點"""
        node = Node(task)
        if self.tail is None:
            self.head = self.tail = node
        else:
            self.tail.next = node
            node.prev = self.tail
            self.tail = node
        self.size += 1
        return node

    def remove(self, node: Node) -> Task:
        """從鏈結串列中移除指定節點，回傳其任務"""
        if node.prev:
            node.prev.next = node.next
        else:
            self.head = node.next

        if node.next:
            node.next.prev = node.prev
        else:
            self.tail = node.prev

        self.size -= 1
        node.prev = node.next = None
        return node.task

    def to_list(self) -> List[Task]:
        """將所有任務依序輸出為 list"""
        tasks = []
        cur = self.head
        while cur:
            tasks.append(cur.task)
            cur = cur.next
        return tasks

    def find_node_by_id(self, task_id: int) -> Optional[Node]:
        """根據任務 ID 尋找節點（O(n)）"""
        cur = self.head
        while cur:
            if cur.task.id == task_id:
                return cur
            cur = cur.next
        return None


# ==================== 任務管理器（整合 Heap + LinkedList + Queue/Stack） ====================
class TaskManager:
    def __init__(self):
        # 底層儲存：雙向鏈結串列
        self.task_list = LinkedList()

        # 排序引擎：最小堆積 (Priority Queue)
        self.heap: List[Task] = []

        # 操作紀錄 (Queue: 處理順序, Stack: Undo)
        self.history_queue = deque()      # 儲存操作描述字串
        self.undo_stack = []              # 儲存 (操作類型, 任務資料) 用於復原

        # 任務 ID 生成器
        self.next_id = 1

        # 載入時重建 heap 用
        self._heap_dirty = True           # 標記 heap 是否需要重建

    def _rebuild_heap(self):
        """將鏈結串列中所有任務重建至 heap 中"""
        self.heap = [task for task in self.task_list.to_list()]
        heapq.heapify(self.heap)
        self._heap_dirty = False

    def _ensure_heap_valid(self):
        """若 heap 髒了就重建（適用於刪除任務後）"""
        if self._heap_dirty:
            self._rebuild_heap()

    def add_task(self, name: str, deadline: str, priority: int):
        """新增任務"""
        task = Task(name, deadline, priority, self.next_id)
        self.next_id += 1

        # 加入鏈結串列
        node = self.task_list.append(task)

        # 加入 heap
        heapq.heappush(self.heap, task)

        # 紀錄操作
        self.history_queue.append(f"新增任務: {task.name}")
        self.undo_stack.append(('add', task.id))

        print(f"✓ 已新增任務：{task}")

    def delete_task_by_id(self, task_id: int):
        """根據 ID 刪除任務（同時從鏈結串列與 heap 中移除）"""
        node = self.task_list.find_node_by_id(task_id)
        if node is None:
            print("❌ 找不到該任務 ID")
            return

        task = node.task
        # 從鏈結串列移除
        self.task_list.remove(node)

        # 標記 heap 需要重建（無法高效刪除非頂端元素）
        self._heap_dirty = True

        # 紀錄操作
        self.history_queue.append(f"刪除任務: {task.name}")
        self.undo_stack.append(('delete', task))

        print(f"✓ 已刪除任務：{task}")

    def complete_top_task(self):
        """完成最高優先級任務（從堆頂取出）"""
        self._ensure_heap_valid()
        if not self.heap:
            print("目前沒有任何任務")
            return

        top_task = heapq.heappop(self.heap)
        # 從鏈結串列中移除對應節點
        node = self.task_list.find_node_by_id(top_task.id)
        if node:
            self.task_list.remove(node)
        else:
            # 可能已被其他操作刪除，但 heap 中還有殘留（lazy deletion 處理）
            # 這裡簡單重建 heap 確保一致性
            self._rebuild_heap()
            print("⚠️ 資料不一致，已自動修復，請重新嘗試完成任務")
            return

        self.history_queue.append(f"完成任務: {top_task.name}")
        self.undo_stack.append(('complete', top_task))

        print(f"🎉 已完成任務：{top_task}")

    def show_all_tasks(self):
        """顯示所有任務（依優先級排序）"""
        self._ensure_heap_valid()
        if not self.heap:
            print("📭 目前沒有任何任務")
            return

        print("\n=== 所有任務（依優先級與到期日排序）===")
        # 顯示排序後的所有任務（不破壞 heap）
        sorted_tasks = sorted(self.heap)
        for i, t in enumerate(sorted_tasks, 1):
            print(f"{i}. {t}")
        print()

    def show_today_tasks(self):
        """顯示今日到期的任務"""
        today = datetime.now().date()
        self._ensure_heap_valid()
        today_tasks = [t for t in self.heap if t.deadline.date() == today]

        if not today_tasks:
            print("📭 今日沒有任何到期任務")
            return

        print("\n=== 今日到期任務 ===")
        for i, t in enumerate(sorted(today_tasks), 1):
            print(f"{i}. {t}")
        print()

    def undo(self):
        """復原上一個操作"""
        if not self.undo_stack:
            print("⚠️ 沒有可以復原的操作")
            return

        last_op = self.undo_stack.pop()
        op_type, data = last_op

        if op_type == 'add':
            # 復原新增：刪除該任務
            self.delete_task_by_id(data)
            print("↩️ 已復原「新增」操作")
        elif op_type == 'delete':
            # 復原刪除：重新加入該任務
            task = data
            # 重新設定 ID 避免衝突（簡單處理：重新產生新 ID）
            new_id = self.next_id
            self.next_id += 1
            new_task = Task(task.name, task.deadline.strftime("%Y-%m-%d %H:%M"), task.priority, new_id)
            self.task_list.append(new_task)
            heapq.heappush(self.heap, new_task)
            self._heap_dirty = False
            print(f"↩️ 已復原「刪除」操作，任務重新加入：{new_task}")
        elif op_type == 'complete':
            # 復原完成：重新加入任務
            task = data
            new_id = self.next_id
            self.next_id += 1
            new_task = Task(task.name, task.deadline.strftime("%Y-%m-%d %H:%M"), task.priority, new_id)
            self.task_list.append(new_task)
            heapq.heappush(self.heap, new_task)
            self._heap_dirty = False
            print(f"↩️ 已復原「完成」操作，任務重新加入：{new_task}")

        # 清除歷史佇列中的對應紀錄（簡單跳過，不實作精準移除）
        # 這裡僅留作展示，實際可選擇清空或保留

    def show_history(self):
        """顯示操作歷史（Queue 內容）"""
        if not self.history_queue:
            print("📜 尚無操作紀錄")
            return
        print("\n=== 操作歷史（依處理順序）===")
        for i, record in enumerate(self.history_queue, 1):
            print(f"{i}. {record}")
        print()

    def save_to_file(self, filename: str = "tasks.json"):
        """將任務儲存至 JSON 檔案"""
        tasks_data = [task.to_dict() for task in self.task_list.to_list()]
        with open(filename, 'w', encoding='utf-8') as f:
            json.dump(tasks_data, f, indent=4, ensure_ascii=False)
        print(f"💾 已儲存 {len(tasks_data)} 個任務至 {filename}")

    def load_from_file(self, filename: str = "tasks.json"):
        """從 JSON 檔案載入任務（覆蓋目前所有資料）"""
        try:
            with open(filename, 'r', encoding='utf-8') as f:
                tasks_data = json.load(f)
        except FileNotFoundError:
            print("⚠️ 找不到檔案，將從空白開始")
            return
        except json.JSONDecodeError:
            print("❌ 檔案格式錯誤")
            return

        # 清除現有資料
        self.task_list = LinkedList()
        self.heap.clear()
        self.history_queue.clear()
        self.undo_stack.clear()
        # 重新建立任務
        max_id = 0
        for data in tasks_data:
            task = Task.from_dict(data)
            self.task_list.append(task)
            heapq.heappush(self.heap, task)
            max_id = max(max_id, task.id)
        self.next_id = max_id + 1
        self._heap_dirty = False
        print(f"📂 已從 {filename} 載入 {len(tasks_data)} 個任務")


# ==================== CLI 主程式 ====================
def main():
    manager = TaskManager()
    print("══════════════════════════════════════")
    print("   智慧任務管理系統 (Smart Task Manager)")
    print("══════════════════════════════════════")

    while True:
        print("\n--- 主選單 ---")
        print("1. 新增任務")
        print("2. 刪除任務（依 ID）")
        print("3. 完成最高優先級任務")
        print("4. 顯示所有任務（排序後）")
        print("5. 顯示今日任務")
        print("6. 復原上一個操作 (Undo)")
        print("7. 顯示操作歷史")
        print("8. 儲存任務至檔案")
        print("9. 載入任務自檔案")
        print("0. 離開系統")
        choice = input("請輸入選擇: ").strip()

        if choice == '1':
            name = input("任務名稱: ").strip()
            deadline = input("截止時間 (格式 YYYY-MM-DD HH:MM): ").strip()
            try:
                priority = int(input("優先級 (數字越小越重要): ").strip())
                manager.add_task(name, deadline, priority)
            except ValueError:
                print("❌ 優先級請輸入整數")
            except Exception as e:
                print(f"❌ 輸入錯誤: {e}")

        elif choice == '2':
            try:
                task_id = int(input("請輸入要刪除的任務 ID: ").strip())
                manager.delete_task_by_id(task_id)
            except ValueError:
                print("❌ ID 請輸入數字")

        elif choice == '3':
            manager.complete_top_task()

        elif choice == '4':
            manager.show_all_tasks()

        elif choice == '5':
            manager.show_today_tasks()

        elif choice == '6':
            manager.undo()

        elif choice == '7':
            manager.show_history()

        elif choice == '8':
            filename = input("儲存檔案名稱 (預設 tasks.json): ").strip()
            if not filename:
                filename = "tasks.json"
            manager.save_to_file(filename)

        elif choice == '9':
            filename = input("載入檔案名稱 (預設 tasks.json): ").strip()
            if not filename:
                filename = "tasks.json"
            manager.load_from_file(filename)

        elif choice == '0':
            print("👋 感謝使用，再見！")
            break
        else:
            print("❌ 無效選項，請重新輸入")


if __name__ == "__main__":
    main()
