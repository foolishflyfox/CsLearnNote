---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 对抗搜索 - 五子棋游戏对抗

## 博弈树

### 代码实现

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
    
    # 构建博弈树
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

### 性能分析

如果选择计算机先下，那么 `board_witdh=2, win_cnt=2` 时需要构造 41 个`TicTacToeBoard`对象，41 = 1 + 4 + 4×3 + 4×3×2，在 `board_witdh=2, win_cnt=2` 时需要构造 563991 个对象。空间复杂度为 $O(board\_width^2!)$。所以，当要构造 8×8 的棋盘下五子棋时，需要构建的节点数约为
$64!=$126886932185884164103433389335161480802865516174545192198801894375214704230400000000000000

计算机应该是存不过来了，所以用构建博弈树的方法智能化五子棋游戏不太现实，时间复杂度更高。


## 极大极小值

### 代码实现

```python {class="line-numbers"}
import re
import copy
from collections import namedtuple
import time

class PutChessError(Exception):
    pass

Action = namedtuple('Action', 'pos chess')

class TicTacToeBoard:
    max_score = 1000
    min_score = 0
    mid_score = (max_score+min_score)//2
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
        
    def getAllLines(self):
        st_len = len(self.state)
        for i in range(st_len):
            yield ''.join(self.state[i])
            yield ''.join(j[i] for j in self.state)
            yield ''.join(self.state[j][j-i] 
                            for j in range(i, st_len))
            yield ''.join(self.state[j][i-j]
                            for j in range(0, i+1))
            yield ''.join(self.state[j-(st_len-1-i)][j] 
                            for j in range(st_len-1-i, st_len))
            yield ''.join(self.state[j][st_len-1-(j-i)]
                            for j in range(i, st_len))
        
    def minJudge(self, depth):
        self.score = self.mid_score
        for line in self.getAllLines():
            if 'o'*self.win_cnt in line:
                self.score = self.min_score
                break
            if depth==0:
                if 'o'*self.(win_cnt-1)+'-' in line:
                    self.score -= 1
                if '-'+'o'*self.(win_cnt-1) in line:
                    self.score -= 1
        if self.score<=self.min_score or depth==0:
            return
        self.score = self.max_score
        self.sub_cases = []
        for action in self.getValidActions():
            sub_case = TicTacToeBoard(self.state, action,
                        self.win_cnt, self.machine_first)
            sub_case.maxJudge(depth-1)
            self.sub_cases.append(sub_case)
        for case in self.sub_cases:
            if case.score < self.score:
                self.score = case.score
        self.sub_cases.clear()
                
    def maxJudge(self, depth):
        self.score = self.mid_score
        for line in self.getAllLines():
            if 'x'*self.win_cnt in line:
                self.score = self.max_score
                break
            if depth==0:
                if 'x'*(self.win_cnt-1)+'-' in line:
                    self.score += 1
                if '-'+'x'*(self.win_cnt-1) in line:
                    self.score += 1
        if self.score>=self.max_score or depth==0:
            return
        self.score = self.min_score
        self.sub_cases = []
        for action in self.getValidActions():
            sub_case = TicTacToeBoard(self.state, action,
                        self.win_cnt, self.machine_first)
            sub_case.minJudge(depth-1)
            self.sub_cases.append(sub_case)
        for case in self.sub_cases:
            if case.score > self.score:
                self.score = case.score
            if case.sub_cases:
                case.sub_cases.clear()
                
    def doMinMaxAction(self, depth):
        self.maxJudge(depth)
        for sub_case in self.sub_cases:
            if self.score == sub_case.score:
                self.state = sub_case.state
                break

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

def tic_tac_toe(board_width = 3, win_cnt=3, machine_first=True,
               calc_depth=3):
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
        board.doMinMaxAction(calc_depth)
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
        else:
            try:
                board.executeAction(Action((row, column), 'o'))
            except PutChessError:
                print("Inputed pos invalid, put input pos again.")
                continue
#         print(board)
        # 判断人是否赢了
        winner = judgeFinished(board)
        if winner:
            print(board)
            break
        # 机器落子
        t0 = time.time()
        board.doMinMaxAction(calc_depth)
        t1 = time.time()
        print(f'Machine Thinking : {t1-t0} s')
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
tic_tac_toe(5, 5, True, 4)
```
运行效果：
```
--- Machine chess:x   Man Chess:o ---
   0 1 2 3 4
0  x - - - -
1  - - - - -
2  - - - - -
3  - - - - -
4  - - - - -
Input (row, column) :2,2
Machine Thinking : 15.443007946014404 s
   0 1 2 3 4
0  x x - - -
1  - - - - -
2  - - o - -
3  - - - - -
4  - - - - -
Input (row, column) :0,2
Machine Thinking : 10.637631893157959 s
   0 1 2 3 4
0  x x o x -
1  - - - - -
2  - - o - -
3  - - - - -
4  - - - - -
Input (row, column) :1,2
Machine Thinking : 6.827928066253662 s
   0 1 2 3 4
0  x x o x x
1  - - o - -
2  - - o - -
3  - - - - -
4  - - - - -
Input (row, column) :3,2
Machine Thinking : 4.019567012786865 s
   0 1 2 3 4
0  x x o x x
1  - - o - -
2  - - o - -
3  - - o - -
4  - - x - -
Input (row, column) :bye
This game is draw
```

