# 笔记《Linus Torvalds rules for using Git》

长期使用git，但是都比较随意。最近阅读自己的项目时，发现整个history非常混乱，，使得我自己都无法很好的理解当时发生了什么。于是便阅读了Linus Torvalds关于git使用的一些建议。



"I want clean history, but that really means (a) clean and (b) history. "

这句话很有意思，意思是要保持clean和history之间的balance。



### Clean history

+ 使用 ‘git rebase’ 可以有效的保持历史记录的整洁，但是在某些场景下不应该使用‘git rebase’。例如在分支中做了一些特定的测试，如果使用rebase，那么测试所基于的code就发生了变化，这些测试就可能不再准确。
+ 不要rebase别人的history。
+ 一旦你发布了代码，那么这部分history就不再是你私有的了。



### Merging too much vs too little

+ 频繁的merge会让history显得很混乱，无法再‘真实’的看到两个独立的特性，且无法再pull单独的特性。
+ 不要在随意的节点执行merge、pull，尽量只在需要合并的时候才合并。



**全文的大意就是要保持 ‘clean’ 以及 ‘history’**



小细节：Linus的原文虽然是邮件写的，但却通篇使用markdown语法。



git rebase用法：

http://gitbook.liuhui998.com/4_2.html