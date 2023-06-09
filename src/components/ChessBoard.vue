<template>
    <div class="relative grid grid-cols-{{board.length}} border-2 border-gray-800 m-4 max-w-max">
        <div v-if="!hasLegalMoves" class="absolute inset-0 flex items-center justify-center bg-white bg-opacity-25">
            <div class="p-5 rounded-md shadow-xl text-center font-bold text-blue-800 opacity-100 bg-blue-200 border select-none">
                <p class="font-normal text-gray-800">
                    Congratulations! You made <span class="text-lg font-bold text-gray-600">{{ score }}</span> moves!
                </p>
                <p class="py-4">Game Over! There are no more legal moves!</p>
                <p class="text-xs font-normal text-black">P.S. The maximum number of possible moves is 64.</p>

                <button @click="resetGame" class="mt-4 bg-blue-500 hover:bg-blue-700 font-normal text-sm text-white py-2 px-4 rounded">Start Over!</button>
                <button @click="copyBoardState" class="mt-4 ml-2 bg-blue-500 hover:bg-blue-700 font-normal text-sm text-white py-2 px-4 rounded">Share</button>
            </div>
        </div>
        <div v-for="(row, rowIndex) in board" :key="rowIndex" class="flex flex-row">
            <div v-for="(square, columnIndex) in row" :key="columnIndex" class="flex flex-col">
                <div
                    @click="handleSquareClick(rowIndex, columnIndex)"
                    class="w-24 h-24 flex items-center justify-center select-none border border-gray-400 text-3xl cursor-pointer"
                    :class="squareClass(rowIndex, columnIndex)"
                >
                    {{ square !== 0 ? square : "" }}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "ChessBoard",
    data() {
        return {
            board: [],
            previousMove: null,
        };
    },
    mounted() {
        this.board = this.initBoard();
    },
    methods: {
        initBoard() {
            const board = [];
            for (let i = 0; i < 8; i++) {
                const row = [];
                for (let j = 0; j < 8; j++) {
                    row.push(0);
                }
                board.push(row);
            }
            return board;
        },
        handleSquareClick(rowIndex, columnIndex) {
            const clickedSquareValue = this.board[rowIndex][columnIndex];

            if (clickedSquareValue !== 0) {
                return;
            }

            if (this.previousMove === null) {
                this.previousMove = [rowIndex, columnIndex];
                this.board[rowIndex][columnIndex] = 1;
                return;
            } else {
                // Calculate the valid knight move based on the previous move
                const [prevRow, prevCol] = this.previousMove;
                const rowDiff = Math.abs(rowIndex - prevRow);
                const colDiff = Math.abs(columnIndex - prevCol);

                if ((rowDiff === 2 && colDiff === 1) || (rowDiff === 1 && colDiff === 2)) {
                    // Valid knight move, update the square value
                    this.board[rowIndex][columnIndex] = this.score + 1;
                    this.previousMove = [rowIndex, columnIndex];
                    return;
                } else {
                    // Invalid move, do nothing
                    return;
                }
            }
        },
        printSquareColor(rowIndex, columnIndex) {
            if (this.previousMove !== null) {
                const [prevRow, prevCol] = this.previousMove;
                if (rowIndex === prevRow && columnIndex === prevCol) {
                    return "bg-green-800 text-white";
                }
            }

            if (this.board[rowIndex][columnIndex] === 0) {
                if ((rowIndex + columnIndex) % 2 === 0) {
                    return "bg-white hover:bg-gray-300";
                } else {
                    return "bg-yellow-900 hover:bg-yellow-700";
                }
            } else {
                return "bg-green-500 text-white";
            }
        },
        squareClass(rowIndex, columnIndex) {
            return [
                "w-24",
                "h-24",
                "flex",
                "items-center",
                "justify-center",
                "select-none",
                "border",
                "border-gray-400",
                "text-3xl",
                "cursor-pointer",
                this.printSquareColor(rowIndex, columnIndex),
            ];
        },
        resetGame() {
            this.board = this.initBoard();
            this.previousMove = null;
        },
        copyBoardState() {
            const boardState = this.formatTableForClipboard();
            navigator.clipboard
                .writeText(boardState)
                .then(() => {
                    alert("Board state copied to clipboard!");
                })
                .catch((error) => {
                    console.error("Failed to copy board state:", error);
                });
        },
        formatTableForClipboard() {
            const formattedBoardState = JSON.stringify(this.board)
                .replace(/\],\[/g, "\n")
                .replace(/\[/g, "")
                .replace(/\]/g, "")
                .replace(/,/g, "\t")
                .replace(/"/g, "")
                .replace(/\b(\d)\b/g, " $1") // Add a space in front of single digit numbers
                .replace(/\b(\d{2})\b/g, "$1 "); // Add a space after double digit numbers

            return formattedBoardState;
        },
    },
    computed: {
        hasLegalMoves() {
            if (this.previousMove === null) {
                // First move, always legal
                return true;
            }

            const [prevRow, prevCol] = this.previousMove;
            const possibleMoves = [
                [prevRow - 2, prevCol - 1],
                [prevRow - 2, prevCol + 1],
                [prevRow - 1, prevCol - 2],
                [prevRow - 1, prevCol + 2],
                [prevRow + 1, prevCol - 2],
                [prevRow + 1, prevCol + 2],
                [prevRow + 2, prevCol - 1],
                [prevRow + 2, prevCol + 1],
            ];

            for (const [row, col] of possibleMoves) {
                if (row >= 0 && row < 8 && col >= 0 && col < 8 && this.board[row][col] === 0) {
                    // At least one legal move is available
                    return true;
                }
            }

            return false;
        },
        score() {
            return Math.max(...this.board.flat()) ?? 0;
        },
    },
};
</script>
