---
layout: project
title: "Chess Bevy"
description: "A chess gui, written using a custom chess library which uses bitboards to represent the board state."
image: /assets/images/chess_bevy.png
show-image: true
link: https://github.com/tmforshaw/ChessBevy
nav-menu: true
date: 06/07/2025
tools:
  - Rust
  - Bevy
  - UCI
---

<h2><span><a href="https://github.com/tmforshaw/ChessBevy">Chess GUI</a></span></h2>

A chess GUI written using a custom-built chess library, <code>chess_core</code>, which intended for future use in a chess engine.
The GUI currently uses <code>stockfish</code> as its chess engine, communicating via <strong>UCI</strong> to get the best move for the black pieces to make.
The engine provides information about moves, and using this, the GUI can classify moves into categories (Best Move, Excellent, Good, Mistake, Blunder).
An evaluation bar is also provided via <code>stockfish</code> and updates after each move.

The GUI, via the chess library, is aware of legal moves, so it prevents moving pieces to illegal positions.
Possible moves for each piece are shown when the piece is picked up, and the user can also undo/redo moves since move history is implemented in the chess library.
The GUI shows an indicator for the last move which was made, making it clearer what is happening.

Promotion is mostly implemented; currently, pawns can only promote to queens.
En passant, and castling are fully implemented, and all moves work with the move history feature, including remembering the state of castling rights and the en passant square.

The board is created using a <strong>FEN</strong> starting position, and internally represented via bitboards.
There are keybinds to show the piece bitboards, the attacked squares for each player, as well as the en passant square.

<hr class="major">

<h2><span><a href="https://github.com/tmforshaw/ChessCore">Chess Core</a></span></h2>

<h3>Board</h3>

There is a <code>Board</code> struct which keeps track of the board state, along with the move history.
<code>boards</code> is an array of bitboards for each piece type, meaning that there are twelve <code>u64</code> values representing the positions.
Each bitboard can be manipulated using bitwise operations to get or set certain positions, as well as more complicated tests like finding if the space between two positions is empty.

<pre><code>struct Board {
  boards: [BitBoard; PIECE_AMT * COLOUR_AMT],
  en_passant_tile: u64,
  castling_rights: [(bool, bool); COLOUR_AMT],
  player: Player,
  half_move_counter: usize,
  full_move_counter: usize,
  move_history: PieceMoveHistory,
}</code></pre>

<h3>Move History</h3>

When adding moves to <code>move_history</code>, there is a check to see if this move matches the next move in the history, and if it is, the history is cleared before adding pushing move; if the move is the same as the current <code>HistoryMove</code>, the index is simply incremented.

<pre><code>fn make_move(
    &amp;mut self,
    piece_move: PieceMove,
    captured_piece: Option&lt;Piece&gt;,
    en_passant_tile: Option&lt;TilePos&gt;,
    castling_rights: [(bool, bool); COLOUR_AMT],
) {
    if piece_move.show {
        // Clear history depending on where current_idx is (if the move is different from the history)
        if let Some(current_idx) = self.current_idx {
            // If the suggested move is different to the current move in history, and is not the last move in the history
            if piece_move != self.moves[current_idx].piece_move &amp;&amp; current_idx + 1 &lt; self.moves.len() {
                self.clear_excess_moves();
            }
        } else if !self.moves.is_empty() {
            self.clear_excess_moves();
        }

        self.moves
            .push(HistoryMove::new(piece_move, captured_piece, en_passant_tile, castling_rights));
        let _ = self.increment_index();
    }
}</code></pre>
