c++实现的俄罗斯方块，基于ncurse渲染
===

## 完整代码
```c
#include <ctime>
#include <curses.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>


// block[7][4] 中设置了7种方块和4种旋转状态
// 每个旋转类型的方块使用整数编码，其中包括宽度，长度，每个小块的相对坐标（每种方块都是由4个小块组成）
// 宽度和长度之所以-1，是为了使用2bit保存
// block layout is: {w-1,h-1}{x0,y0}{x1,y1}{x2,y2}{x3,y3} (two bits each)
// x,y 坐标；px,py 方块上次位置；r 旋转类型；c 用户输入；p 方块类型；tick 方块自动下落阈值（到达阈值方块会下落一格）
// board[20][10] 设置一个20 x 10 的游戏界面
// score 分数，每清除一行会增加
int x = 431424, y = 598356, r = 427089, px = 247872, py = 799248, pr,
    c = 348480, p = 615696, tick, board[20][10],
    block[7][4] = {{x, y, x, y},
                   {r, p, r, p},
                   {c, c, c, c},
                   {599636, 431376, 598336, 432192},
                   {411985, 610832, 415808, 595540},
                   {px, py, px, py},
                   {614928, 399424, 615744, 428369}},
    score = 0;

// 解码block[7][4]中的方块，获取坐标
// extract a 2-bit number from a block entry
int NUM(int x, int y) { return 3 & block[p][x] >> y; }

// create a new piece, don't remove old one (it has landed and should stick)
void new_piece() {
  y = py = 0;  // 纵坐标清零
  p = rand() % 7;  // 随机获取一种方块
  r = pr = rand() % 4;  // 随意获取方块的旋转类型
  x = px = rand() % (10 - NUM(r, 16));  // 随机生成方块的初始横坐标
}

// draw the board and score
void frame() {
  for (int i = 0; i < 20; i++) {
    move(1 + i, 1); // otherwise the box won't draw
    for (int j = 0; j < 10; j++) {
      board[i][j] && attron(262176 | board[i][j] << 8);
      printw("  ");
      attroff(262176 | board[i][j] << 8);
    }
  }
  move(21, 1);
  printw("Score: %d", score);
  refresh();
}

// set the value fo the board for a particular (x,y,r) piece
void set_piece(int x, int y, int r, int v) {
  for (int i = 0; i < 8; i += 2) {
    board[NUM(r, i * 2) + y][NUM(r, (i * 2) + 2) + x] = v;
  }
}

// move a piece from old (p*) coords to new
int update_piece() {
  set_piece(px, py, pr, 0);
  set_piece(px = x, py = y, pr = r, p + 1);
}

// remove line(s) from the board if they're full
void remove_line() {
  for (int row = y; row <= y + NUM(r, 18); row++) {
    c = 1;
    for (int i = 0; i < 10; i++) {
      c *= board[row][i];
    }
    if (!c) {
      continue;
    }
    for (int i = row - 1; i > 0; i--) {
      memcpy(&board[i + 1][0], &board[i][0], 40);
    }
    memset(&board[0][0], 0, 10);
    score++;
  }
}

// check if placing p at (x,y,r) will be a collision
int check_hit(int x, int y, int r) {
  if (y + NUM(r, 18) > 19) {
    return 1;
  }
  set_piece(px, py, pr, 0);
  c = 0;
  for (int i = 0; i < 8; i += 2) {
    board[y + NUM(r, i * 2)][x + NUM(r, (i * 2) + 2)] && c++;
  }
  set_piece(px, py, pr, p + 1);
  return c;
}

// slowly tick the piece y position down so the piece falls
int do_tick() {
  if (++tick > 30) {
    tick = 0;
    if (check_hit(x, y + 1, r)) {
      if (!y) {
        return 0;
      }
      remove_line();
      new_piece();
    } else {
      y++;
      update_piece();
    }
  }
  return 1;
}

// main game loop with wasd input checking
void runloop() {
  while (do_tick()) {
    usleep(10000);
    if ((c = getch()) == 'a' && x > 0 && !check_hit(x - 1, y, r)) {
      x--;
    }
    if (c == 'd' && x + NUM(r, 16) < 9 && !check_hit(x + 1, y, r)) {
      x++;
    }
    if (c == 's') {
      while (!check_hit(x, y + 1, r)) {
        y++;
        update_piece();
      }
      remove_line();
      new_piece();
    }
    if (c == 'w') {
      ++r %= 4;
      while (x + NUM(r, 16) > 9) {
        x--;
      }
      if (check_hit(x, y, r)) {
        x = px;
        r = pr;
      }
    }
    if (c == 'q') {
      return;
    }
    update_piece();
    frame();
  }
}

// init curses and start runloop
int main() {
  srand(time(0));
  initscr();
  start_color();
  // colours indexed by their position in the block
  for (int i = 1; i < 8; i++) {
    init_pair(i, i, 0);
  }
  new_piece();
  resizeterm(22, 22);
  noecho();
  timeout(0);
  curs_set(0);
  box(stdscr, 0, 0);
  runloop();
  endwin();
}
```

## 如何编译

```
g++-11 -g -O0 -Wall tinytetris-commented.cpp -lncurses -o tinytetris_debug
```