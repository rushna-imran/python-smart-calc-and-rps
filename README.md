# python-smart-calc-and-rps
Smart Calculator &amp; Retro/Modern RPS
python-arcade-and-calc/
├── README.md
├── .gitignore
├── calculator/
│   ├── simple_calc_lilac.py
│   ├── calc_medium.py
│   ├── smart_calc_pro.py
│   └── smart_calc_pro_gui.py
└── rock_paper_scissors/
    ├── rps_arcade_90s.py
    └── rps_modern_pro.py
.gitignore
__pycache__/
*.py[cod]
*.json
.vscode/
# Python Portfolio: Smart Calculator & Retro/Modern RPS

A hands-on progression of Python projects evolving from basic logic and CLI tools into modular architecture, safe expression evaluation, and visual Tkinter interfaces.

## 1. Calculator Evolution
- **Lilac Simple GUI (`calculator/simple_calc_lilac.py`):** Clean beginner calculator with a soft lilac Tkinter UI, basic arithmetic (`+`, `-`, `*`, `/`, `%`), decimals, backspace, clear, and divide-by-zero handling.
- **Medium Engine (`calculator/calc_medium.py`):** Adds operation dictionaries, state tracking, undo/redo stacks, search filtering, and JSON persistence.
- **Advance-Pro Engine & Scientific Pastel GUI (`calculator/smart_calc_pro.py`, `calculator/smart_calc_pro_gui.py`):** Uses Python's `ast` module for safe math parsing (no `eval()`), a `@plugin` decorator registry, `>>` fluent piping with an audit trail, `deque` undo/redo time travel, and a lazy Fibonacci generator (`yield`).

## 2. Rock, Paper, Scissors
- **90s Arcade Edition (`rock_paper_scissors/rps_arcade_90s.py`):** Neon arcade styling, round counter, and retro monospace typography.
- **Modern Pro Edition (`rock_paper_scissors/rps_modern_pro.py`):** Slate UI, `Counter`-based adaptive smart AI toggle, win-rate/streak stats, recent round log, undo support, and keyboard shortcuts.

## How to Run
Run any script in VS Code or terminal (Python 3.10+ recommended):
```bash
python calculator/simple_calc_lilac.py    
