# VIT-SoftwareDev

Problem Statement:  
https://docs.google.com/document/d/16CNXNQ-VLLSoVe7CvJFBzueawgrCjWorB7zMLNvOF94/edit?usp=sharing



#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <algorithm>

using namespace std;

struct ChessBoard{
    enum class Turn {white, black} turn;
    enum class Piece {king, queen, white_pawn, black_pawn, rook, bishop, knight};
    static map<Piece,int> pieceValues;
    bool show_coordinates = false;

    struct Pos{
        int x,y;
        Pos(const Pos &p, int dx=0, int dy=0){ *this = p; x+=dx; y+=dy;}
        Pos(int _x, int _y){  x=_x; y=_y; }
        bool operator<(const Pos & p) const { return (x < p.x) || (x==p.x && y < p.y); }
        bool operator==(const Pos & p) const { return x==p.x && y==p.y; }
        Pos(){x=-1;y=-1;}
    };

    map<Pos,Piece> white_pieces, black_pieces;
    map<Pos,Piece> & moverPieces(){ return turn == Turn::white ? white_pieces : black_pieces; }
    map<Pos,Piece> & opponentPieces(){ return turn == Turn::white ? black_pieces : white_pieces; }

    void reset(){
        turn = Turn::white;
        white_pieces.clear();
        black_pieces.clear();
        for(int i=1; i < 9; ++i){
            white_pieces[Pos(i,7)]=Piece::white_pawn;
            black_pieces[Pos(i,2)]=Piece::black_pawn;
        }
        int n=1;
        for(auto piece : {Piece::rook,Piece::knight,Piece::bishop,Piece::king}){
            white_pieces[Pos(n,8)]=white_pieces[Pos(9-n,8)]=black_pieces[Pos(n,1)]=black_pieces[Pos(9-n,1)]=piece;
            ++n;
        }
        white_pieces[Pos(4,8)]=black_pieces[Pos(4,1)]=Piece::queen;
    }

    void flipTurn(){ turn = turn == Turn::white? Turn::black : Turn::white; }

    bool makeMove(Pos from, Pos to){
        vector<Pos> allowed = possibleMoves(from);
        if(find(allowed.begin(), allowed.end(), to) == allowed.end())
            return false;
        opponentPieces().erase(to);
        moverPieces()[to]=moverPieces()[from];
        moverPieces().erase(from);
        if((moverPieces()[to]==Piece::white_pawn || moverPieces()[to]==Piece::black_pawn) && (to.y == 1 || to.y == 8))
            moverPieces()[to]=Piece::queen;
        flipTurn();
        return true;
    }
