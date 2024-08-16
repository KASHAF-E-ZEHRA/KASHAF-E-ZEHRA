TASK 1 :Develop a simple Tic-Tac-Toe game where the computer plays against theuser using basic algorithms like minimax.

#include <iostream>
#include <vector>
#include <limits>

using namespace std;

const char PLAYER = 'X';
const char COMPUTER = 'O';
const char EMPTY = ' ';

void printBoard(const vector<vector<char>>& board) {
    for (const auto& row : board) {
        for (const auto& cell : row) {
            cout << (cell == EMPTY ? '.' : cell) << " ";
        }
        cout << endl;
    }
}

bool isMovesLeft(const vector<vector<char>>& board) {
    for (const auto& row : board) {
        for (const auto& cell : row) {
            if (cell == EMPTY) {
                return true;
            }
        }
    }
    return false;
}

int evaluate(const vector<vector<char>>& board) {
    for (int row = 0; row < 3; ++row) {
        if (board[row][0] == board[row][1] && board[row][1] == board[row][2]) {
            if (board[row][0] == PLAYER) return -10;
            else if (board[row][0] == COMPUTER) return 10;
        }
    }

    for (int col = 0; col < 3; ++col) {
        if (board[0][col] == board[1][col] && board[1][col] == board[2][col]) {
            if (board[0][col] == PLAYER) return -10;
            else if (board[0][col] == COMPUTER) return 10;
        }
    }

    if (board[0][0] == board[1][1] && board[1][1] == board[2][2]) {
        if (board[0][0] == PLAYER) return -10;
        else if (board[0][0] == COMPUTER) return 10;
    }

    if (board[0][2] == board[1][1] && board[1][1] == board[2][0]) {
        if (board[0][2] == PLAYER) return -10;
        else if (board[0][2] == COMPUTER) return 10;
    }

    return 0;
}

int minimax(vector<vector<char>>& board, int depth, bool isMax) {
    int score = evaluate(board);

    if (score == 10) return score;
    if (score == -10) return score;
    if (!isMovesLeft(board)) return 0;

    if (isMax) {
        int best = numeric_limits<int>::min();

        for (int i = 0; i < 3; ++i) {
            for (int j = 0; j < 3; ++j) {
                if (board[i][j] == EMPTY) {
                    board[i][j] = COMPUTER;
                    best = max(best, minimax(board, depth + 1, !isMax));
                    board[i][j] = EMPTY;
                }
            }
        }
        return best;
    } else {
        int best = numeric_limits<int>::max();

        for (int i = 0; i < 3; ++i) {
            for (int j = 0; j < 3; ++j) {
                if (board[i][j] == EMPTY) {
                    board[i][j] = PLAYER;
                    best = min(best, minimax(board, depth + 1, !isMax));
                    board[i][j] = EMPTY;
                }
            }
        }
        return best;
    }
}

pair<int, int> findBestMove(vector<vector<char>>& board) {
    int bestVal = numeric_limits<int>::min();
    pair<int, int> bestMove = {-1, -1};

    for (int i = 0; i < 3; ++i) {
        for (int j = 0; j < 3; ++j) {
            if (board[i][j] == EMPTY) {
                board[i][j] = COMPUTER;
                int moveVal = minimax(board, 0, false);
                board[i][j] = EMPTY;

                if (moveVal > bestVal) {
                    bestMove = {i, j};
                    bestVal = moveVal;
                }
            }
        }
    }
    return bestMove;
}

int main() {
    vector<vector<char>> board(3, vector<char>(3, EMPTY));
    int row, col;

    while (true) {
        printBoard(board);

        cout << "Enter your move (row and column): ";
        cin >> row >> col;

        if (board[row][col] != EMPTY) {
            cout << "Cell already occupied! Try again." << endl;
            continue;
        }

        board[row][col] = PLAYER;

        if (evaluate(board) == -10) {
            printBoard(board);
            cout << "You win!" << endl;
            break;
        }

        if (!isMovesLeft(board)) {
            printBoard(board);
            cout << "It's a draw!" << endl;
            break;
        }

        auto bestMove = findBestMove(board);
        board[bestMove.first][bestMove.second] = COMPUTER;

        if (evaluate(board) == 10) {
            printBoard(board);
            cout << "Computer wins!" << endl;
            break;
        }

        if (!isMovesLeft(board)) {
            printBoard(board);
            cout << "It's a draw!" << endl;
            break;
        }
    }

    return 0;
}

