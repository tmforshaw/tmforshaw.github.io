---
layout: project
title: "Chess Bevy"
description: "A chess gui, written using a custom chess library."
image: /assets/images/chess_bevy.png
github: https://github.com/tmforshaw/ChessBevy
nav-menu: true
---

A chess GUI written using a custom-built chess library which intended for future use in a chess engine.
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