### 性能分析

在 3×3 的棋盘上，如果有9个空位，需要预测3步，则需要展开节点数为 $1+9 + 9\times8 + 9\times8\times7=586$，如果是 8×8 的棋盘，如果有64 个空位，需要预测3步，则需要展开节点数为 $1+64+64\times63+64\times63\times62=254081$。n×n 的棋盘，需要预测 k 步，一步决策的 时间复杂度 大约为 $O(n^{2k})$ 次，也是指数级的增长，从实验中的 `Machine Thinking` 的时间可以看出，每次规划都花费了大量的时间，这样的开销完全不适用，不过该算法仍然可以作为对博弈进行数学分析和设计实用算法的基础。因为在程序中一次性生成所有的后继，并且在判断后及时清理，因此该算法的空间复杂度为 $O(n^2k)$，空间复杂度并不大。

## $\alpha-\beta$ 剪枝

极小极大值搜索的问题是必须检查的游戏状态数目随着博弈的进行呈现指数级增长，虽然指数增长无法消除，不过我们还是可以使用剪枝的办法有效地将其减半。

$\alpha-\beta$ 剪枝的伪代码为：
```javascript
function ALPHA_BETA_Search(state) returns an action
    v <- MAX.VALUE(state, -inf, +inf)
    return the action in Actions(state) with value v

function MAX_VALUE(state, alpha, beta) returns a utility value
    if TERMINAL_TEST(state) then return UTILITY(state)
    v = -inf
    for each a in ACTIONS(state) do
        v <- MAX(v, MIN_VALUE(RESULT(s, a), alpha, beta))
        if v >= beta then return v
        alpha <- MAX(alpha, v)
    return v

function MIN_VALUE(state, alpha, beta) returns a utility value
    if TERMINAL_TEST(state) then return UTILITY(state)
    v <- +inf
    for each a in ACTIONS(RESULT(s, a), alpha, beta)
        v <- MIN(v, MAX_VALUE(RESULT(s, a), alpha, beta))
        if v <= alpha then return v
        beta <- MIN(beta, v)
    return v
```

