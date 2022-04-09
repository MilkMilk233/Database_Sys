## Mysql Workbench学习笔记

###### Set as default schema

在workbench时，要右键数据库->Set as default schema才可以在query页面免去sql前缀：

如在数据库sys中有一个表叫workbench_test，如果不set as default schema的话，需要把sql改成`select * from sys.workbench_test`，但set as default schema之后，只用`select * from workbench_test`就ok了



