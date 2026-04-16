[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/tfm_-hwX)
# Project 2: Moonlight Museum After Dark


## Project summary
Our project builds a museum management system for organizing strange artifacts during a secret late-night exhibition. The system uses multiple data structures — a BST for fast artifact lookup, a queue for restoration requests, a stack for undo history, and a linked list for the guided exhibit route. Helper functions generate category reports, room summaries, and sorted artifact lists.

---

## Feature checklist

### Core structures
- [x] `Artifact` class/record
- [x] `ArtifactBST`
- [x] `RestorationQueue`
- [x] `ArchiveUndoStack`
- [x] `ExhibitRoute` singly linked list

### BST features
- [x] insert artifact
- [x] search by ID
- [x] preorder traversal
- [x] inorder traversal
- [x] postorder traversal
- [x] duplicate IDs ignored

### Queue features
- [x] add request
- [x] process next request
- [x] peek next request
- [x] empty check
- [x] size

### Stack features
- [x] push action
- [x] undo last action
- [x] peek last action
- [x] empty check
- [x] size

### Linked list features
- [x] add stop to end
- [x] remove first matching stop
- [x] list stops in order
- [x] count stops

### Utility/report features
- [x] category counts
- [x] unique rooms
- [x] sort by age
- [x] linear search by name

### Integration
- [x] `demo_museum_night()`
- [x] at least 8 artifacts in demo
- [x] demo shows system parts working together

---

## Design note (150-250 words)

We chose a **Binary Search Tree** for the artifact archive because artifact IDs are unique integers, which makes BST ordering natural. Searching by ID takes O(h) time — much faster than scanning a list — and inorder traversal produces artifacts in sorted ID order for free, which is useful for audits and reports.

A **queue** fits restoration requests because the museum staff should handle the oldest request first. This is the classic FIFO problem: the first artifact flagged for repair should be the first one serviced. Python's `collections.deque` makes both enqueue and dequeue O(1).

A **stack** fits the undo system because the most recent action is always the one to reverse. LIFO order is exactly what undo requires. A plain Python list with `append` and `pop` gives us O(1) push and undo with no extra imports.

A **singly linked list** fits the exhibit route because the route is ordered and we frequently add stops to the end or remove a specific stop by name. The linked list makes structural changes easy without shifting elements like a list would require.

The system is organized into clearly separated classes for each structure, with standalone utility functions for reporting. The `demo_museum_night` function ties everything together to prove the system works as a whole.

---

## Complexity reasoning

- `ArtifactBST.insert`: O(h) where h is the tree height, because we follow one path from root to an empty child slot. In the worst case (sorted input) h = n, giving O(n).
- `ArtifactBST.search_by_id`: O(h) — we follow at most one path from root to a leaf, comparing IDs at each step.
- `ArtifactBST.inorder_ids`: O(n) — every node is visited exactly once during the recursive traversal.
- `RestorationQueue.process_next_request`: O(1) — `deque.popleft()` removes the front element in constant time.
- `ArchiveUndoStack.undo_last_action`: O(1) — `list.pop()` removes the last element in constant time.
- `ExhibitRoute.remove_stop`: O(n) — in the worst case we walk the entire list to find the matching stop.
- `sort_artifacts_by_age`: O(n log n) — Python's built-in `sorted()` uses Timsort.
- `linear_search_by_name`: O(n) — we scan the list from the front until a name matches or the list ends.

---

## Edge-case checklist

### BST
- [x] insert into empty tree — first insert sets `self.root` directly
- [x] search for missing ID — recursive search returns `None` when it reaches a `None` node
- [x] empty traversals — all three traversals return `[]` when `self.root is None`
- [x] duplicate ID — `_insert` compares IDs and returns `False` without modifying the tree

### Queue
- [x] process empty queue — `process_next_request` checks `if self._items` before calling `popleft`
- [x] peek empty queue — `peek_next_request` returns `None` when `_items` is empty

### Stack
- [x] undo empty stack — `undo_last_action` returns `None` when `_items` is empty
- [x] peek empty stack — `peek_last_action` returns `None` when `_items` is empty

### Exhibit route linked list
- [x] empty route — `list_stops` returns `[]`, `count_stops` returns `0`
- [x] remove missing stop — `remove_stop` returns `False` after walking the full list without a match
- [x] remove first stop — handled as a special case: `self.head = self.head.next`
- [x] remove middle stop — `prev.next = current.next` skips the matching node
- [x] remove last stop — same `prev.next = current.next` sets `prev.next = None`
- [x] one-stop route — handled by the head special case; result is an empty list

### Reports
- [x] empty artifact list — all four functions return empty dict, set, list, or `None`
- [x] repeated categories — `count_artifacts_by_category` uses `dict.get` with a default of 0
- [x] repeated rooms — `unique_rooms` uses a set comprehension, so duplicates are removed automatically
- [x] missing artifact name — `linear_search_by_name` returns `None` when no match is found
- [x] same-age artifacts — `sort_artifacts_by_age` uses a stable sort, so equal-age artifacts keep their original relative order

---

## Demo plan / how to run

Run the full test suite:
```bash
python -m pytest -q
```

Run the integration demo:
```bash
python -c "from src.project import demo_museum_night; demo_museum_night()"
```

---

## Assistance & sources
- AI used? Y
- What it helped with: code structure, recursive BST logic, edge-case review, README writing
- Non-course sources used: Python docs for `collections.deque`, `sorted()`, `dataclasses`
- Links: https://docs.python.org/3/library/collections.html#collections.deque