代码主要修改了原来的 `minJudge`、`minJudge` 和 `doMinMaxAction` 3 个函数：
```python {class="line-numbers"}
import re
import copy
from collections import namedtuple
import time

class PutChessError(Exception):
    pass

Action = namedtuple('Action', 'pos chess')

class TicTacToeBoard:
    max_score = 1000
    min_score = 0
    mid_score = (max_score+min_score)//2
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
        
    def getAllLines(self):
        st_len = len(self.state)
        for i in range(st_len):
            yield ''.join(self.state[i])
            yield ''.join(j[i] for j in self.state)
            yield ''.join(self.state[j][j-i] 
                            for j in range(i, st_len))
            yield ''.join(self.state[j][i-j]
                            for j in range(0, i+1))
            yield ''.join(self.state[j-(st_len-1-i)][j] 
                            for j in range(st_len-1-i, st_len))
            yield ''.join(self.state[j][st_len-1-(j-i)]
                            for j in range(i, st_len))
        
    def minJudge(self, depth, alpha, beta):
        self.score = self.mid_score
        for line in self.getAllLines():
            if 'o'*self.win_cnt in line:
                self.score = self.min_score
                break
            if depth==0:
                if 'o'*(self.win_cnt-1)+'-' in line:
                    self.score -= 1
                if '-'+'o'*(self.win_cnt-1) in line:
                    self.score -= 1
        if self.score<=self.min_score or depth==0:
            return
        self.score = self.max_score
        self.sub_cases = []
        for action in self.getValidActions():
            sub_case = TicTacToeBoard(self.state, action,
                        self.win_cnt, self.machine_first)
            sub_case.maxJudge(depth-1, alpha, beta)
            self.score = min(self.score, sub_case.score)
            self.sub_cases.append(sub_case)
            if self.score <= alpha:
                break
            beta = min(self.score, beta)
        self.sub_cases.clear()
                
    def maxJudge(self, depth, alpha, beta):
        self.score = self.mid_score
        for line in self.getAllLines():
            if 'x'*self.win_cnt in line:
                self.score = self.max_score
                break
            if depth==0:
                if 'x'*(self.win_cnt-1)+'-' in line:
                    self.score += 1
                if '-'+'x'*(self.win_cnt-1) in line:
                    self.score += 1
        if self.score>=self.max_score or depth==0:
            return
        self.score = self.min_score
        self.sub_cases = []
        for action in self.getValidActions():
            sub_case = TicTacToeBoard(self.state, action,
                        self.win_cnt, self.machine_first)
            sub_case.minJudge(depth-1, alpha, beta)
            self.score = max(self.score, sub_case.score)
            if self.score >= beta:
                break
            alpha = max(self.score, alpha)
            self.sub_cases.append(sub_case)
        for case in self.sub_cases:
            if case.sub_cases:
                case.sub_cases.clear()
                
    def doMinMaxAction(self, depth):
        self.maxJudge(depth, self.min_score, self.max_score)
        for sub_case in self.sub_cases:
            if self.score == sub_case.score:
                self.state = sub_case.state
                break

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

def tic_tac_toe(board_width = 3, win_cnt=3, machine_first=True,
               calc_depth=3):
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
        board.doMinMaxAction(calc_depth)
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
        else:
            try:
                board.executeAction(Action((row, column), 'o'))
            except PutChessError:
                print("Inputed pos invalid, put input pos again.")
                continue
#         print(board)
        # 判断人是否赢了
        winner = judgeFinished(board)
        if winner:
            print(board)
            break
        # 机器落子
        t0 = time.time()
        board.doMinMaxAction(calc_depth)
        t1 = time.time()
        print(f'Machine Thinking : {t1-t0} s')
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
tic_tac_toe(5, 5, True, 4)
```

与普通的极大极小值搜索进行系统的实验进行对比实验：
```
--- Machine chess:x   Man Chess:o ---
   0 1 2 3 4
0  x - - - -
1  - - - - -
2  - - - - -
3  - - - - -
4  - - - - -
Input (row, column) :2,2
Machine Thinking : 0.10620284080505371 s
   0 1 2 3 4
0  x x - - -
1  - - - - -
2  - - o - -
3  - - - - -
4  - - - - -
Input (row, column) :0,2
Machine Thinking : 0.0905296802520752 s
   0 1 2 3 4
0  x x o x -
1  - - - - -
2  - - o - -
3  - - - - -
4  - - - - -
Input (row, column) :1,2
Machine Thinking : 0.07415509223937988 s
   0 1 2 3 4
0  x x o x x
1  - - o - -
2  - - o - -
3  - - - - -
4  - - - - -
Input (row, column) :3,2
Machine Thinking : 0.3078148365020752 s
   0 1 2 3 4
0  x x o x x
1  - - o - -
2  - - o - -
3  - - o - -
4  - - x - -
Input (row, column) :bye
This game is draw
```
可以看出，结果运行结果比之前的块了10倍左右，效果非常明显。

## 孤立点剪枝+更新启发式函数

前面的决策还是很不明智，比如明明不能连成5子，但是机器还在那个方向上放子。我们通过更新 `maxJudge` 和 `minJudge` 的启发式函数时决策更加智能。

