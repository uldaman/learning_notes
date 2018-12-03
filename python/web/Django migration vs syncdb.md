# 一、migrate 概述

1.7 版本之前, Django 只支持新增模型到数据库中, 通过 syncdb (migrate 的前身) 命令变更或者删除现存的模型是不可能的.

1.7 版本之后, Django 引入了迁移机制:

```
syncdb is deprecated because of the migration system.

Now you can log your changes using makemigrations. This transforms your model changes into python code to make them deployable to another databases.

After you created the migrations you have to apply them: migrate.

So instead of using syncdb you should use makemigrations and then migrate.
```

> 1.9 版本之后, 彻底移除了 syncdb 命令.

迁移机制引入两条命令:

- makemigrations, 根据模型的变更创建新的迁移脚本
- migrate, 使迁移生效, 以及未生效时报告它们的状态

你应该把迁移作为数据库架构的版本**控制系统**.<br>
`makemigrations` 负责包装模型变更到独立的迁移文件中, 类似于提交代码<br>
`migrate` 负责应用到数据库中

迁移文件存放在每个应用程序的一个 `migrations` 目录中, 被设计成提交, 并作为发布的一部分到代码库中; 你应该在你的开发机器上使用它们, 然后在你的同事的机器上运行相同的迁移脚本, `staging machines`, 最终应用在生产机器环境.

迁移将在相同的数据集上以相同的方法运行并产生一致的结果, 这意味着你将看到在开发阶段、staging 阶段, 在相同的情况下和生产环境上的表现完全一致.

# 二、使用 migrate

migrate 的使用非常简单: 修改 model, 比如增加 field, 然后运行

```python
python manager.py makemigrations
```

你的 model 会被扫描, 然后与之前的版本作比较, 在 app 的 migrations 目录下生成本次迁移文件.

我们建议查看一下该迁移文件, 确保没有问题, 然后运行:

```python
python manager.py migrate
```

migrate 命令会进行比较, 并应用该迁移.
