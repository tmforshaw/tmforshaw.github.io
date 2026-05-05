---
layout: project
title: "Chess Bevy"
description: "A chess gui, written using a custom chess library which uses bitboards to represent the board state."
image: /assets/images/chess_bevy.png
show-image: true
link: https://github.com/tmforshaw/ChessBevy
nav-menu: true
date: 2026-03-06
tools:
  - Rust
  - Bevy
  - UCI
---
<!-- date: 06/07/2025 -->

<section>
<h2><span><a href="https://github.com/tmforshaw/ChessBevy">Chess GUI</a></span></h2>

<div>
    <h3>Module Summary</h3>
    <ul>
        <li>Built a desktop chess <strong>GUI</strong> using <code>Bevy</code>.</li>
        <li>Uses a custom chess engine library, <code>chess_core</code>, based on bitboards for game logic and state management.</li>
        <li>Designed as an interactive interface layer over a full rules-compliant chess core.</li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Technical Challenges</h3>
    <ul>
        <li>Managing asynchronous communication with <code>Stockfish</code> via the <code>UCI</code> protocol while keeping the GUI responsive.</li>
        <li>Parsing and synchronising real-time engine evaluation updates without desynchronising game state.</li>
        <li>Mapping raw engine evaluation scores into meaningful move quality categories (Best Move &rarr; Blunder).</li>
        <li>Maintaining strict synchronisation between GUI state and <code>chess_core</code> to prevent board desyncs.</li>
        <li>Handling complex chess rules (castling, en passant, promotion) without duplicating logic in the GUI layer.</li>
        <li>Preventing illegal user interactions while keeping the interface responsive.</li>
        <li>Ensuring legal-move highlighting stays consistent with engine-validated state per frame.</li>
        <li>Implementing undo/redo functionality with support for branching move paths.</li>
        <li>Preventing history corruption when users diverge from existing move timelines.</li>
        <li>Designing clear and non-intrusive visual feedback for moves and evaluations.</li>
        <li>Handling rapid user interactions (fast moves, undo spamming) without UI instability or flickering.</li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Engine Integration (<strong>UCI</strong>)</h3>
    <ul>
        <li>Integrated <code>Stockfish</code> via the <code>UCI</code> protocol for move analysis.</li>
        <li>Retrieves best moves and evaluation scores after each player move.</li>
        <li>Uses engine output to classify player moves by quality (Best Move &rarr; Blunder).</li>
        <li>Drives an evaluation bar that updates after every move.</li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Gameplay / Rules Environment</h3>
    <ul>
        <li>All moves are validated through the underlying chess engine (preventing illegal states in UI).</li>
        <li>Legal moves displayed when selecting a piece.</li>
        <li>Full Support for:
            <ul>
                <li>Castling (with rights tracking).</li>
                <li>En passant captures.</li>
                <li>Move history consistency across special moves.</li>
            </ul>
        </li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Move System</h3>
    <ul>
        <li>Undo and redo functionality implemented via engine-backed move history.</li>
        <li>Maintains consistent board state across navigation.</li>
        <li>Tracks and restores full game state per move (including special rule state).</li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>UI Features</h3>
    <ul>
        <li>Highlights last move played on the board.</li>
        <li>Shows legal move indicators for selected pieces.</li>
        <li>Displays engine evaluation bar in real-time.</li>
        <li>Shows move classification (from engine evaluation) on the last moved piece.</li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Board Initialisation</h3>
    <ul>
        <li>Board initialised using <strong>FEN</strong> strings.</li>
        <li>Debug overlays available:
            <ul>
                <li>Bitboard visualisation (Per-piece-per-player).</li>
                <li>Attack square visualisation.</li>
                <li>En passant square state.</li>
            </ul>
        </li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Current Limitations</h3>
    <ul>
        <li>Pawn promotion partially implemented: only allows promotion to queen currently.</li>
    </ul>
</div>
</section>

<hr class="major">

<section>
<h2><span><a href="https://github.com/tmforshaw/ChessCore">Chess Core</a></span></h2>

<div>
    <h3>Module Summary</h3>
    <ul>
        <li>Low-level chess engine library written in <code>Rust</code>.</li>
        <li>Designed for performance and future extensibility (e.g. full engine implementation).</li>
        <li>Uses bitboards as the primary representation for board state and move computation.</li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Technical Challenges</h3>
    <ul>
        <li>Designing a full chess board representation using only <code>u64</code> bitboards.</li>
        <li>Encoding 12 piece sets efficiently while keeping operations cache-friendly.</li>
        <li>Balancing low-level bitwise control with maintainable abstractions.</li>
        <li>Structuring the library for future extensibility (e.g. full engine integration).</li>
        <li>Implementing move generation using bitwise operations instead of per-tile iteration.</li>
        <li>Ensuring deterministic and consistent move generation across all board states.</li>
        <li>Building a move history system which supports undo/redo as well as branching timelines.</li>
        <li>Ensuring full reproducibility of game state from historical position.</li>
        <li>Handling divergence logic without corrupting or duplicating history.</li>
        <li>Synchronising all state components (castling rights, en passant, counters) across history traversal.</li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Board Representation</h3>
    <ul>
        <li>Board state represented using 12 bitboards (piece_type &times; player count).</li>
        <li>Each bitboard is stored as a <code>u64</code> for compact and fast operations.</li>
        <li>Supports fast bitwise operations for:
            <ul>
                <li>Move generation.</li>
                <li>Occupancy checks.</li>
                <li>Path clearance validation.</li>
            </ul>
        </li>
        <li>Central <code>Board</code> struct maintains full game state:
            <ul>
                <li>Piece bitboards.</li>
                <li>En passant square.</li>
                <li>Castling rights per player.</li>
                <li>Active player.</li>
                <li>Half-move and full-move counters.</li>
                <li>Move history system.</li>
            </ul>
        </li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Move Generation and State Logic</h3>
    <ul>
        <li>Move validation built around bitboard operations rather than per-tile iteration.</li>
        <li>Efficient checks for legal movement using bit masking.</li>
        <li>Supports special rule handling (castling, en passant) at engine level.</li>
        <li>Ensures UI and engine remain synchronised through strict state control.</li>
    </ul>
</div>

<hr class="minor">

<div>
    <h3>Move History System</h3>
    <ul>
        <li>Implements full move history tracking with support for:
            <ul>
                <li>Undo.</li>
                <li>Redo.</li>
                <li>Branching move paths.</li>
            </ul>
        </li>
        <li>Key design features:
            <ul>
                <li>Detects divergence from existing move timeline.</li>
                <li>Clears invalid future history when a new branch occurs.</li>
                <li>Maintains an index pointer into history for navigation.</li>
            </ul>
        </li>
        <li>Each history entry stores:
            <ul>
                <li>Move data.</li>
                <li>Captured piece (if any).</li>
                <li>En passant state at time of move.</li>
                <li>Castling rights snapshot.</li>
            </ul>
        </li>
        <li>Ensures:
            <ul>
                <li>Accurate rollback of full game state.</li>
                <li>Consistency of special rule state across history traversal.</li>
            </ul>
        </li>
    </ul>
</div>
</section>