因为代码较长，只贴出 `maxJudge` 和 `minJudge` 的函数，替换到上面的代码中就可进行试验：
```python {class="line-numbers"}
### import re
### import re
### import re
import copy
from collections import namedtuple
import time

class PutChessError(Exception):
    pass

Action = namedtuple('Action', 'pos chess')

class TicTacToeBoard:
    max_score = 1000
    min_score = 0
    mid_score = (max_score+min_score)//2
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
        
    def getAllLines(self):
        st_len = len(self.state)
        for i in range(st_len):
            yield ''.join(self.state[i])
            yield ''.join(j[i] for j in self.state)
            yield ''.join(self.state[j][j-i] 
                            for j in range(i, st_len))
            yield ''.join(self.state[j][i-j]
                            for j in range(0, i+1))
            yield ''.join(self.state[j-(st_len-1-i)][j] 
                            for j in range(st_len-1-i, st_len))
            yield ''.join(self.state[j][st_len-1-(j-i)]
                            for j in range(i, st_len))
            
    def heuristicFunc(self, line):
        for i in range(self.win_cnt//2, self.win_cnt):
            if 'o'*i+'-' in line:
                self.score -= i
            if '-'+'o'*i in line:
                self.score -= i
            if 'x'*i+'-' in line:
                self.score += i
            if '-'+'x'*i in line:
                self.score += i
                
    def getChessCnt(self):
        cnt = 0
        for row in self.state:
            for c in row:
                if c!='-':
                    cnt += 1
        return cnt
    
    def isIsolateOpt(self, action):
        moves = [(-1,0),(1,0),(0,1),(0,-1),
                (-1,-1),(1,1),(1,-1),(-1,1)]
        opt_pos = [(action[0][0]+mov[0], action[0][1]+mov[1]) 
                   for mov in moves]
        st_len = len(self.state)
        for pos in opt_pos:
            r, c = pos
            if (r<0 or c<0 or r>=st_len or c>=st_len):
                continue
            if self.state[r][c]!='-':
                return False
        return True
            
    
    def minJudge(self, depth, alpha, beta):
        self.score = self.mid_score
        for line in self.getAllLines():
            if 'x'*self.win_cnt in line:
                self.score = self.max_score
                break
            if depth==0:
                self.heuristicFunc(line)
        if self.score>=self.max_score or depth==0:
            return
        self.score = self.max_score
        self.sub_cases = []
        cur_chess_cnt = self.getChessCnt()
        for action in self.getValidActions():
            if cur_chess_cnt > 1 and self.isIsolateOpt(action):
                continue
            sub_case = TicTacToeBoard(self.state, action,
                        self.win_cnt, self.machine_first)
            sub_case.maxJudge(depth-1, alpha, beta)
            self.score = min(self.score, sub_case.score)
            self.sub_cases.append(sub_case)
            if self.score <= alpha:
                break
            beta = min(self.score, beta)
        self.sub_cases.clear()
                
    def maxJudge(self, depth, alpha, beta):
        self.score = self.mid_score
        for line in self.getAllLines():
            if ('o'*self.win_cnt in line or 
            '-'+'o'*(self.win_cnt-1)+'-' in line):
                self.score = self.min_score
                break
            if depth==0:
                self.heuristicFunc(line)
        if self.score<=self.min_score or depth==0:
            return
        self.score = self.min_score
        self.sub_cases = []
        cur_chess_cnt = self.getChessCnt()
        for action in self.getValidActions():
            if cur_chess_cnt > 1 and self.isIsolateOpt(action):
                continue
            sub_case = TicTacToeBoard(self.state, action,
                        self.win_cnt, self.machine_first)
            sub_case.minJudge(depth-1, alpha, beta)
            self.score = max(self.score, sub_case.score)
            self.sub_cases.append(sub_case)
            if self.score >= beta:
                break
            alpha = max(self.score, alpha)
            
        for case in self.sub_cases:
            if case.sub_cases:
                case.sub_cases.clear()
                
    def doMinMaxAction(self, depth):
        self.maxJudge(depth, self.min_score, self.max_score)
        for sub_case in self.sub_cases:
            if self.score == sub_case.score:
                self.state = sub_case.state
                break

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

def tic_tac_toe(board_width = 3, win_cnt=3, machine_first=True,
               calc_depth=3):
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
        board.executeAction(Action((board_width//2, board_width//2),'x'))
#         board.doMinMaxAction(calc_depth)
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
        else:
            try:
                board.executeAction(Action((row, column), 'o'))
            except PutChessError:
                print("Inputed pos invalid, put input pos again.")
                continue
#         print(board)
        # 判断人是否赢了
        winner = judgeFinished(board)
        if winner:
            print(board)
            break
        # 机器落子
        t0 = time.time()
        board.doMinMaxAction(calc_depth)
        t1 = time.time()
        print(f'Machine Thinking : {t1-t0} s')
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
tic_tac_toe(8, 5, True, 4)
```
大家可以代码写入两个文件，通过 vscode 或 beyond compare 等对比根据进行对比查看。上述代码不仅优化了启发式函数，而且加入了孤立点剪枝，进一步加速了运算速度。

试验效果为：

