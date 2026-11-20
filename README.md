# Python Portfolio: Smart Calculator & Retro/Modern RPS

A hands-on progression of Python projects evolving from basic logic and CLI scripts into modular architecture, safe expression evaluation, and visual Tkinter interfaces.

## Calculators
- **Simple Lilac GUI (`simple_calc_lilac.py`):** Clean beginner calculator in a soft light-purple Tkinter interface with `+`, `-`, `*`, `/`, `%`, decimals, backspace, clear, and divide-by-zero handling.
- **Medium Engine (`calc_medium.py`):** Adds operation dictionaries, state tracking, undo/redo stacks, history search, and JSON persistence.
- **Advance-Pro Engine & Scientific Pastel GUI (`smart_calc_pro.py`, `smart_calc_pro_gui.py`):** Features safe `ast` expression parsing (no `eval()`), a `@plugin` decorator registry, `>>` fluent piping with an audit trail, `deque` undo/redo time travel, and a lazy Fibonacci generator (`yield`).

## Rock, Paper, Scissors
- **90s Arcade Edition (`rps_arcade_90s.py`):** Retro arcade styling with neon colors, round counter, and monospace fonts.
- **Modern Pro Edition (`rps_modern_pro.py`):** Dark slate dashboard with a `Counter`-based smart AI toggle, win-rate and streak stats, recent round log, undo support, and keyboard shortcuts.

## How to Run
Run any file in VS Code or a browser Tkinter editor:

