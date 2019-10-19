### 字符串

- 判断是否是回文字符串

``````python
class Solution:

    def isPalindrome(self, s: str) -> bool:
        '''
        判断是否是回文字符串
        str.isalnum判断一个字符是不是数字或者字符串，如果不是返回false
        '''
        filter_str = filter(str.isalnum, s)
        new_str = ''.join(filter_str)
        reverse_string = new_str.lower()[::-1]
        if s == reverse_string:
            return True
        else:
            return False
``````