```
--- Machine chess:x   Man Chess:o ---

   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - - - - -
2  - - - - - - - -
3  - - - - - - - -
4  - - - - x - - -
5  - - - - - - - -
6  - - - - - - - -
7  - - - - - - - -
Input (row, column) :3,3
Machine Thinking : 1.0809872150421143 s
   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - - - - -
2  - - - - - - - -
3  - - - o x - - -
4  - - - - x - - -
5  - - - - - - - -
6  - - - - - - - -
7  - - - - - - - -
Input (row, column) :2,4
Machine Thinking : 2.6504628658294678 s
   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - - - - -
2  - - - - o - - -
3  - - - o x - - -
4  - - x - x - - -
5  - - - - - - - -
6  - - - - - - - -
7  - - - - - - - -
Input (row, column) :4,3
Machine Thinking : 2.451833963394165 s
   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - - - - -
2  - - - x o - - -
3  - - - o x - - -
4  - - x o x - - -
5  - - - - - - - -
6  - - - - - - - -
7  - - - - - - - -
Input (row, column) :4,5
Machine Thinking : 5.607155084609985 s
   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - - - - -
2  - - - x o - - -
3  - - x o x - - -
4  - - x o x o - -
5  - - - - - - - -
6  - - - - - - - -
7  - - - - - - - -
Input (row, column) :5,3
Machine Thinking : 8.986511945724487 s
   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - - - - -
2  - - - x o - - -
3  - - x o x - - -
4  - x x o x o - -
5  - - - o - - - -
6  - - - - - - - -
7  - - - - - - - -
Input (row, column) :5,0
Machine Thinking : 7.310142993927002 s
   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - - - - -
2  - - - x o - - -
3  - - x o x - - -
4  - x x o x o - -
5  o - x o - - - -
6  - - - - - - - -
7  - - - - - - - -
Input (row, column) :6,2
Machine Thinking : 4.588731050491333 s
   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - - - - -
2  - - - x o - - -
3  - - x o x - - -
4  - x x o x o - -
5  o - x o - - - -
6  - - o x - - - -
7  - - - - - - - -
Input (row, column) :3,0
Machine Thinking : 5.667893886566162 s
   0 1 2 3 4 5 6 7
0  - - - - - - - -
1  - - - - x - - -
2  - - - x o - - -
3  o - x o x - - -
4  - x x o x o - -
5  o - x o - - - -
6  - - o x - - - -
7  - - - - - - - -
Input (row, column) :0,5
Machine Thinking : 9.12896203994751 s
   0 1 2 3 4 5 6 7
0  - - - - - o - -
1  - - x - x - - -
2  - - - x o - - -
3  o - x o x - - -
4  - x x o x o - -
5  o - x o - - - -
6  - - o x - - - -
7  - - - - - - - -
Input (row, column) :2,2
Machine Thinking : 7.826042890548706 s
   0 1 2 3 4 5 6 7
0  - - - - - o - -
1  - - x x x - - -
2  - - o x o - - -
3  o - x o x - - -
4  - x x o x o - -
5  o - x o - - - -
6  - - o x - - - -
7  - - - - - - - -
Input (row, column) :1,5
Machine Thinking : 4.286202907562256 s
   0 1 2 3 4 5 6 7
0  - - - - - o - -
1  - x x x x o - -
2  - - o x o - - -
3  o - x o x - - -
4  - x x o x o - -
5  o - x o - - - -
6  - - o x - - - -
7  - - - - - - - -
Input (row, column) :1,0
Machine Thinking : 5.429880142211914 s
   0 1 2 3 4 5 6 7
0  - - - - - o - -
1  o x x x x o - -
2  x - o x o - - -
3  o - x o x - - -
4  - x x o x o - -
5  o - x o - - - -
6  - - o x - - - -
7  - - - - - - - -
Input (row, column) :bye
This game is draw
```
哈哈，终于有种和正常人下棋的感觉了，而且感觉棋力和我差不多。看起来需要后面的增强学习的方式进行训练后，才能超越代码编写者的下棋水平。

## 总结

### 运算速度

- 博弈树：一次性生成所有情况，并按照获胜的概率选择行动，缺点是空间复杂度和时间复杂度都非常大，不实用。
<br>
- 极大极小搜索：模拟人的行为，只需要估计当前情况下的接下来几步，时间复杂度和空间复杂度都比博弈树要小一些，不过还是不实用。
<br>
- $\alpha-\beta$ 剪枝，降低时间复杂度
<br>
- 孤立点剪枝，降低时间复杂度

### 决策行为

通过优化启发式函数来获得更好的行为。
