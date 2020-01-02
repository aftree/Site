nano文本编辑器
        #nano test.txt      # test.txt存在就打开，不存在就新建  
       
        GNU nano 2.3.1                        File: test.txt                                 
	 |  <==这个是游标所在处
	
	                 [ New File ]
	^G Get Help   ^O WriteOut   ^R Read File  ^Y Prev Page  ^K Cut Text   ^C Cur Pos
	^X Exit       ^J Justify    ^W Where Is   ^V Next Page  ^U UnCut Te   ^T To Spell
	
	# 上面两行是指令说明列，其中^代表的是[ctrl]的意思
	如上图所示，你可以看到第一行反白的部分，那仅是在宣告nano的版本与档名(File: text.txt)而已。 之后你会看到最底下的三行，分别是档案的状态(New File)与两行指令说明列。指令说明列反白的部分就是组合键， 接的则是该组合键的功能。那个指数符号(^)代表的是键盘的[Ctrl]按键啦！底下先来说说比较重要的几个组合按键：
	•	[ctrl]-G：取得联机帮助(help)，很有用的！
	•	[ctrl]-X：离开nano软件，若有修改过档案会提示是否需要储存喔！
	•	[ctrl]-O：储存档案，若你有权限的话就能够储存档案了；
	•	[ctrl]-R：从其他档案读入资料，可以将某个档案的内容贴在本档案中；
	•	[ctrl]-W：搜寻字符串，这个也是很有帮助的指令喔！
	•	[ctrl]-C：说明目前光标所在处的行数与列数等信息；
	•	[ctrl]-_：可以直接输入行号，让光标快速移动到该行；
	•	[alt]-Y：校正语法功能开启或关闭(单击开、再单击关)
	•	[alt]-M：可以支持鼠标来移动光标的功能
	比较常见的功能是这些，如果你想要取得更完整的说明，可以在nano的画面中按下[ctrl]-G或者是[F1]按键， 就能够显示出完整的nano内指令说明了。好了，请你在上述的画面中随便输入许多字， 输入完毕之后就储存后离开，如下所示：
	GNU nano 2.3.1                        File: text.txt                                
	Type some words in this nano editor program.
	You can use [ctrl] plus some keywords to go to some functions.
	Hello every one.
	Bye bye.
	<==这个是由标所在处
	[ New File ]                                      
	^G Get Help   ^O WriteOut   ^R Read File  ^Y Prev Page  ^K Cut Text   ^C Cur Pos
	^X Exit       ^J Justify    ^W Where Is   ^V Next Page  ^U UnCut Te   ^T To Spell
	此时按下[ctrl]-X会出现类似下面的画面：

	  GNU nano 2.3.1                        File: text.txt                                
	Type some words in this nano editor program.
	You can use [ctrl] plus some keywords to go to some functions.
	Hello every one.
	Bye bye.

	Save modified buffer (ANSWERING "No" WILL DESTROY CHANGES) ? ▋
	 Y Yes
	 N No           ^C Cancel
	如果不要储存资料只想要离开，可以按下N即可离开。如果确实是需要储存的，那么按下Y后，最后三行会出现如下画面：
	File Name to Write: text.txt▋<==可在这里修改档名或直接按[enter]
	^G Get Help          M-D DOS Format       M-A Append           M-B Backup File
	^C Cancel            M-M Mac Format       M-P Prepend
	如果是单纯的想要储存而已，直接按下[enter]即可储存后离开nano程序。，^代表[ctrl]， M是代表 [alt]