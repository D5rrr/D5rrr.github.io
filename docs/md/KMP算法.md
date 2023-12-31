# KMP算法

## next数组

* next[i]表示字符串的**第1个字符到第i个字符**的最大相等前缀和后缀（长度小于字符串本身长度）

* next数组从0开始，定义next[0]为-1，next[1]=0

* 模式串p的下标从0开始

* 假设对于模式串的位置j，有next[j]=k，则对于模式串的位置j+1，有两种情况
    1. 若`p[k]==p[j]`，则有`next[j+1]=next[j]+1`
        * 已经直到next[j]=k，即模式串的第一个字符到第j个字符的最大相同前后缀为k，这时p[k]==p[j]，对应着模式串第j+1个字符等于第k+1个字符（相对于上一个新比较的字符），使得最大相同前后缀+1，即有next[j+1]=next[j]+1

    2. 若`p[k]!=p[j]`，则令`k=next[k]`，若`p[k]==p[j]`，则`next[j+1]=k+1`，否则重复此过程
        * 模式串的第j+1个字符不等于第k+1个字符，就不能再上一个子串的最大前后缀加1了

        * 既然不能在上一个子串的基础上加1，那么就试图寻找更短的基础，直到能加上1。试图找更短的基础就是next[.....next[next[k]]]，这里就是**在原来最大相同前后缀的缀里再求最大相同前后缀**，以达到寻找更短基础的目的。

```cpp
void getNext(const string& p,int next[])//常引用节省空间，保证值不变
{

    int len=strlen();
    next[0]=-1;
    int k=-1;//用来比较，初始值-1是为了让next[1]=0
    int j=0;//j用来迭代next数组
    while(j < len - 1)
    {
        if(k==-1||p[j] == p[k])
        {
            ++j,++k;//相加是为了下一次字符比较和赋值给next，因为p的下标从0开始，k++这样k就能表示最大相同前后缀了
            next[j] = k;
        }
        else
        {
            k = next[k];//变换为更小的最大相同前后缀基础
        }
    }
}
```

## KMP

* 返回匹配成功的第一个元素的位置，否则为-1

```cpp
int kmp(const string& s, const string& pattern)
{
    int n = strlen(s);
    int ans = -1;//匹配不到就返回-1
    int i = 0;//文本的索引
    int j = 0;//模式串的索引
    int patternLen = strlen(pattern);

    int next[patternLen] = {0};
    getNext(pattern,next);

    while(i < n)
    {
        if(j == -1 || s[i] == pattern[j])
        {
            ++i;++j;
        }
        else
        {
            j = next[j];//相当于将模式串开头滑动到目前匹配成功的最大相同后缀的位置
        }

        if(j == patternLen)//j扫描到了末尾，意味着匹配成功
        {
            ans = i - patternLen;
            break;
        }
    }
    return ans;
}
```
