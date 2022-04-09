## Mysql Workbench学习笔记

###### Set as default schema

在workbench时，要右键数据库->Set as default schema才可以在query页面免去sql前缀：

Example：假如在数据库sys中有一个表叫workbench_test，如果不set as default schema的话，需要把sql改成`select * from sys.workbench_test`，但set as default schema之后，只用`select * from workbench_test`就ok了

###### Foreign key

本质上是一种restriction，会对tuple的删除和修改造成很大的影响。举个栗子：行程卡上面要填写你从哪里来的，通常这时会有一个下拉框框，从中可以选省份和城市。这时候table`行程卡`中的attribute`你来自哪里`(referring)就引用了table`region`中的`地名`(referred)一栏。这就叫做foreign key。当删除或更改`地名`的时候，首先要把引用它的`你来自哪里`全部去除，直到保证它不再受引为止，才可删除或修改。

那么怎么用sql在有外键(foreign key)的情况下增减数据呢？答案是暂时把所有外键disable掉。命令是`set foreign_key_checks=0`，复原则set为1.



