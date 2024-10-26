Function Get Safe King Moves
```bash
function getSafeKingMoves(kingPosition, board, currentPlayerColor) {
    let possibleMoves = [];
    let notPossibleMoves = [];
    const directions = [
        { row: -1, col: 0 },  // Up
        { row: 1, col: 0 },   // Down
        { row: 0, col: -1 },  // Left
        { row: 0, col: 1 },   // Right
        { row: -1, col: -1 }, // Up-Left
        { row: -1, col: 1 },  // Up-Right
        { row: 1, col: -1 },  // Down-Left
        { row: 1, col: 1 }    // Down-Right
    ];

    const kingRow = kingPosition.row;
    const kingCol = kingPosition.col;

// Assuming directions is defined and contains the potential moves for the king
    directions.forEach(direction => {
        const newRow = kingRow + direction.row;
        const newCol = kingCol + direction.col;
        // Ensure the move is within board bounds
        if (newRow >= 0 && newRow < 8 && newCol >= 0 && newCol < 8) {
            const targetSquare = board[newRow * 8 + newCol];

            // Make sure the square is either empty or contains an opponent's piece
            if (!targetSquare.img || targetSquare.pieceColor !== currentPlayerColor) {
                // Create a temporary board
                const tempBoard = createTempBoard(board);
                console.log(tempBoard)
                const originalSquare = tempBoard[kingRow * 8 + kingCol];
                const tempSquare = tempBoard[newRow * 8 + newCol];

                // Store the original state of both squares to restore later
                const originalImg = originalSquare.img;
                const targetImg = tempSquare.img;

                // Move king to new square
                tempSquare.img = originalImg; 

                let underCheck = false;  // Reset underCheck before checking

                for (let row = 0; row < 8; row++) {
                    for (let col = 0; col < 8; col++) {
                        const piece = tempBoard[row * 8 + col];

                        if (piece && piece.pieceColor === 'black' && piece.img !== '') {
                            let piece1 = piece.img.toLowerCase();
                            console.warn(piece1, row, col);
                            console.log("Testing piece:", piece1, "at", { row, col }, "against king at", { row: originalSquare.row, col: originalSquare.col });
                            underCheck = canPieceAttackKing(piece1, { row, col }, { row: originalSquare.row, col: originalSquare.col }, tempBoard);
                            console.log(underCheck);
                            if (underCheck) {
                                possibleMoves.push({ row: newRow, col: newCol });
                                break; // Exit inner loop
                            } else {
                                console.log(notPossibleMoves.push({ row: newRow, col: newCol }));
                            }
                        }
                    }
                    if (underCheck) {
                        break; // Exit outer loop if king is in check
                    }
                }

                // Restore the original state of the squares
                originalSquare.img = originalImg;  // Return king to original square
                tempSquare.img = targetImg;  // Restore the original state of the target square

                // If the king is not under check at the new position, it's a valid move
                if (!underCheck) {
                    console.log("Valid king move:", { row: newRow, col: newCol });
                }
            }
        }
    });

    console.log('Possible Moves', possibleMoves);
    return possibleMoves;
}
```
The function canPieceAttackKing called as
```bash
underCheck = canPieceAttackKing(piece1, { row, col }, { row: originalSquare.row, col: originalSquare.col }, tempBoard);
```
definition of canPieceAttackKing
```bash
function canPieceAttackKing(piece, pieceRow, pieceCol, kingPosition, board) {
    const kingRow = kingPosition.row;
    const kingCol = kingPosition.col;
    switch (piece) {
        case 'q': // Queen
            return canRookAttack(pieceRow, pieceCol, kingRow, kingCol, board, kingPosition) ||
                   canBishopAttack(pieceRow, pieceCol, kingRow, kingCol, board, kingPosition);
        case 'r': // Rook
            return canRookAttack(pieceRow, pieceCol, kingRow, kingCol, board, kingPosition);
        case 'b': // Bishop
            return canBishopAttack(pieceRow, pieceCol, kingRow, kingCol, board, kingPosition);
        case 'n': // Knight
            return canKnightAttack(pieceRow, pieceCol, kingRow, kingCol);
        case 'p': // Pawn
            return canPawnAttack(piece, pieceRow, pieceCol, kingRow, kingCol);
        case 'k': // King
            return canKingAttack(pieceRow, pieceCol, kingRow, kingCol);
        default:
            return false;
    }
}
```
When we call this way
```bash
// Pawn attack logic (diagonal attack for black pawns)
function canPawnAttack(pawn, pieceRow, pieceCol, kingRow, kingCol) {
    return pawn === 'p' && pieceRow + 1 === kingRow && Math.abs(pieceCol - kingCol) === 1;
}
```
But when we call
```bash
console.log("Pawn Attack Test:", canPawnAttack('p', 4, 3, 5, 4)); // Expected output: true
```
This returns true

This is console.log of the calling of canPawnAttack
```bash
chessTest1.html:1310 p 4 3

chessTest1.html:1311 Testing piece: p at {row: 4, col: 3}
 against king at {row: 5, col: 4}
chessTest1.html:1313 false
chessTest1.html:1318 96
chessTest1.html:1333 Valid king move: 
{row: 6, col: 3}
chessTest1.html:1339 Possible Moves []
```
