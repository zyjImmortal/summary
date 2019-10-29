#### 杨辉三角

返回指定行数的杨辉三角

```python
class Solution:
    def generate(self, numRows):
        """
        :type numRows: int
        :rtype: List[List[int]]
        """
        if numRows == 0:
            return []
        result = [[1]]
        // 
        for i in range(1, numRows):
            result.append(list(map(lambda x, y: x + y, result[-1] + [0], [0] + result[-1])))
        return result

    def generateV2(self, numRows: int) -> List[List[int]]:
        if numRows == 0:
            return []
        if numRows == 1:
            return [[1]]
        result = []
        for i in range(numRows):
            cur = []
            for j in range(i+1):
                if j ==0 or j == i:
                    cur.append(1)
                else:
                    cur.append(result[i-1][j] + result[i-1][j-1])
            result.append(cur)
        return result
```

### 杨辉三角2

给定一个非负索引 k，其中 k ≤ 33，返回杨辉三角的第 k 行

```python
def getRow(self, rowIndex: int) -> List[int]:
    """
    给定一个非负索引 k，其中 k ≤ 33，返回杨辉三角的第 k 行，
    和上面的返回一个杨辉三角的思路是一样的，只不过现在是给出索引了，只返回索引行，
    那么只需记录上一行的数据就可以
    :param rowIndex:
    :return:
    """

    if rowIndex == 0:
        return [1]
    pre = [] # 前一行数据
    for i in range(rowIndex + 1):
        cur = []
        for j in range(i + 1):
            if j == 0 or j == i:
                cur.append(1)
            else:
                cur.append(pre[j] + pre[j - 1])
        if i == rowIndex:
            return cur
        pre = cur

def getRowV2(self, rowIndex: int) -> List[int]:
    if rowIndex == 0:
        return [1]
    result = []
    for i in range(rowIndex + 1):
        cur = []
        for j in range(i + 1):
            if j == 0 or j == i:
                cur.append(1)
            else:
                cur.append(result[i - 1][j] + result[i - 1][j - 1])
        if i == rowIndex:
            return cur
        result.append(cur)

def getRowV3(self, rowIndex):
    """
    第j行的数据由第j-1行的数据得到，而且会比上一行多一个元素
    假设第j-1行为[1,3,3,1]
    现在第j-1头部添加元素0，那j行为[0+1,1+3,3+3,3+1,1]
    :param rowIndex:
    :return:
    """
    r = [1] # 初始行
    for i in range(1, rowIndex + 1): # 因为rowIndex表示的是索引，所以在for循环里需要加一限定循环范围
        r.insert(0, 0) # 在前一行头部插入0
        # 因为i行的数据长度为i+1, 所以j+1不会越界, 并且最后一个1不会被修改.
        # 生成每一行数据
        for j in range(i):
            r[j] = r[j] + r[j + 1]
    return r
```