---
title: N皇后问题
date: 2017-12-02 18:11:23
tags: Algorithm
---

算法练习有两个好处，一个是实际开发中可能需要用到，另一个则是保持着一种编程状态。遥想在大一时还没有学过[回溯法](https://en.wikipedia.org/wiki/Backtracking)的时候看到[八皇后问题](https://en.wikipedia.org/wiki/Eight_queens_puzzle)，在不借鉴任何书籍的情况下我找到了答案，而这个答案恰巧就是回溯法。

那么算法开篇的第一题就写八皇后吧，dfs最关键的地方是如何进行**剪枝**，这里**剪枝**我会用hash算法做判重**优化**，而不是循环往上找，比很多标准答案应该要快不少，以前在oj上练习，现在发现[leetcode](https://www.leetcode.com)很流行，我也在上面找到了[这一题](https://leetcode.com/problems/n-queens/description/)，但我不了解为什么它的*Difficulty*为*Hard*。

<!-- more -->

```c++
//假设最大为30皇后
#define MAXN 30
class Solution {
public:
	int Queens[MAXN][MAXN];
	//列判重
	int ColumnCheck[MAXN];
	//左斜线判重
	int LeftCheck[MAXN + MAXN + 1];
	//右斜线判重
	int RightCheck[MAXN + MAXN + 1];
	int count = 0;
	vector<vector<string>> result;
	void dfs(int CurrentRow, int n,vector<vector<string>> &result)
    {
		if (CurrentRow == n)
		{
			vector<string> v;
			result.push_back(v);
			for (int i = 0; i < n; ++i)
			{
				string str;
				for (int j = 0; j < n; ++j)
				{
					if (Queens[i][j] == 0)
						str += ".";
					else
						str += "Q";
				}
				result[count].push_back(str);
			}
			count++;
		}
		for (int column = 0; column < n; ++column)
		{
			//左右斜线的判重hash算法
			int lefttop = column - CurrentRow;
			int righttop = CurrentRow - (n - column);
			if (ColumnCheck[column] == 0 
				&& LeftCheck[n + lefttop] == 0 
				&& RightCheck[n + righttop] == 0)
			{
				Queens[CurrentRow][column] = 1;
				ColumnCheck[column] = 1;
				LeftCheck[n+lefttop] = 1;
				RightCheck[n+righttop]=1;
				dfs(CurrentRow + 1, n,result);
				ColumnCheck[column] = 0;
				Queens[CurrentRow][column] = 0;
				LeftCheck[n+lefttop] = 0;
				RightCheck[n+righttop]=0;
			}
		}
	}
    vector<vector<string>> solveNQueens(int n) 
    {
		memset(Queens,0,sizeof(Queens));
		memset(Queens, 0, sizeof(Queens));
		memset(ColumnCheck, 0, sizeof(ColumnCheck));
		memset(LeftCheck, 0, sizeof(LeftCheck));
		memset(RightCheck, 0, sizeof(RightCheck));
		count = 0;
		dfs(0, n,result);
		return result;
    }
};
```
![result](https://raw.githubusercontent.com/shenlizhong2644/shenlizhong2644.github.io/master/images/NQueenResult.PNG)

可以看出结果还不错，排在第一梯队:P

### 思考 ###

这是用回溯法求出N皇后的方法，那么我们似乎可以只求出**部分答案**，然后再对其进行**旋转**和**翻转**操作就直接得到另一部分答案了。