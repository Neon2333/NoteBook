# 1. 简介

字符串匹配算法——快速地从文本字符串txt中匹配到模式串pat，返回pat在txt中首次出现的位置。通过记录比较时的信息，避免在主串中不必要的比较过程，降低暴力匹配算法的时间复杂度。

# 2.流程
step1.	对模式串pat进行处理，计算pat每个字符对应的要跳转的下标。计算prefixTable。

step2. 对求得到的prefixTable进行处理，舍弃最后一位，将0添加到头。

step3. 执行KMP主流程。

# 3.代码

```c++
Author:
Date:
Description:	KMP算法，在字符串txt中匹配模式串pat。
Ver:
***/

#include<iostream>
#include<string>	

//对模式串pat进行预处理，计算其prefix表
//prefix表表示当在pat的某个字符处无法匹配时，跳转到表中该字符对应的位置
int* getPrefixTable(std::string& pat) {
	int patLen = pat.length();	//prefix表长与pat长度相同，每个字符都有对应值
	int* prefixTable = (int*)(malloc(sizeof(int)*patLen));
	prefixTable[0] = 0;	//prefix[0]暂且先置0
	int i = 1;
	int len = 0;	//对于当前i来说，左侧的子串中，相等前缀、后缀的长度最大为len,暂存pat[index-1]处                     //的len，初始值PrefixTable[0]=0
	while (i < patLen) {
		if (pat.at(i) == pat.at(len)) {	//pat[i]与pat(prefixTalbe[i-1])比较，若相等，则
            							//表明带上i处的字符前缀和后缀仍然相等，因此len应增加1.
			len++;
			prefixTable[i] = len;	//得到i处的prefixTable值
			i++;
		}
		else {
			if (len > 0) {
				len = prefixTable[len - 1];
				//prefixTable[i] = len;
			}
			//若len=0，对应元素又不相等，则对应prefixTable的元素为0
			else {
				prefixTable[i] = 0;
				i++;
			}
		}
	}
	return prefixTable;
}

//对所得的prefixTable进行处理，舍弃最后一位，在头部添加-1。
void movePrefixTable(int* prefixTable,int prefixTableLen) {
	for (int i = prefixTableLen - 1; i > 0; i--) {
		prefixTable[i] = prefixTable[i - 1];
	}
	prefixTable[0] = -1;
}

//KMP算法主流程
int main() {
	std::string txt = "aaacaaabaaab";	//ababababcabaab
	std::string pat = "aaab";	//ababcabaa
	int* prefixTable = getPrefixTable(pat);
	int patLen = pat.length();
	int txtLen = txt.length();
	//若主串长度小于模式串长度，则返回error
    if (txtLen < patLen) {
		std::cout << "error:txt's length < pat's length.." << std::endl;
	}
	
  
	for (int i = 0; i < patLen; i++) {
		std::cout << *(prefixTable + i) << std::endl;
	}

	movePrefixTable(prefixTable, patLen);

    //KMP主流程
	int i = 0, j = 0;
	while (i < txtLen) {
		if (j == patLen - 1 && txt.at(i) == pat.at(j)) {
			std::cout << "found the pat,and the pos is:" << i - j << std::endl;
			//i++;
			j=prefixTable[j];
		}
		if (txt.at(i) == pat.at(j)) {
			i++;
			j++;
		}
		else {
			j = prefixTable[j];
			if (j == -1) {
				j=0;
				i++;
			}
		}
		std::cout << i << std::endl;
	}
	free(prefixTable);
	return 0;
}
```

# 4.原理分析

* 为什么要引入前缀、后缀的概念，并计算模式串每个j对应的最大相等前缀、后缀长度？

* 在处理P[j]!=P[len]时，为什么要让len=prefix_table[len-1]？即不断让j回溯？

* 当len=0，j=-1时，已经无处可以回溯时，让j=0,i++，重新用模式串的第一位与主串的下一位进行比较。

  