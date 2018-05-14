---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 对抗搜索 - 五子棋游戏对抗

## 代码实现

案例：一个 3×3 的井字棋游戏：

```python {class="line-numbers"}
import re
import copy
from collections import namedtuple

class PutChessError(Exception):
    pass

Action = namedtuple('Action', 'pos chess')

class TicTacToeBoard:
    def __init__(self, pre_state, action=None, win_cnt=None,
                machine_first=True):
        if win_cnt == None:
            self.win_cnt = len(pre_state)
        else:
            self.win_cnt = win_cnt
        self.state = copy.deepcopy(pre_state)
        self.sub_cases = None
        self.machine_first = machine_first
        if action!=None:
            self.executeAction(action)
            
    def executeAction(self, action):
        st_len = len(self.state)
        p0, p1 = action.pos
        if (p0<0 or p1<0 or p0>=st_len or p1>=st_len or
            self.state[p0][p1]!='-'):
            raise PutChessError()
        self.state[p0][p1] = action.chess

    def judge(self):
        st_len = len(self.state)
        for i in range(st_len):
            lines = []
            lines.append(''.join(self.state[i]))
            lines.append(''.join(j[i] for j in self.state))
            lines.append(''.join(self.state[j][j-i] 
                                 for j in range(i, st_len)))
            lines.append(''.join(self.state[j][i-j]
                                 for j in range(0, i+1)))
            lines.append(''.join(self.state[j-(st_len-1-i)][j] 
                            for j in range(st_len-1-i, st_len)))
            lines.append(''.join(self.state[j][st_len-1-(j-i)]
                           for j in range(i, st_len)))
            for line in lines:
                for c in ('x', 'o'):
                    if c*self.win_cnt in line:
                        return c
        for i in self.state:
            for c in i:
                if c=='-':
                    return '-'
        # 和棋状态
        return '$'
    def __str__(self):
        st_len = len(self.state)
        str_lines = []
        str_lines.append('   '+' '.join(str(i) for i in range(st_len)))
        for i in range(st_len):
            str_lines.append(f'{i:<3}' + ' '.join(self.state[i]))
        return '\n'.join(str_lines)
    
    def getValidActions(self):
        cnt_x = cnt_o = 0
        for row in self.state:
            for c in row:
                if c=='x':cnt_x+=1
                if c=='o':cnt_o+=1
        st_len = len(self.state)
        for i in range(st_len):
            for j in range(st_len):
                if self.state[i][j]=='-':
                    if cnt_o > cnt_x:
                        yield Action((i, j), 'x')
                    if cnt_o < cnt_x:
                        yield Action((i, j), 'o')
                    if cnt_o == cnt_x:
                        if self.machine_first:
                            yield Action((i, j), 'x')
                        else:
                            yield Action((i, j), 'o')
    
    def getJudgeTree(self):
#         print(self)
        judge_result = self.judge()
        self.sub_cases = []
        self.possibility = {'x':0.0, 'o':0.0}
        if judge_result=='o':
            self.possibility = {'x':0.0, 'o':1.0}
        elif judge_result=='x':
            self.possibility = {'x':1.0, 'o':0.0}
        elif judge_result=='$':
            self.possibility = {'x':0.5, 'o':0.5}
        else:
            for action in self.getValidActions():
                sub_c = TicTacToeBoard(self.state, action, self.win_cnt,
                                      self.machine_first)
                sub_c.getJudgeTree()
                self.sub_cases.append(sub_c)
            for sub_c in self.sub_cases:
                for c in ('x', 'o'):
                    self.possibility[c] += sub_c.possibility[c]
            sum_p = sum(self.possibility.values())
            for c in ('x', 'o'):
                self.possibility[c] /= sum_p
        return self

def tic_tac_toe(board_width = 3, win_cnt=3, machine_first=True):
    def judgeFinished(board):
        judge_result = board.judge()
        if judge_result=='-':
            return None
        if judge_result=='x':
            return 'Machine'
        if judge_result=='o':
            return 'Man'
        if judge_result=='$':
            return 'Nobody'
            
    print('--- Machine chess:x   Man Chess:o ---\n')
    board_array = [['-']*board_width for i in range(board_width)]
    board = None
    if machine_first:
        board = TicTacToeBoard(board_array, win_cnt=win_cnt)
        board.getJudgeTree()
        board = max(board.sub_cases, 
                    key=lambda case:case.possibility['x'])
        print(board)
    winner = None
    while True:
        # 人落子
        man_input = input("Input (row, column) :")
        if man_input == 'bye':
            break
        man_input = re.sub(r'\(|\)', '', man_input)
        row, column = (int(i) for i in man_input.split(','))
        if board==None:
            board = TicTacToeBoard(board_array,Action((row, column), 'o'),
                                   win_cnt=win_cnt,machine_first=False)
            board.getJudgeTree()
        else:
            try:
                board.executeAction(Action((row, column), 'o'))
            except PutChessError:
                print("Inputed pos invalid, put input pos again.")
                continue
            for sub_case in board.sub_cases:
                if sub_case.state==board.state:
                    board = sub_case
                    break
#         print(board)
        # 判断人是否赢了
        winner = judgeFinished(board)
        if winner:
            print(board)
            break
        # 机器落子
        board = max(board.sub_cases, 
                    key=lambda case:case.possibility['x'])
        
        winner = judgeFinished(board)
        print(board)
        if winner:
            break
        
    if winner=='Man':
        print('Congratulations, you win the game!')
    elif winner=='Machine':
        print("You're failure, do better next time!")
    else:
        print("This game is draw")

# 将参数 win_cnt 设为5，并扩大 board_width，即变为五子棋对抗游戏
tic_tac_toe(3, 3, False)
```
游戏效果：
```
--- Machine chess:x   Man Chess:o ---

Input (row, column) :1,1
   0 1 2
0  x - -
1  - o -
2  - - -
Input (row, column) :0,2
   0 1 2
0  x - o
1  - o -
2  x - -
Input (row, column) :0,1
   0 1 2
0  x o o
1  x o -
2  x - -
You're failure, do better next time!
```

## 性能分析

如果选择计算机先下，那么 `board_witdh=2, win_cnt=2` 时需要构造 41 个`TicTacToeBoard`对象，41 = 1 + 4 + 4×3 + 4×3×2，在 `board_witdh=2, win_cnt=2` 时需要构造 563991 个对象。空间复杂度为 $O(board\_width^2!)$。所以，当要构造 8×8 的棋盘下五子棋时，需要构建的节点数约为
$64!=$126886932185884164103433389335161480802865516174545192198801894375214704230400000000000000

计算机应该是存不过来了，所以用构建博弈树的方法智能化五子棋游戏不太现实，时间复杂度更高。

