# 在循环中是使用若干个if
if else语句在执行时，若if为真，则else语句不会执行。
但是若写了若干个连续的if，则前一个if若为真，后一个if也会继续判断执行。
若在循环中，只想某一种情况被执行后该次循环结束的话，可以用continue。

比如在下方将前缀表达式转为后缀表达式的代码中，遍历前缀表达式判断当前字符是数字还是符号时，一旦一个if成立，后续的if就不需要再判断了，因此使用continue终止该次循环。

```c++
void infixToSuffix(string& infix, string& suffix) {
	CLinkStack* signStack = CreateCLinkStack();
	//在符号栈中事先push一个'#'，当比较符号优先级时，若符号栈为空，则以当前符号与'#'比较，从而将空栈情况归入signTable中。
	DataSign* emptyStackFlag = (DataSign*)(malloc(sizeof(DataSign)));
	emptyStackFlag->data = '#';
	PushCLinkStack(signStack, (CLinkNode*)emptyStackFlag);


	for (auto it = infix.begin(); it != infix.end(); ++it) {
		if (isdigit(*it)) {
			suffix.push_back(*it);
		}
		else {
			//若符号栈为空，则进栈
			if (IsEmptyCLinkStack(signStack) && ((*it)!=')')) {
				DataSign* sign = (DataSign*)(malloc(sizeof(DataSign)));
				sign->data = *it;
				PushCLinkStack(signStack, (CLinkNode*)sign);
				continue;	//用if else可以避免后续的条件判断，若只用if则需配合continue终止该次循环
			}
			//若符号栈非空，但当前符号为(则进栈
			if (!IsEmptyCLinkStack(signStack) && ((*it) == '(')) {
				DataSign* sign = (DataSign*)(malloc(sizeof(DataSign)));
				sign->data = *it;
				PushCLinkStack(signStack, (CLinkNode*)sign);
				continue;
			}
			//当前符号栈非空，当前符号为)则出栈，将符号依次添加到suffix，直至出现(，但(不添加进suffix
			if (!IsEmptyCLinkStack(signStack) && ((*it) == ')')) {
				char tempSign = ' ';
				while (((DataSign*)(TopCLinkStack(signStack)))->data != '(') {
					tempSign = ((DataSign*)(TopCLinkStack(signStack)))->data;
					suffix += tempSign;
					PopCLinkStack(signStack);	//符号栈栈顶元素添加一个pop一个
				}
				PopCLinkStack(signStack);	//pop ')'
				continue;
			}
			//符号栈非空，当前符号也非括号，则判断当前符号与栈顶符号的优先级：若当前符号优先级大于栈顶元素则直接进栈，否则就将栈顶元素出栈直至栈顶元素优先级低于当前符号
			if (compareSign((*it), ((DataSign*)(TopCLinkStack(signStack)))->data, signTable) == 1) {
				DataSign* sign = (DataSign*)(malloc(sizeof(DataSign)));
				sign->data = *it;
				PushCLinkStack(signStack, (CLinkNode*)sign);
				continue;
			}
			else {
				char tempSign = ' ';
				while (!(compareSign((*it), ((DataSign*)(TopCLinkStack(signStack)))->data, signTable) == 1)) {
					tempSign = ((DataSign*)(TopCLinkStack(signStack)))->data;
					suffix += tempSign;
					PopCLinkStack(signStack);
				}
				DataSign* sign = (DataSign*)(malloc(sizeof(DataSign)));
				sign->data = *it;
				PushCLinkStack(signStack, (CLinkNode*)sign);
				continue;
			}
		}
	}
	//中缀表达式遍历完毕，将符号栈中剩余除'#'以外，所有符号都pop
	while (!IsEmptyCLinkStack(signStack) && ((DataSign*)(TopCLinkStack(signStack)))->data!='#') {
		DataSign* sign = (DataSign*)(TopCLinkStack(signStack));
		suffix += sign->data;
		PopCLinkStack(signStack);
	}
}
```