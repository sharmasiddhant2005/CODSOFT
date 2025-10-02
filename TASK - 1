"""

Tic-Tac-Toe (player vs computer)
-----------------------------------
- Pure Python (no external libraries)
- Minimax with Alpha-Beta pruning
- Depth-aware scoring (earlier wins are preferred)
- player can choose X or O, and who goes first
- Clean CLI with input validation

Run:
    python tictactoe_unbetableai.py
"""

import math
import random
from typing import List, Optional, Tuple

Position = int  # 0..8
Board = List[str]  # "X", "O", or " "


def new_board() -> Board:
    return [" "] * 9


def print_board(b: Board) -> None:
    def row(i):
        return f" {b[i]} | {b[i+1]} | {b[i+2]} "
    sep = "\n---+---+---\n"
    print("\n" + row(0) + sep + row(3) + sep + row(6) + "\n")


def available_moves(b: Board) -> List[Position]:
    return [i for i, v in enumerate(b) if v == " "]


def check_winner(b: Board) -> Optional[str]:
    lines = [
        (0, 1, 2), (3, 4, 5), (6, 7, 8),  # rows
        (0, 3, 6), (1, 4, 7), (2, 5, 8),  # columns
        (0, 4, 8), (2, 4, 6)              # diagonals
    ]
    for a, c, d in lines:
        if b[a] != " " and b[a] == b[c] == b[d]:
            return b[a]
    return None


def is_draw(b: Board) -> bool:
    return check_winner(b) is None and all(v != " " for v in b)


def minimax(b: Board, depth: int, is_max: bool, computer: str, player: str,
            alpha: float, beta: float) -> Tuple[int, Optional[Position]]:
    winner = check_winner(b)
    if winner == computer:
        # Prefer faster wins
        return 10 - depth, None
    elif winner == player:
        # Prefer delaying opponent wins
        return depth - 10, None
    elif is_draw(b):
        return 0, None

    best_move: Optional[Position] = None

    if is_max:
        best_score = -math.inf
        for move in available_moves(b):
            b[move] = computer
            score, _ = minimax(b, depth + 1, False, computer, player, alpha, beta)
            b[move] = " "
            if score > best_score:
                best_score, best_move = score, move
            alpha = max(alpha, best_score)
            if beta <= alpha:
                break
        return best_score, best_move
    else:
        best_score = math.inf
        for move in available_moves(b):
            b[move] = player
            score, _ = minimax(b, depth + 1, True, computer, player, alpha, beta)
            b[move] = " "
            if score < best_score:
                best_score, best_move = score, move
            beta = min(beta, best_score)
            if beta <= alpha:
                break
        return best_score, best_move


def get_best_move(b: Board, computer: str, player: str) -> Position:
    # Small optimization: if center is free, consider it first
    if b[4] == " ":
        # Check if winning in one move
        b[4] = computer
        if check_winner(b) == computer:
            b[4] = " "
            return 4
        b[4] = " "

    # Try to win in one move or block in one move before full minimax
    for player in (computer, player):
        for move in available_moves(b):
            b[move] = player
            if check_winner(b) == player:
                b[move] = " "
                return move
            b[move] = " "

    # Otherwise use minimax with alpha-beta
    _, move = minimax(b, 0, True, computer, player, -math.inf, math.inf)
    assert move is not None
    return move


def ask_choice(prompt: str, valid: List[str]) -> str:
    while True:
        ans = input(prompt).strip().upper()
        if ans in valid:
            return ans
        print(f"Please type one of: {', '.join(valid)}")


def ask_move(b: Board) -> Position:
    while True:
        try:
            raw = input("Enter your move (1-9, left→right, top→bottom): ").strip()
            i = int(raw) - 1
            if i < 0 or i > 8:
                print("Choose a number from 1 to 9.")
                continue
            if b[i] != " ":
                print("That spot is taken. Try again.")
                continue
            return i
        except ValueError:
            print("Please enter a valid number (1-9).")


def game_loop():
    print("\n==== Tic-Tac-Toe: Unbeatable AI ====\n")
    player = ask_choice("Choose your mark (X/O): ", ["X", "O"])
    computer = "O" if player == "X" else "X"

    first = ask_choice("Who plays first? (P for player / C for computer ): ", ["P", "C"])

    b = new_board()
    turn = "P" if first == "P" else "C"

    print("\nBoard index map:")
    print(" 1 | 2 | 3 \n---+---+---\n 4 | 5 | 6 \n---+---+---\n 7 | 8 | 9 \n")
    print("Let's play!\n")

    while True:
        print_board(b)

        if turn == "P":
            move = ask_move(b)
            b[move] = player
        else:
            print("computer is thinking...")
            move = get_best_move(b, computer, player)
            b[move] = computer
            print(f"computer plays at position {move + 1}")

        winner = check_winner(b)
        if winner:
            print_board(b)
            if winner == player:
                print("🎉 You win! (That was not supposed to happen!)")
            else:
                print("🤖 computer wins. Unbeatable remains unbeaten.")
            break

        if is_draw(b):
            print_board(b)
            print("It's a draw!")
            break

        turn = "C" if turn == "P" else "P"

    again = ask_choice("\nPlay again? (Y/N): ", ["Y", "N"])
    if again == "Y":
        print("\nRestarting...\n")
        game_loop()
    else:
        print("Thanks for playing! 👋")


if __name__ == "__main__":
    game_loop()