```bash
python simple_calc_lilac.py
[rockpaperscissors-tk.py](https://github.com/user-attachments/files/35183634/rockpaperscissors-tk.py)
import random
import tkinter as tk

CHOICES = ["rock", "paper", "scissors"]

# 90s Retro Arcade Palette
BG_DARK = "#0d0221"  # Deep midnight violet-black
BG_PANEL = "#190538"  # Retro cabinet panel
NEON_CYAN = "#00f5d4"  # Electric cyan
NEON_PINK = "#ff2a85"  # Arcade hot pink
NEON_YELLOW = "#fee440"  # High-score yellow
NEON_LIME = "#70e000"  # Win green
TEXT_WHITE = "#f8f9fa"

# Chunky monospace fonts that degrade gracefully in web sandboxes
FONT_TITLE = ("Courier New", 18, "bold")
FONT_SUB = ("Courier New", 10, "bold")
FONT_STATUS = ("Courier New", 12, "bold")
FONT_BTN = ("Courier New", 11, "bold")
FONT_SMALL = ("Courier New", 9, "bold")


class RPSGuiGame:

  def __init__(self, root: tk.Tk):
    self.root = root
    self.root.title("★ RPS ARCADE '94 ★")
    self.root.geometry("440x410")
    self.root.resizable(False, False)
    self.root.configure(bg=BG_DARK)

    self.user_score = 0
    self.comp_score = 0
    self.round_num = 0
    self.game_over = False

    # Outer neon frame
    outer = tk.Frame(
        root,
        bg=BG_DARK,
        highlightbackground=NEON_PINK,
        highlightthickness=3,
        padx=12,
        pady=10,
    )
    outer.pack(fill=tk.BOTH, expand=True, padx=8, pady=8)

    tk.Label(
        outer,
        text="=== ROCK PAPER SCISSORS ===",
        font=FONT_TITLE,
        bg=BG_DARK,
        fg=NEON_CYAN,
    ).pack(pady=(2, 0))

    tk.Label(
        outer,
        text="1994 ARCADE EDITION  //  INSERT COIN",
        font=FONT_SMALL,
        bg=BG_DARK,
        fg=NEON_PINK,
    ).pack(pady=(0, 8))

    # Scoreboard cabinet box
    board = tk.Frame(
        outer,
        bg=BG_PANEL,
        highlightbackground=NEON_CYAN,
        highlightthickness=2,
        padx=10,
        pady=8,
    )
    board.pack(fill=tk.X, pady=4)

    self.round_label = tk.Label(
        board,
        text="ROUND: 00",
        font=FONT_SMALL,
        bg=BG_PANEL,
        fg=NEON_YELLOW,
    )
    self.round_label.pack()

    self.score_label = tk.Label(
        board,
        text="Score -> You: 0 | Computer: 0",
        font=FONT_SUB,
        bg=BG_PANEL,
        fg=TEXT_WHITE,
        pady=3,
    )
    self.score_label.pack()

    # Arena / status box
    arena = tk.Frame(
        outer,
        bg=BG_PANEL,
        highlightbackground=NEON_YELLOW,
        highlightthickness=2,
        padx=10,
        pady=10,
    )
    arena.pack(fill=tk.X, pady=8)

    self.comp_label = tk.Label(
        arena,
        text="CPU PICK: [ --- ]",
        font=FONT_SUB,
        bg=BG_PANEL,
        fg=NEON_CYAN,
    )
    self.comp_label.pack(pady=(0, 4))

    self.result_label = tk.Label(
        arena,
        text="PICK ROCK, PAPER, OR SCISSORS!",
        font=FONT_STATUS,
        bg=BG_PANEL,
        fg=NEON_YELLOW,
        wraplength=370,
        justify=tk.CENTER,
    )
    self.result_label.pack()

    # Choice buttons
    btn_frame = tk.Frame(outer, bg=BG_DARK)
    btn_frame.pack(pady=10)

    button_styles = {
        "rock": ("🪨 ROCK", NEON_PINK),
        "paper": ("📄 PAPER", NEON_CYAN),
        "scissors": ("✂️ SCISS", NEON_YELLOW),
    }

    self.choice_buttons = []
    for choice in CHOICES:
      label_text, accent = button_styles[choice]
      btn = tk.Button(
          btn_frame,
          text=label_text,
          font=FONT_BTN,
          bg=BG_PANEL,
          fg=accent,
          activebackground=accent,
          activeforeground=BG_DARK,
          highlightbackground=accent,
          highlightthickness=2,
          bd=2,
          relief=tk.RAISED,
          width=11,
          pady=6,
          cursor="hand2",
          command=lambda c=choice: self.play_round(c),
      )
      btn.pack(side=tk.LEFT, padx=5)
      self.choice_buttons.append(btn)

    # Action bar (Quit & Play Again)
    action_frame = tk.Frame(outer, bg=BG_DARK)
    action_frame.pack(pady=6)

    tk.Button(
        action_frame,
        text="QUIT",
        font=FONT_SMALL,
        bg="#2b0938",
        fg="#ff6b6b",
        activebackground="#ff6b6b",
        activeforeground=BG_DARK,
        bd=2,
        relief=tk.RAISED,
        width=11,
        pady=4,
        command=self.quit_game,
    ).pack(side=tk.LEFT, padx=8)

    tk.Button(
        action_frame,
        text="PLAY AGAIN",
        font=FONT_SMALL,
        bg="#082f2b",
        fg=NEON_LIME,
        activebackground=NEON_LIME,
        activeforeground=BG_DARK,
        bd=2,
        relief=tk.RAISED,
        width=12,
        pady=4,
        command=self.reset_game,
    ).pack(side=tk.LEFT, padx=8)

  def play_round(self, user: str):
    if self.game_over:
      return

    user = user.strip().lower()
    if user not in CHOICES:
      self.result_label.config(text="Invalid choice. Try again.", fg=NEON_PINK)
      return

    self.round_num += 1
    comp = random.choice(CHOICES)
    self.comp_label.config(text=f"CPU PICK: [ {comp.upper()} ]")

    if user == comp:
      round_msg = "It's a tie!"
      color = NEON_YELLOW
    elif (
        (user == "rock" and comp == "scissors")
        or (user == "paper" and comp == "rock")
        or (user == "scissors" and comp == "paper")
    ):
      round_msg = "You win this round!"
      self.user_score += 1
      color = NEON_LIME
    else:
      round_msg = "Computer wins this round!"
      self.comp_score += 1
      color = NEON_PINK

    self.round_label.config(text=f"ROUND: {self.round_num:02d}")
    self.result_label.config(text=round_msg, fg=color)
    self.score_label.config(
        text=f"Score -> You: {self.user_score} | Computer: {self.comp_score}"
    )

  def quit_game(self):
    self.game_over = True
    for btn in self.choice_buttons:
      btn.config(state=tk.DISABLED)
    self.result_label.config(text="GAME OVER — Thanks for playing!", fg=NEON_CYAN)
    self.score_label.config(
        text=f"Final Score -> You: {self.user_score} | Computer: {self.comp_score}"
    )

  def reset_game(self):
    self.user_score = 0
    self.comp_score = 0
    self.round_num = 0
    self.game_over = False
    for btn in self.choice_buttons:
      btn.config(state=tk.NORMAL)
    self.round_label.config(text="ROUND: 00")
    self.comp_label.config(text="CPU PICK: [ --- ]")
    self.result_label.config(
        text="PICK ROCK, PAPER, OR SCISSORS!", fg=NEON_YELLOW
    )
    self.score_label.config(
        text=f"Score -> You: {self.user_score} | Computer: {self.comp_score}"
    )


if __name__ == "__main__":
  root = tk.Tk()
  app = RPSGuiGame(root)
  root.mainloop()

[simplecalc-tk.py](https://github.com/user-attachments/files/35183661/simplecalc-tk.py)
import tkinter as tk

# Light Purple / Lilac Palette
BG_LILAC = "#f3e8ff"       # Soft light purple background
BG_CARD = "#faf5ff"        # Whisper-light lilac surface
BG_SCREEN = "#e9d5ff"      # Pastel lavender display
BTN_NUM = "#ffffff"        # Crisp white-lilac number keys
BTN_OP = "#ddd6fe"         # Soft iris operator keys
BTN_EQ = "#c084fc"         # Vibrant light-purple equals key
BTN_CLEAR = "#f5d0fe"      # Soft orchid clear key
TEXT_PLUM = "#2e1065"      # Deep plum ink for crisp contrast
TEXT_MUTED = "#6b21a8"     # Muted grape accent

FONT_TITLE = ("Segoe UI", 14, "bold")
FONT_SCREEN = ("Consolas", 20, "bold")
FONT_SUB = ("Consolas", 9)
FONT_KEY = ("Segoe UI", 12, "bold")
FONT_OP = ("Segoe UI", 11, "bold")
```
<img width="346" height="502" alt="Screenshot 2026-11-20 225119" src="https://github.com/user-attachments/assets/35d782f8-7bfa-400d-8f72-88b55e9a4498" />
```                                                              

class SimpleLilacCalculator:

  def __init__(self, root: tk.Tk):
    self.root = root
    self.root.title("Simple Calc · Lilac Edition")
    self.root.geometry("330x460")
    self.root.resizable(False, False)
    self.root.configure(bg=BG_LILAC)

    self.current_input = ""
    self.first_number = None
    self.operator = None
    self.just_calculated = False

    outer = tk.Frame(root, bg=BG_LILAC, padx=14, pady=12)
    outer.pack(fill=tk.BOTH, expand=True)

    # Header
    tk.Label(
        outer,
        text="Simple Calculator",
        font=FONT_TITLE,
        bg=BG_LILAC,
        fg=TEXT_PLUM,
    ).pack(anchor="w", pady=(0, 6))

    # Display Card
    screen_box = tk.Frame(
        outer,
        bg=BG_SCREEN,
        highlightbackground="#d8b4fe",
        highlightthickness=2,
        padx=12,
        pady=10,
    )
    screen_box.pack(fill=tk.X, pady=(0, 12))

    self.expr_label = tk.Label(
        screen_box,
        text="",
        font=FONT_SUB,
        bg=BG_SCREEN,
        fg=TEXT_MUTED,
        anchor="e",
    )
    self.expr_label.pack(fill=tk.X)

    self.display_label = tk.Label(
        screen_box,
        text="0",
        font=FONT_SCREEN,
        bg=BG_SCREEN,
        fg=TEXT_PLUM,
        anchor="e",
    )
    self.display_label.pack(fill=tk.X, pady=(2, 0))

    # Keypad Grid
    keypad = tk.Frame(outer, bg=BG_LILAC)
    keypad.pack(fill=tk.BOTH, expand=True)

    for r in range(5):
      keypad.rowconfigure(r, weight=1, uniform="row")
    for c in range(4):
      keypad.columnconfigure(c, weight=1, uniform="col")

    buttons = [
        ("C", 0, 0, BTN_CLEAR, TEXT_PLUM, self.clear_all),
        ("⌫", 0, 1, BTN_OP, TEXT_PLUM, self.backspace),
        ("%", 0, 2, BTN_OP, TEXT_MUTED, lambda: self.choose_op("%")),
        ("/", 0, 3, BTN_OP, TEXT_MUTED, lambda: self.choose_op("/")),
        ("7", 1, 0, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("7")),
        ("8", 1, 1, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("8")),
        ("9", 1, 2, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("9")),
        ("*", 1, 3, BTN_OP, TEXT_MUTED, lambda: self.choose_op("*")),
        ("4", 2, 0, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("4")),
        ("5", 2, 1, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("5")),
        ("6", 2, 2, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("6")),
        ("-", 2, 3, BTN_OP, TEXT_MUTED, lambda: self.choose_op("-")),
        ("1", 3, 0, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("1")),
        ("2", 3, 1, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("2")),
        ("3", 3, 2, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("3")),
        ("+", 3, 3, BTN_OP, TEXT_MUTED, lambda: self.choose_op("+")),
        ("0", 4, 0, BTN_NUM, TEXT_PLUM, lambda: self.press_digit("0")),
        (".", 4, 1, BTN_NUM, TEXT_PLUM, self.press_dot),
        ("=", 4, 2, BTN_EQ, "#ffffff", self.calculate),
    ]

    for text, row, col, bg_col, fg_col, cmd in buttons:
      colspan = 2 if text == "=" else 1
      btn = tk.Button(
          keypad,
          text=text,
          font=FONT_OP if text in ("C", "⌫", "+", "-", "*", "/", "%", "=") else FONT_KEY,
          bg=bg_col,
          fg=fg_col,
          activebackground="#c084fc" if text != "=" else "#9333ea",
          activeforeground="#ffffff",
          bd=0,
          relief=tk.FLAT,
          cursor="hand2",
          command=cmd,
      )
      btn.grid(row=row, column=col, columnspan=colspan, sticky="nsew", padx=3, pady=3)

  def _format_num(self, num: float) -> str:
    return f"{num:g}"

  def press_digit(self, digit: str):
    if self.just_calculated:
      self.current_input = ""
      self.just_calculated = False
    if digit == "0" and self.current_input == "0":
      return
    self.current_input += digit
    self.display_label.config(text=self.current_input)

  def press_dot(self):
    if self.just_calculated:
      self.current_input = "0."
      self.just_calculated = False
      self.display_label.config(text=self.current_input)
      return
    if not self.current_input:
      self.current_input = "0."
    elif "." not in self.current_input:
      self.current_input += "."
    self.display_label.config(text=self.current_input)

  def backspace(self):
    if self.just_calculated or not self.current_input:
      return
    self.current_input = self.current_input[:-1]
    self.display_label.config(text=self.current_input if self.current_input else "0")

  def clear_all(self):
    self.current_input = ""
    self.first_number = None
    self.operator = None
    self.just_calculated = False
    self.expr_label.config(text="")
    self.display_label.config(text="0")

  def choose_op(self, op: str):
    if self.current_input:
      num = float(self.current_input)
      if self.first_number is not None and self.operator is not None and not self.just_calculated:
        self.calculate(next_op=op)
        return
      self.first_number = num
      self.current_input = ""
    elif self.first_number is None:
      self.first_number = 0.0

    self.operator = op
    self.just_calculated = False
    self.expr_label.config(text=f"{self._format_num(self.first_number)} {op}")
    self.display_label.config(text="0")

  def calculate(self, next_op: str | None = None):
    if self.first_number is None or self.operator is None:
      return

    second_number = float(self.current_input) if self.current_input else self.first_number
    op = self.operator

    try:
      if op == "+":
        result = self.first_number + second_number
      elif op == "-":
        result = self.first_number - second_number
      elif op == "*":
        result = self.first_number * second_number
      elif op == "/":
        if second_number == 0:
          raise ZeroDivisionError("Cannot divide by zero")
        result = self.first_number / second_number
      elif op == "%":
        if second_number == 0:
          raise ZeroDivisionError("Cannot modulo by zero")
        result = self.first_number % second_number
      else:
        return
    except ZeroDivisionError:
      self.clear_all()
      self.expr_label.config(text="Error: Divide by 0")
      self.display_label.config(text="Oops!")
      return

    formatted = self._format_num(result)
    if next_op:
      self.first_number = result
      self.operator = next_op
      self.current_input = ""
      self.just_calculated = False
      self.expr_label.config(text=f"{formatted} {next_op}")
      self.display_label.config(text=formatted)
    else:
      self.expr_label.config(text=f"{self._format_num(self.first_number)} {op} {self._format_num(second_number)} =")
      self.display_label.config(text=formatted)
      self.first_number = result
      self.current_input = ""
      self.operator = None
      self.just_calculated = True
<img width="437" height="439" alt="Screenshot 2026-11-20 223809" src="https://github.com/user-attachments/assets/c127470e-0a89-4e46-b292-8694620d943e" />

